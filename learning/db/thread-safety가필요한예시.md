## Thread Safety가 필요한 예시 10가지

### 1. **글로벌 카운터 증가**

```python
count = 0

def increment():
    global count
    count += 1  # 여러 스레드에서 동시에 실행되면 꼬임

```

- 문제: `count` 값이 중복되거나 누락됨.

---

### 2. **공유 리스트에 항목 추가**

```python
shared_list = []

def add_item(item):
    shared_list.append(item)

```

- 문제: append 중간에 중단되면 데이터가 잘못 들어가거나 충돌 발생.

---

### 3. **로그 파일 쓰기**

```python
with open("log.txt", "a") as f:
    f.write("message\n")

```

- 문제: 동시에 여러 스레드가 접근하면 로그가 겹치거나 깨짐.

---

### 4. **DB 커넥션 객체 재사용**

```python
db_conn = get_connection()

def run_query():
    db_conn.execute("SELECT * FROM table")

```

- 문제: 하나의 커넥션을 여러 스레드에서 동시에 사용하면 커넥션 풀 상태가 꼬임.

---

### 5. **사용자 세션 데이터 접근**

```python
session_data[user_id]["cart"].append(item)

```

- 문제: 동시에 장바구니를 수정하면 데이터 손상 가능.

---

### 6. **캐시(dict)에 값 읽고 쓰기**

```python
if key not in cache:
    cache[key] = get_value_from_db()

```

- 문제: race condition → 동일 키가 중복 요청됨.

---

### 7. **이메일 또는 알림 발송 기록 체크**

```python
if not already_sent(email):
    send_email(email)
    mark_as_sent(email)

```

- 문제: 같은 이메일을 동시에 여러 번 보낼 수 있음.

---

### 8. **파일 다운로드 상태 저장**

```python
if not os.path.exists(file_path):
    download_file(url)

```

- 문제: 동시에 두 스레드가 같은 파일을 받게 될 수 있음.

---

### 9. **비밀번호 변경 처리**

```python
if user.password == old_password:
    user.password = new_password

```

- 문제: 동시에 요청되면 상태 꼬임 또는 Race Condition 가능성.

---

### 10. **통계 수치 업데이트 (ex. 조회수)**

```python
article.views += 1

```

- 문제: 여러 요청이 동시에 들어오면 조회수 업데이트가 누락될 수 있음.

---

## 해결 방법 요약

| 상황 | 해결책 |
| --- | --- |
| 공유 변수 조작 | `threading.Lock`, `multiprocessing.Lock` |
| 파일 접근 | `with` 블록 + Lock, 또는 파일 기반 queue |
| DB | 커넥션 풀 사용, 트랜잭션 처리 |
| 캐시 | `thread-safe cache` 라이브러리 (예: `cachetools`, `redis`) |
| async 환경 | `asyncio.Lock` 사용 |