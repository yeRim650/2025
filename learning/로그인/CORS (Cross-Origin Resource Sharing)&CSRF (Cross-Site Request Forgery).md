**CORS (Cross-Origin Resource Sharing)** 와 **CSRF (Cross-Site Request Forgery)** 는 둘 다 웹 애플리케이션 보안과 관련된 개념이지만, 서로 다른 문제를 다룹니다.

---

## 1. CORS (Cross-Origin Resource Sharing)

- **문제**
    
    브라우저 보안 정책(“동일 출처 정책, Same-Origin Policy”)에 따라, 스크립트가 로드된 도메인(origin)과 다른 도메인으로 AJAX 요청을 보내면 기본적으로 차단됩니다.
    
    예: `https://app.example.com` 페이지에서 `https://api.example.com` 을 호출할 때
    
- **해결책**
    
    서버가 HTTP 응답 헤더를 통해 “어느 출처에서 오는 요청을 허용할지” 명시적으로 선언함으로써, 브라우저가 이를 해제해 줍니다.
    
- **주요 헤더**
    
    ```
    Access-Control-Allow-Origin: https://app.example.com
    Access-Control-Allow-Credentials: true
    Access-Control-Allow-Methods: GET, POST, PUT, DELETE
    Access-Control-Allow-Headers: Content-Type, Authorization, X-CSRF-Token
    
    ```
    
    - `Allow-Origin`: 허용할 출처(혹은 )
    - `Allow-Credentials`: 쿠키·헤더 인증 정보를 포함할지를 허용
    - `Allow-Methods`, `Allow-Headers`: 어떤 메서드·헤더를 허용할지
- **Spring Boot 설정 예**
    
    ```java
    @Configuration
    public class WebConfig implements WebMvcConfigurer {
        @Override
        public void addCorsMappings(CorsRegistry registry) {
            registry.addMapping("/**")
                    .allowedOrigins("https://app.example.com")
                    .allowedMethods("GET","POST","PUT","DELETE")
                    .allowCredentials(true)
                    .allowedHeaders("Content-Type","Authorization","X-CSRF-Token");
        }
    }
    
    ```
    

---

## 2. CSRF (Cross-Site Request Forgery)

- **문제**
    
    사용자가 이미 로그인된 상태에서, 악성 사이트가 **사용자의 인증 쿠키**를 몰래 이용해 API 요청을 보내면, 서버는 이를 정상 요청으로 처리합니다.
    
    예: `<img src="https://bank.example.com/transfer?to=attacker&amount=1000">`
    
- **해결책**
    
    “진짜 내 애플리케이션에서 보낸 요청인지” 검증할 수 있는 **CSRF 토큰** 혹은 `SameSite` 쿠키 정책을 함께 사용합니다.
    

### A) CSRF 토큰 방식

1. 서버가 **폼 렌더링 시** 고유 토큰 발급 → HTML `<input type="hidden" name="_csrf" value="…">`
2. 클라이언트가 폼 제출·AJAX 요청 시 이 토큰을 헤더(`X-CSRF-Token`)에 포함
3. 서버는 요청 헤더의 토큰 값과 세션(혹은 쿠키) 저장 토큰을 대조하여 일치할 때만 처리

```java
// Spring Security 기본 CSRF 설정
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
          .csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
          .and()
          .authorizeRequests()
            .anyRequest().authenticated();
    }
}

```

### B) SameSite 쿠키 설정

- `SameSite=Strict` 또는 `Lax` 옵션을 설정하면, **외부 사이트**에서 쿠키가 전송되지 않도록 브라우저가 차단합니다.
- 쿠키만으로 CSRF 리스크를 크게 줄일 수 있지만, 완벽하진 않으므로 중요한 상태 변경 요청에는 CSRF 토큰을 함께 쓰는 것이 안전합니다.

```
Set-Cookie: sessionId=…; HttpOnly; Secure; SameSite=Strict

```

---

## 3. CORS vs. CSRF: 요약 비교

| 구분 | CORS | CSRF |
| --- | --- | --- |
| 목적 | “어느 출처에서 API를 호출할 수 있나” 제어 | “진짜 내 앱에서 보낸 요청인가” 검증 |
| 보호 대상 | 클라이언트(브라우저)에서의 AJAX 요청 차단 해제·허용 | 서버 측에서 외부 요청에 의한 상태 변경 방어 |
| 주요 메커니즘 | HTTP 응답 헤더(`Access-Control-*`) | CSRF 토큰 매핑 또는 쿠키 `SameSite` 속성 |
| 적용 위치 | 서버(REST API) | 서버(서버가 폼이나 AJAX 요청 처리 로직에 삽입) |

---

### 결론

- **CORS** 는 “내 API를 어느 도메인에서 허용할지” 설정하는 브라우저 보안 정책

- **CSRF** 는 “인증된 사용자 권한을 악용한 요청”을 서버가 차단하기 위한 방어 기법

두 가지를 **함께** 올바르게 구성해야 SPA + SSO 환경에서 안전하고 원활하게 동작합니다.