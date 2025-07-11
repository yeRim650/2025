# 리눅스에서 포트 테스트용 임시 서버 띄우기

이 문서는 리눅스 서버에서 특정 포트를 열어 테스트 서버를 띄우고, Windows PC에서 해당 포트로 접속해 테스트하는 방법을 설명합니다.

---

## 1. 리눅스에서 테스트 서버 열기 (netcat 사용)

### netcat 설치

```bash
# Ubuntu/Debian
sudo apt install netcat

# CentOS/RHEL
sudo yum install nc
````

### 포트 리슨 시작 (예: 40000, 40001, 40002)

```bash
# 포트 40000
nc -lk 40000

# 포트 40001
nc -lk 40001

# 포트 40002
nc -lk 40002
```

> 터미널을 하나씩 열어 각각 실행하거나, `&` 기호로 백그라운드 실행 가능

---

## 2. 윈도우에서 접속 테스트

### 방법 1: `telnet` 사용

#### telnet 설치 확인

* Windows 10/11에서는 기본 미설치
* **제어판 → 프로그램 및 기능 → Windows 기능 켜기/끄기 → "Telnet Client" 체크 후 적용**

#### 접속 명령어

```cmd
telnet [서버 IP] 40000
telnet [서버 IP] 40001
telnet [서버 IP] 40002
```

**예시**

```cmd
telnet 115.145.129.152 40000
```

#### 결과

* **빈 화면 또는 커서만 깜빡** → 연결 성공
* **"연결할 수 없습니다"** → 포트 차단 or 서비스 없음

---

### 방법 2: PowerShell 사용

Windows 10 이상에서는 PowerShell에서 테스트 가능:

```powershell
Test-NetConnection -ComputerName 115.145.129.152 -Port 40000
Test-NetConnection -ComputerName 115.145.129.152 -Port 40001
Test-NetConnection -ComputerName 115.145.129.152 -Port 40002
```

#### 결과 해석

* `TcpTestSucceeded : True` → 포트 열림
* `TcpTestSucceeded : False` → 포트 닫힘 or 서버 응답 없음

---

## 3. 기타 확인 사항

* **VPN 연결 여부 확인**: 내부망에 있어야 접속 가능할 수도 있음
* **서버 방화벽 확인**: `iptables`, `firewalld`, 클라우드 보안그룹 등에서 해당 포트 열려 있어야 함
* **Windows 방화벽 차단 가능성**도 고려

---

## 참고: 여러 포트 동시 리슨 스크립트 (리눅스)

```bash
#!/bin/bash
for port in 40000 40001 40002; do
  echo "Listening on port $port..."
  nc -lk $port &
done
```

---

## 요약

| 항목 | 리눅스에서 테스트 서버   | 윈도우에서 접속 테스트                   |
| -- | -------------- | ------------------------------ |
| 도구 | `nc`, `socat`  | `telnet`, `Test-NetConnection` |
| 조건 | 포트 열림 + 서비스 리슨 | VPN 또는 내부망 연결                  |
| 결과 | 연결되면 포트 OK     | 실패 시 방화벽 or 서비스 문제             |

