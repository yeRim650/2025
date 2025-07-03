### 1. **Thread Safety (스레드 안전성)**

- **정의:** 여러 개의 스레드가 동시에 접근해도 **공유 데이터가 손상되지 않도록 보장하는 성질**입니다.
- **목적:** 동시에 실행되는 스레드 간에 **데이터 충돌이나 경쟁 조건(race condition)**을 방지.
- **예:** 하나의 리스트를 여러 스레드가 동시에 수정하지 못하게 `lock`으로 제어하는 것.

---

### 2. **Asynchronous (비동기)**

- **정의:** 작업을 시작하고 기다리지 않고 바로 다음 작업을 수행하며, 나중에 작업 결과를 **콜백, Future, Promise 등으로 처리**하는 방식.
- **목적:** **대기 시간(blocking)** 없이 빠르게 처리 흐름을 유지하여 **I/O 작업**에 효율적.
- **예:** 파일 읽기, 네트워크 요청 등을 비동기로 처리하고 완료 시점에 결과를 받음.

---

### 관계: Thread Safety vs 비동기

| 항목 | Thread Safety | 비동기 |
| --- | --- | --- |
| 개념 | 동시성 환경에서 데이터 안전 | 순차 흐름을 깨고 대기 없이 실행 |
| 동시성 | 스레드 기반 (병렬 처리) | 이벤트 루프 기반 (단일 스레드도 가능) |
| 관련성 | 공유 데이터가 있으면 필요 | 단일 스레드라면 대체로 불필요 |
| 예 | `synchronized`, `mutex`, `lock` | `async/await`, `Promise`, `Future` |

---

### 예시로 보는 차이

### [1] 비동기 + 단일 스레드 (예: JavaScript)

```
let count = 0;

async function updateCount() {
  count += 1;
}

```

- 자바스크립트는 단일 스레드라서 일반적으로 thread safety 문제가 없음.

### [2] 멀티스레드 환경 (예: Java)

```java
private int count = 0;

public synchronized void increment() {
    count++;
}

```

- 여러 스레드에서 동시에 접근하므로 `synchronized`로 thread-safe하게 만듦.

---

### 요약

| 질문 | 답변 |
| --- | --- |
| 비동기면 thread-safe한가요? | **항상 그렇진 않습니다.** 비동기라 해도 내부에서 멀티스레드를 쓰면 thread safety 고려 필요 |
| 단일 스레드에서만 동작한다면? | **thread safety를 고려할 필요 없음** (예: JavaScript) |
| 비동기 + 멀티스레드? | **thread safety 필요** (예: Python의 `asyncio` + 쓰레드 혼용 시) |