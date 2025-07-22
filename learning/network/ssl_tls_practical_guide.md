# SSL/TLS 실무 가이드

이 문서는 실무 환경에서 SSL/TLS를 이해하고 구성·운영하는 데 필요한 핵심 개념과 절차를 정리한 마크다운 파일입니다.

---

## 1. 개요

- **SSL(Security Socket Layer)**과 **TLS(Transport Layer Security)**는 네트워크 상에서 데이터 암호화 및 무결성을 보장하는 프로토콜
- 현재 SSL은 취약점으로 인해 사용되지 않으며, TLS(1.2 이상)를 사용
- 주요 목적
  - 인증(Authentication)
  - 암호화(Encryption)
  - 무결성(Integrity)

## 2. SSL/TLS 기본 구조

1. **핸드셰이크(Handshake)**
   - 클라이언트 헬로 → 서버 헬로 → 인증서 전송 → 키 교환 → 암호 스위트 협상 → Finished
2. **레코드 프로토콜(Record Protocol)**
   - 암호화된 데이터 전송
   - 분할, 압축, MAC, 암호화 단계

## 3. 인증서 종류

| 인증서 종류              | 설명                             |
| ------------------------ | -------------------------------- |
| 도메인 검증(DV)          | 도메인 소유권만 확인 (가장 간단, 저비용)       |
| 조직 검증(OV)           | 기업/단체 정보 확인                    |
| 확장 검증(EV)           | 엄격한 심사, 주소창 녹색 바 표시            |
| 와일드카드(*)            | `*.example.com` 하위 모든 서브도메인 커버 |
| SAN(SubjectAltName)      | 여러 개 도메인/서브도메인을 하나의 인증서로 커버    |

## 4. 인증서 발급 절차

1. **키 및 CSR 생성**

   ```bash
   openssl genrsa -out private.key 2048
   openssl req -new -key private.key -out request.csr \
       -subj "/C=KR/ST=Seoul/L=Gangnam/O=MyCompany/OU=IT/CN=example.com"
   ```
2. **CSR 제출 → CA 인증**
3. **인증서(pem, crt) 수령 및 체인 파일 확인**

## 5. 서버 설정 예시

### Nginx

```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate     /etc/ssl/certs/example.crt;
    ssl_certificate_key /etc/ssl/private/example.key;
    ssl_trusted_certificate /etc/ssl/certs/chain.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM';
    ssl_prefer_server_ciphers on;

    # HSTS
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
}
```

### Apache

```apacheconf
<VirtualHost *:443>
  ServerName example.com
  SSLEngine on
  SSLCertificateFile    /etc/ssl/certs/example.crt
  SSLCertificateKeyFile /etc/ssl/private/example.key
  SSLCertificateChainFile /etc/ssl/certs/chain.pem

  SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
  SSLCipherSuite HIGH:!aNULL:!MD5
  Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
</VirtualHost>
```

## 6. 주요 설정 항목 설명

- **ssl_protocols**: 허용할 TLS 버전 설정
- **ssl_ciphers**: 암호화 알고리즘 우선순위 지정
- **ssl_prefer_server_ciphers**: 서버측 암호화 스위트 우선 적용 여부
- **HSTS**: HTTP Strict Transport Security 헤더 설정
- **OCSP Stapling**: 인증서 폐기 여부 검증 최적화
  ```nginx
  ssl_stapling on;
  ssl_stapling_verify on;
  resolver 8.8.8.8 8.8.4.4;
  ```

## 7. 인증서 자동 갱신

- **Let’s Encrypt + Certbot**
  - 설치: `apt install certbot python3-certbot-nginx`
  - 자동 갱신 설정: `certbot renew --dry-run`
  - 크론으로 주기적 실행 (예: 매일 새벽 3시)
    ```cron
    0 3 * * * /usr/bin/certbot renew --quiet
    ```

## 8. 디버깅 & 모니터링

- **OpenSSL s_client**
  ```bash
  openssl s_client -connect example.com:443 -servername example.com
  ```
- **온라인 테스트**
  - SSL Labs: https://www.ssllabs.com/ssltest/
- **로그 확인**
  - Nginx: `/var/log/nginx/error.log`
  - Apache: `/var/log/apache2/error.log`

## 9. 보안 모범 사례

- TLS v1.3 우선 적용
- 불필요한 프로토콜/암호화 제거
- 인증서 비밀 키 권한 최소화 (`chmod 600`)
- 키 길이 최소 2048비트 이상 권장
- 주기적인 취약점 스캔

## 10. 자주 겪는 이슈

- **인증서 체인 누락**: 클라이언트에서 "unable to get local issuer certificate"
- **포트/방화벽 차단**: 443 포트 확인
- **만료된 인증서**: 갱신 스크립트 오류

---

### 참고 자료

- [RFC 8446: TLS 1.3](https://tools.ietf.org/html/rfc8446)
- [OpenSSL 공식 문서](https://www.openssl.org/docs/)
- [Let’s Encrypt Documentation](https://letsencrypt.org/docs/)
