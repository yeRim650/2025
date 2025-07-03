FastAPI는 **비동기 처리**에 최적화된 Python 웹 프레임워크지만, 이것이 **thread-safe(스레드 안전성)**을 자동으로 보장해 주는 것은 아닙니다.

## FastAPI와 비동기

- FastAPI는 `async def`로 정의된 **비동기 엔드포인트**를 지원.
- 내부적으로는 **Starlette + Uvicorn(ASGI 서버)** 를 사용하며, **비동기 I/O (asyncio)** 기반으로 작동.

**CPU가 아닌 I/O 대기 작업**(DB, 외부 API, 파일 읽기 등)에 효율적임.

---

## 그럼 Thread Safety는?

FastAPI는 **여러 요청을 동시에 처리**할 수 있기 때문에, 다음 두 가지 상황에서는 **thread safety를 신경 써야 합니다**:

---

### 1. **공유 자원 접근 (Shared State)**

예:

```python
counter = 0

@app.get("/increment")
def increment():
    global counter
    counter += 1
    return {"counter": counter}

```

위 코드는 **thread-safe하지 않음**. 여러 요청이 동시에 들어오면 `counter` 값이 꼬일 수 있음.

### 해결: Lock 사용

```python
from fastapi import FastAPI
from threading import Lock

app = FastAPI()
counter = 0
lock = Lock()

@app.get("/increment")
def increment():
    global counter
    with lock:
        counter += 1
        return {"counter": counter}

```

---

### 2. **비동기 + 동기 코드 혼용 (Blocking I/O)**

FastAPI는 `async def` 함수로 비동기 코드를 실행하지만, 내부에서 **동기 코드(예: 느린 DB 쿼리, 파일 I/O 등)** 를 호출하면 **이벤트 루프가 막힐 수 있음**.

### 해결: `run_in_threadpool` 사용

```python
from fastapi import FastAPI
from starlette.concurrency import run_in_threadpool

import time

def blocking_io():
    time.sleep(5)  # 블로킹 작업

app = FastAPI()

@app.get("/slow")
async def slow():
    await run_in_threadpool(blocking_io)
    return {"done": True}

```

---

## FastAPI에서 thread-safe하게 개발하는 팁

| 상황 | 해결책 |
| --- | --- |
| 글로벌 변수 사용 | 피하거나, `Lock`, `asyncio.Lock`으로 보호 |
| DB 커넥션 풀 | thread-safe 드라이버 사용 (예: `asyncpg`, `Databases`) |
| 파일 접근 | 파일을 공유하지 말고 요청별로 분리하거나 `Lock` 사용 |
| 동기 코드 | `run_in_threadpool()`로 실행하거나 `async` 라이브러리 사용 |

---

## 정리

| 질문 | 답변 |
| --- | --- |
| FastAPI가 thread-safe한가요? | **아니요.** 비동기지만, 공유 자원 사용 시 thread safety는 개발자가 신경 써야 함 |
| 비동기면 안전한가요? | **그렇지 않음.** 공유 자원, 블로킹 코드 등은 여전히 주의 필요 |
| 어떻게 thread-safe하게 만들 수 있나요? | `Lock`, `asyncio.Lock`, `run_in_threadpool()`, 또는 상태 공유 피하기 |