## 1. 클린 아키텍처에서 Exception Handling

### 기본 철학

- **비즈니스 예외** (예: 회원이 존재하지 않는다)는 **Use Case(Service)** 레이어에서 발생
- **기술 예외** (예: DB 커넥션 오류)는 **Adapter** 레이어에서 처리
- Controller는 **Exception을 받아서 적절한 HTTP Response로 변환만** 한다

---

### 코드 예제

**1. 커스텀 비즈니스 Exception 만들기**

```java
public class MemberNotFoundException extends RuntimeException {
    public MemberNotFoundException(Long id) {
        super("Member not found with id: " + id);
    }
}

```

---

**2. Service 레이어에서 Exception 던지기**

```java
@Transactional(readOnly = true)
public Member getMember(Long id) {
    return memberRepository.findById(id)
        .orElseThrow(() -> new MemberNotFoundException(id));
}

```

> 비즈니스 로직은 "회원 없으면 예외"라는 규칙만 신경씀.
> 

---

**3. ControllerAdvice로 Exception 전역 처리**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MemberNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleMemberNotFound(MemberNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("MEMBER_NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
}

```

**ErrorResponse DTO**

```java
public class ErrorResponse {
    private String code;
    private String message;

    public ErrorResponse(String code, String message) {
        this.code = code;
        this.message = message;
    }
}

```

> ✅ Controller는 예외를 잡아서 "적절한 에러 메시지와 HTTP 상태 코드"만 응답.
> 

---

### 흐름 정리

```
Service: 핵심 로직 중 예외 발생 → Exception 던짐
ControllerAdvice: 예외를 HTTP 응답으로 변환

```

---

---