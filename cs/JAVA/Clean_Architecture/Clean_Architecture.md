## 클린 아키텍처(Clean Architecture)란?

> "변경에 강하고, 유지보수가 쉬운 소프트웨어를 만들기 위해 계층을 명확하게 분리하는 설계 원칙"
> 
> 
> (로버트 C. 마틴, aka "Uncle Bob"이 제안한 개념입니다.)
> 

---

## 구조를 그림으로 간단히 보면:

```
(가장 핵심부터 바깥으로 감싸는 구조)

[Entities]        --> 핵심 비즈니스 규칙 (도메인 모델)
    ↓
[Use Cases]       --> 애플리케이션 비즈니스 로직 (Service)
    ↓
[Interface Adapters] --> Controller, Repository (I/O 변환)
    ↓
[Frameworks & Drivers] --> DB, Web, UI, External APIs

```

---

## 각각 레이어 설명

| 레이어 | 설명 |
| --- | --- |
| **Entities** | 시스템의 핵심 도메인. (ex: Member, Order 같은 비즈니스 객체) |
| **Use Cases** | 시스템이 해야 할 주요 작업(비즈니스 플로우). (ex: 회원가입 서비스) |
| **Interface Adapters** | 외부 입출력을 내부 모델에 맞게 변환. (ex: Controller, Repository) |
| **Frameworks & Drivers** | 기술에 의존하는 부분. (ex: Spring Boot, JPA, MySQL, Kafka) |

---

## 핵심 철학

> 안쪽 레이어는 바깥 레이어를 몰라야 한다.
> 
> 
> (의존성은 항상 **밖 → 안** 으로만 흐른다.)
> 
- Entity나 Use Case는 Spring, DB, 웹 같은 기술을 전혀 몰라야 함.
- Framework나 DB가 바뀌어도, 핵심 로직은 그대로 살아야 함.

---

## 예를 들면

**DB를 MySQL ➔ MongoDB로 바꾼다?**

- Framework 레이어 (Repository 구현)만 수정하면 끝.
- Core 로직(Entities, Use Cases)은 수정할 필요 없음.

---

## 한 줄 요약

> 클린 아키텍처는 "비즈니스 로직을 기술로부터 독립시키는 구조"를 만든다.
> 

---

## 왜 실무에서 중요할까?

| 이유 | 설명 |
| --- | --- |
| 유지보수성 | 기능 추가/수정할 때 영향 범위 최소화 |
| 테스트 용이성 | 핵심 로직을 외부 시스템 없이 테스트 가능 |
| 기술 교체 용이성 | DB, 프레임워크를 쉽게 갈아끼울 수 있음 |
| 확장성 | 기능별로 레이어를 깔끔히 확장 가능 |

---

## 클린 아키텍처와 JPA, open-in-view: false 관계

- **Service 레이어** 안에서 모든 DB작업 끝낸다 → 비즈니스 로직(Service)이 DB 기술에 직접 묶이지 않는다.
- Controller는 단순 요청/응답 변환만 한다 → 비즈니스 로직과 프레젠테이션(웹)이 분리된다.
- 이렇게 하면 **진짜 클린 아키텍처 스타일**로 개발할 수 있게 된다.

---

## 마지막 요약

| 키워드 | 한 줄 요약 |
| --- | --- |
| 핵심 철학 | 안쪽은 바깥을 몰라야 한다 |
| 의존성 방향 | 밖 ➔ 안 (Framework ➔ UseCase ➔ Entity) |
| 목적 | 변경에 강하고 유지보수 쉬운 시스템 만들기 |

---

---