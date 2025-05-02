## 📚 클린 아키텍처 스타일 예제

### 기본 개념

> "Controller → Service → Repository" 흐름으로, 각자 딱 자기 역할만 수행하게 만든다.
> 

---

## 📦 프로젝트 구조 예시

```
com.example.project
├── domain
│   ├── Member.java            (Entity)
│   └── MemberRepository.java  (Repository Interface)
├── application
│   └── MemberService.java      (Use Case)
├── adapter
│   └── in
│       └── MemberController.java  (HTTP Adapter)
│   └── out
│       └── MemberRepositoryImpl.java (DB Adapter)
└── config
    └── AppConfig.java (Bean 등록)

```

---

## ✨ 각 레이어 코드 샘플

### 1. Entity (Domain Layer)

```java
@Entity
public class Member {
    @Id
    private Long id;
    private String name;
}

```

---

### 2. Repository Interface (Domain Layer)

```java
public interface MemberRepository {
    Member findById(Long id);
}

```

> ✅ 핵심 로직은 Repository 인터페이스만 알고 있음 (JPA 같은 기술은 모름).
> 

---

### 3. Service (Application Layer)

```java
@Service
@Transactional(readOnly = true)
public class MemberService {

    private final MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    public Member getMember(Long id) {
        return memberRepository.findById(id);
    }
}

```

> ✅ Service는 Repository 인터페이스만 호출한다.
> 

---

### 4. Repository 구현체 (Adapter Outbound Layer)

```java
@Repository
public class MemberRepositoryImpl implements MemberRepository {

    private final JpaMemberRepository jpaMemberRepository;

    public MemberRepositoryImpl(JpaMemberRepository jpaMemberRepository) {
        this.jpaMemberRepository = jpaMemberRepository;
    }

    @Override
    public Member findById(Long id) {
        return jpaMemberRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Member not found"));
    }
}

```

**JpaMemberRepository**는 JPA Repository:

```java
public interface JpaMemberRepository extends JpaRepository<Member, Long> {
}

```

> ✅ DB 기술(JPA)은 Adapter(RepositoryImpl) 안에서만 사용.
> 

---

### 5. Controller (Adapter Inbound Layer)

```java
@RestController
@RequestMapping("/members")
public class MemberController {

    private final MemberService memberService;

    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }

    @GetMapping("/{id}")
    public MemberResponse getMember(@PathVariable Long id) {
        Member member = memberService.getMember(id);
        return new MemberResponse(member.getId(), member.getName());
    }
}

```

**DTO (MemberResponse)**

```java
public class MemberResponse {
    private Long id;
    private String name;

    public MemberResponse(Long id, String name) {
        this.id = id;
        this.name = name;
    }
}

```

> ✅ Controller는 DTO로 변환해서 리턴. Entity를 바로 리턴하지 않음.
> 

---

## 🎯 정리 흐름

```
Controller (HTTP 요청 처리)
    ↓
Service (비즈니스 로직 + 트랜잭션 관리)
    ↓
Repository Interface (DB 접근 인터페이스)
    ↓
Repository 구현체 (JPA로 실제 DB 연결)

```

- DB가 바뀌어도(예: MongoDB, Redis로 전환)
    
    ➔ RepositoryImpl만 수정하면 Core 로직은 건들 필요 없음!
    

---

## ✅ 한 줄 요약

> "핵심 로직은 기술을 모르게, 기술은 바깥쪽에 몰아넣어라."
> 

= 이게 바로 클린 아키텍처!

---

## 📢 추가 꿀팁

- 진짜 제대로 하려면 **Domain Layer** 에 **Validation/비즈니스 규칙**도 같이 넣습니다.
- Service에서는 "비즈니스 흐름만" 책임지게 합니다.

---

**추가로**

- "진짜 클린 아키텍처식 Exception Handling 방법"
- "복잡한 비즈니스 규칙을 Entity 안에 녹이는 방법"