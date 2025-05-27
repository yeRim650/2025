# 자료구조
### JVM이 정확히 무엇이고, 어떤 기능을 하는지 설명해 주세요.
<details>
<summary></summary>
<div>

## 1. JVM의 정의
- **Java Virtual Machine(JVM)**이란 자바 바이트코드(bytecode)를 실행하기 위한 **가상 컴퓨터(추상화된 컴퓨팅 플랫폼)**입니다.  
- 자바 컴파일러(`javac`)가 생성한 `.class` 파일(바이트코드)을 입력으로 받아, 실제 머신(운영체제·하드웨어)에서 동작할 수 있도록 해석·실행합니다.  
- JVM은 **‘한 번 컴파일하면 어디서나 실행된다(Write Once, Run Anywhere)’**는 자바 플랫폼의 핵심을 가능하게 하는 핵심 요소입니다.

## 2. JVM의 주요 구성 요소
1. **클래스 로더(Class Loader)**  
   - `.class` 파일을 읽어 메모리에 올리고, 내부 데이터 구조로 변환(링크)합니다.  
   - 사용자 정의 클래스뿐 아니라 런타임 라이브러리(예: `java.lang.*`)도 모두 이 서브시스템을 통해 로드됩니다.
2. **런타임 데이터 영역(Runtime Data Areas)**  
   - **메소드 영역(Method Area)**: 클래스·인터페이스의 런타임 상수 풀, 필드·메소드 정보 저장  
   - **힙(Heap)**: 객체 및 배열 인스턴스들이 생성되는 공간  
   - **스택(Stack)**: 각 스레드별로 JVM 호출 스택을 유지(메소드 호출 시마다 프레임 생성)  
   - **프로그램 카운터(PC) 레지스터**: 각 스레드가 현재 실행 중인 바이트코드의 주소를 가리킴  
   - **네이티브 메소드 스택(Native Method Stack)**: 자바 외 언어(C/C++ 등)로 작성된 네이티브 메소드 호출 시 사용
3. **실행 엔진(Execution Engine)**  
   - **인터프리터(Interpreter)**: 바이트코드를 한 줄씩 해석하며 실행  
   - **JIT 컴파일러(Just-In-Time Compiler)**: 반복적으로 호출되는 바이트코드를 네이티브 머신 코드로 번역해 **성능을 최적화**  
4. **가비지 컬렉터(Garbage Collector, GC)**  
   - 더 이상 참조되지 않는 객체를 탐지해 자동으로 메모리를 회수  
   - 메모리 누수를 방지하고, 명시적 메모리 해제 없이 안전한 실행 환경 제공  
5. **네이티브 인터페이스(Java Native Interface, JNI)**  
   - 자바 애플리케이션에서 C/C++ 등의 네이티브 코드를 호출할 수 있게 해 주는 브릿지 역할

## 3. JVM의 주요 기능
- **플랫폼 독립성 보장**  
  자바 바이트코드는 특정 하드웨어나 OS에 종속되지 않으므로, 동일한 `.class` 파일을 Windows·macOS·Linux 등 다양한 환경에서 실행 가능
- **메모리 관리 및 자동 가비지 컬렉션**  
  개발자가 명시적으로 메모리를 해제하지 않아도, JVM이 필요 시점에 객체를 정리하여 안정적인 메모리 운영
- **성능 최적화**  
  JIT 컴파일러를 통해 런타임에 핫스팟(빈번히 호출되는 코드)을 네이티브 코드로 변환, 반복 실행 시 성능 향상
- **보안 관리**  
  바이트코드 검증(bytecode verifier)을 통해 악의적인 코드 삽입을 방지하고, 클래스 로더·보안 매니저를 통해 실행 권한을 제어
- **멀티스레드 스케줄링 지원**  
  JVM 내부에서 스레드별 호출 스택과 PC 레지스터를 관리하여, 자바 레벨의 스레드 동시 실행 및 동기화 제공

## 4. JVM의 동작 과정 요약
1. **소스 코드(.java) → 바이트코드(.class) 컴파일**  
2. **클래스 로더**가 `.class` 파일 로드 및 링크  
3. **바이트코드 검증** (안전성·무결성 검사)  
4. **인터프리터** 또는 **JIT 컴파일러**를 통해 바이트코드 실행  
5. **런타임 데이터 영역**에서 객체 생성 및 메모리 관리  
6. **가비지 컬렉터**가 주기적으로 불필요 객체 회수

## 5. JVM의 이점
- **이식성**: 한번 작성한 코드를 다양한 플랫폼에서 실행  
- **안정성**: 메모리 안전성 및 보안 메커니즘 내장  
- **관리 용이성**: 자동 메모리 관리, 모니터링·디버깅 도구 제공  
- **확장성**: 다양한 가비지 컬렉터(G1, ZGC 등)와 최적화 기법 선택 가능  

---

이와 같이 JVM은 자바 프로그램을 실행하기 위한 **추상화된 런타임 환경**으로, 바이트코드를 플랫폼 독립적으로 해석·컴파일하고, 메모리·스레드·보안을 종합적으로 관리함으로써 자바의 핵심 가치인 “이식성·안정성·성능”을 실현합니다.
</div>
</details>
<details>
<summary>그럼, 자바 말고 다른 언어는 JVM 위에 올릴 수 없나요?</summary>
<div>

JVM은 ‘바이트코드(​.class)’를 실행하는 가상 머신이기 때문에, **바이트코드를 생성할 수 있는(any language compiler)를 JVM 타깃으로 삼기만 하면** 자바뿐 아니라 어떤 언어도 올릴 수 있습니다. 실제로 다음과 같은 언어들이 JVM 위에서 동작합니다:

- **Kotlin**  
  JetBrains에서 만든 정적 타입 언어로, 자바와 100% 상호 운용(interoperability) 가능  
- **Scala**  
  함수형·객체지향을 결합한 언어로, 병렬·비동기 프로그래밍에 강점  
- **Groovy**  
  동적 스크립트 언어로, 자바 문법과 호환되며 빌드 스크립트·테스트 코드 등에 자주 사용  
- **Clojure**  
  JVM 기반의 함수형 리스프(Lisp) 언어, 불변 자료구조·병렬 처리 모델 제공  
- **JRuby**  
  루비(Ruby) 언어를 JVM 위에서 실행할 수 있게 옮긴 구현체  
- **Jython**  
  파이썬(Python) 2.x 계열을 JVM 위에서 실행할 수 있는 구현체  
- **Ceylon, Fantom, Xtend, Gosu, Frege** 등  
  각각 고유의 문법과 특성을 가진 언어들이 JVM 타깃을 지원  

---

## 어떻게 가능할까?
1. **컴파일러 플러그인/백엔드**  
   - 언어의 컴파일러가 최종 산출물을 JVM 바이트코드로 생성하도록 백엔드를 구현  
   - 예: Kotlin 컴파일러, Scala 컴파일러, Groovy 컴파일러 등  
2. **인터프리터 방식**  
   - 순수 자바(Java) 코드로 해당 언어의 인터프리터를 작성하고, JVM에서 직접 해석  
   - 예: JRuby, Jython  
3. **JSR-292 (invokedynamic) 지원**  
   - 자바 7부터 도입된 `invokedynamic` 바이트코드 명령어로 동적 언어를 더 효율적으로 구현  

이처럼 JVM은 **언어 독립적인 런타임 플랫폼**으로 설계되어 있기 때문에, “JVM 타깃 코드(바이트코드)만 만들 수 있느냐”가 관건입니다. 자바가 아니어도, 자신만의 문법·타입 시스템을 가진 언어라도 바이트코드를 생산할 수 있다면 JVM 위에서 실행시킬 수 있습니다.
</div>
</details>
<details>
<summary>반대로 JVM 계열 언어를 일반적으로 컴파일해서 사용할 순 없나요?</summary>
<div>

**JVM 바이트코드로 컴파일**되어 `.class` 파일로 출력되고, 이를 JVM에서 실행하는 방식으로 동작합니다. 즉, 이들은 \*\*"JVM 위에서 실행되는 언어"\*\*입니다. 따라서 보통은 **JVM 없이 실행되지 않습니다.**

하지만 "일반적으로 컴파일해서 사용할 수 없느냐?"는 질문에 대해 더 깊이 들어가 보면 몇 가지 예외나 대안도 존재합니다.

---

### ✅ JVM 계열 언어를 "일반적으로 컴파일해서" 실행하는 방법들

1. **Ahead-of-Time (AOT) 컴파일러 사용**

   * 대표 사례: **GraalVM Native Image**
   * Java나 Kotlin 코드를 JVM 없이 실행할 수 있는 **네이티브 바이너리**로 컴파일합니다.
   * 장점: 빠른 시작 시간, 낮은 메모리 사용량
   * 단점: 리플렉션 등 일부 JVM 기능은 제한될 수 있음

2. **Kotlin/Native**

   * Kotlin은 JVM 이외에도 **Native 플랫폼용 컴파일러(Kotlin/Native)** 를 제공합니다.
   * 주로 iOS, Linux CLI 프로그램 등에 사용됩니다.
   * JVM 없이 실행 가능합니다.

3. **Scala Native**

   * Scala를 LLVM 기반의 **네이티브 코드**로 컴파일하는 프로젝트
   * 아직 완전 성숙하지는 않지만, 간단한 CLI 앱 등은 작성 가능

---

### ❌ 일반적인 상황에서는 JVM이 필요함

* Java나 Kotlin은 기본적으로 `javac`, `kotlinc` 등으로 **바이트코드**를 생성하고,
* 생성된 `.class` 파일은 `java` 커맨드나 JVM 기반 런타임에서 실행됨
* 그러므로 **JVM이 없이는 실행할 수 없습니다.**

---

요약하자면:
\*\*"일반적인 상황에서는 JVM이 필요하지만, 네이티브 컴파일러(AOT)를 사용하면 JVM 없이 실행 가능한 실행 파일도 만들 수 있다"\*\*는 게 핵심입니다.

</div>
</details>

<details>
<summary>VM을 사용함으로써 얻을 수 있는 장점과 단점에 대해 설명해 주세요.</summary>
<div>

가상 머신(VM, 특히 여기서는 **Java Virtual Machine – JVM**을 염두에 두고 설명하겠습니다)을 사용하는 것은 개발과 실행 환경에 많은 영향을 미칩니다. 아래는 **VM 사용의 장점과 단점**을 정리한 내용입니다.

---

## ✅ VM(JVM)을 사용함으로써 얻을 수 있는 **장점**

### 1. **플랫폼 독립성 (Write Once, Run Anywhere)**

* Java나 Kotlin 같은 언어는 바이트코드로 컴파일되고, JVM만 있다면 **Windows, macOS, Linux 등에서 동일하게 실행 가능**합니다.
* 이는 다양한 운영체제를 지원해야 하는 애플리케이션에 큰 장점입니다.

### 2. **풍부한 런타임 기능**

* 자동 메모리 관리(Garbage Collection)
* 스레드 관리, 모니터링, 리플렉션, JIT(Just-In-Time) 컴파일 등 고급 기능 제공
* 개발자가 저수준의 자원 관리를 직접 할 필요가 줄어듭니다.

### 3. **보안**

* JVM은 **샌드박스 환경**을 통해 애플릿이나 스크립트의 접근 범위를 제한할 수 있음
* 특히 웹 기반의 코드 실행 등에서 유용함 (과거에는 많이 쓰였지만 지금은 줄어듦)

### 4. **성숙한 생태계**

* 수십 년에 걸친 **툴링, 디버깅, 모니터링** 생태계
* Spring, Gradle, IntelliJ, JUnit 등 다양한 도구와 라이브러리 제공

### 5. **멀티언어 지원**

* JVM 위에서는 Java 외에도 Kotlin, Scala, Groovy 등 다양한 언어를 사용할 수 있음
* 여러 언어를 섞어 개발하는 것도 가능

---

## ❌ VM(JVM)을 사용할 때의 **단점**

### 1. **시작 시간 지연 (Cold Start)**

* JVM은 애플리케이션 시작 시 초기화 작업이 많아서 **시작 시간이 느릴 수 있음**
* 서버리스 환경이나 CLI 툴 등에서는 큰 단점이 될 수 있음

### 2. **메모리 사용량이 큼**

* JVM 자체가 일정 메모리를 항상 점유하고 있으며, 가비지 컬렉션 등도 추가 리소스를 사용
* 가벼운 애플리케이션에는 과할 수 있음

### 3. **JVM에 대한 의존**

* 실행에 반드시 JVM이 필요하며, JVM 버전에 따라 호환성 문제가 생길 수도 있음
* 설치/배포 환경에서 JVM 유무 확인이 필요

### 4. **네이티브 성능보다 낮을 수 있음**

* C/C++ 등 네이티브 언어보다는 실행 성능이 낮거나 예측 불가능할 수 있음
* 특히 하드웨어에 가까운 시스템 프로그래밍에는 부적합

---

## 🔄 요약 표

| 항목      | 장점             | 단점             |
| ------- | -------------- | -------------- |
| 플랫폼 독립성 | OS에 상관없이 실행 가능 | JVM 설치 필요      |
| 성능      | JIT으로 일정 수준 확보 | 네이티브보다 느릴 수 있음 |
| 메모리     | 자동 관리(GC)      | 리소스 사용량 많음     |
| 보안      | 샌드박스 실행        | 복잡한 설정 필요      |
| 생태계     | 풍부한 도구와 언어     | 러닝 커브 가능       |

---

좋습니다! 아래는 **VM 기반 언어 (예: Java, Kotlin, C#, Scala 등)** 와 **네이티브 언어 (예: C, C++, Rust, Go 등)** 를 다양한 관점에서 비교한 표입니다. 두 유형은 서로 다른 철학과 목적을 가지고 있어, 선택은 사용 목적과 환경에 따라 달라집니다.

---

## ✅ VM 기반 언어 vs 네이티브 언어 비교

| 항목           | VM 기반 언어                              | 네이티브 언어                        |
| ------------ | ------------------------------------- | ------------------------------ |
| **실행 방식**    | 중간 바이트코드로 컴파일 → VM(JVM, CLR 등) 위에서 실행 | 기계어로 직접 컴파일 → OS에서 직접 실행       |
| **플랫폼 독립성**  | 높음 (JVM만 있으면 OS에 상관없이 실행 가능)          | 낮음 (OS별로 바이너리 따로 빌드 필요)        |
| **성능**       | 적절히 빠름 (JIT, GC 등 덕분에 괜찮은 성능)         | 일반적으로 더 빠름 (AOT 컴파일로 최적화됨)     |
| **시작 속도**    | 느릴 수 있음 (JVM 초기화 시간)                  | 빠름 (바로 실행 가능)                  |
| **메모리 사용**   | 더 큼 (JVM 자체 오버헤드 + GC 리소스)            | 더 작음 (직접 메모리 관리)               |
| **개발 생산성**   | 높음 (자동 메모리 관리, 풍부한 라이브러리, 리플렉션 등)     | 낮을 수 있음 (직접 메모리 관리, 포인터 등)     |
| **디버깅/모니터링** | 풍부한 툴 제공 (JVM 기반 툴: VisualVM, JFR 등)  | 도구는 다양하지만 OS/언어마다 다름           |
| **안정성**      | 메모리 관련 버그 적음 (GC, 타입 안정성)             | 메모리 오류 발생 가능 (버퍼 오버플로우 등)      |
| **호환성**      | JVM 버전 간 이슈 있을 수 있음                   | OS/컴파일러 종속성 문제 있을 수 있음         |
| **사용 예**     | 웹 서버, 대규모 엔터프라이즈 앱, 백엔드 시스템           | 시스템 프로그래밍, 게임, OS, 실시간 앱, 임베디드 |

---

## ✅ 요약

* **VM 기반 언어**

  * 장점: 개발 속도 빠르고 안정적, 이식성 우수
  * 단점: 성능과 자원 사용면에서 불리

* **네이티브 언어**

  * 장점: 성능 우수, 리소스 제어 가능
  * 단점: 개발 난이도 높고, 플랫폼 종속성 있음

---

### 💡 어떤 경우에 어떤 언어를 쓰면 좋을까?

| 상황                        | 추천               |
| ------------------------- | ---------------- |
| 빠르게 안정적인 서버 구축            | Java, Kotlin, C# |
| 고성능 시스템이나 게임 개발           | C++, Rust        |
| 임베디드 시스템, 드라이버            | C                |
| 빠른 시작 시간과 단일 실행파일이 중요한 경우 | Go, Rust         |
| 복잡한 비즈니스 로직이 많은 대형 서비스    | Java, Scala      |

---


</div>
</details>

<details>
<summary>JVM과 내부에서 실행되고 있는 프로그램은 부모 프로세스 - 자식 프로세스 관계를 갖고 있다고 봐도 무방한가요?</summary>
<div>

**JVM과 그 안에서 실행되는 프로그램(Java, Kotlin 등으로 작성된 코드)** 간의 관계를 \*\*"부모-자식 프로세스 관계"\*\*로 보는 것은 **정확하지 않습니다.**

---

## 🔍 이유 설명

### ✅ JVM은 **하나의 프로세스**입니다.

* 우리가 `java MyProgram` 같은 명령어로 프로그램을 실행하면:

  1. **JVM 프로세스**가 시작되고
  2. 그 안에서 `.class` 또는 `.jar` 파일에 담긴 **바이트코드를 해석/실행**합니다.
* 즉, **Java 애플리케이션은 JVM 프로세스 안에서 실행되는 코드일 뿐**, 별도의 프로세스가 아닙니다.

➡️ 따라서 JVM과 Java 애플리케이션은 **"부모-자식 프로세스"가 아니라, "호스트와 내부 실행 코드" 관계**입니다.

---

## 💡 예외적으로 자식 프로세스를 생성하는 경우

Java 애플리케이션 내에서 다음과 같은 방식으로 **실제 자식 프로세스**를 생성할 수는 있습니다:

```java
Process p = Runtime.getRuntime().exec("ping google.com");
```

* 이 경우 `ping`은 **자식 프로세스**로 실행되며,
* **JVM은 부모 프로세스**, `ping`은 실제 OS 레벨에서 **자식 프로세스**가 됩니다.

---

## 🔄 요약

| 구분                     | 관계                               |
| ---------------------- | -------------------------------- |
| JVM ↔ Java 애플리케이션      | ❌ 프로세스-프로세스 아님 (같은 프로세스 내에서 실행됨) |
| JVM ↔ exec()로 만든 외부 명령 | ✅ 부모-자식 프로세스 관계 성립               |

---

ps, top, pstree 같은 명령어로 JVM과 서브프로세스 관계를 확인

리눅스나 유닉스 계열 시스템에서 **JVM과 서브프로세스 관계**를 확인하는 데 사용할 수 있는 주요 명령어들을 아래에 정리

---

## ✅ 1. `ps` 명령어

```bash
ps -ef | grep java
```

* 현재 실행 중인 JVM 프로세스를 확인할 수 있습니다.
* `java -jar MyApp.jar` 같은 명령이 보일 겁니다.

---

## ✅ 2. `pstree` 명령어

```bash
pstree -p
```

* 프로세스들을 **트리 구조로 시각화**해서 보여줍니다.
* JVM이 `exec()` 등을 통해 생성한 **자식 프로세스**가 있다면 JVM PID 아래에 보이게 됩니다.

예시:

```
systemd(1)─┬─java(12345)─┬─ping(12346)
```

→ 여기서 `java(12345)`가 JVM이고, `ping(12346)`은 `Runtime.exec()` 등으로 생성된 자식 프로세스입니다.

---

## ✅ 3. `top` 또는 `htop`

* 실행 중인 프로세스를 실시간으로 볼 수 있고,
* `htop`에서는 **트리 모드(F5)** 로 자식 프로세스를 확인할 수 있습니다.

---

## ✅ 4. Java 코드에서 PID 확인하는 방법

```java
long pid = ProcessHandle.current().pid();
System.out.println("JVM PID: " + pid);
```

자식 프로세스는 아래처럼 생성:

```java
Process p = Runtime.getRuntime().exec("ping google.com");
```

이 경우, OS 레벨에서 `ping` 프로세스는 위 JVM의 **자식 프로세스**로 보이게 됩니다.

---

</div>
</details>

### final 키워드를 사용하면, 어떤 이점이 있나요?

<details>
<summary></summary>
<div>

1. 변수에 final 사용
java
복사
편집
final int x = 10;
한 번 초기화된 이후 값을 변경할 수 없음.

이점:

불변성 보장: 코드 흐름 중 값이 바뀌지 않아 오류 가능성 감소.

멀티스레딩 환경에서 안정성 향상: final 변수는 thread-safe하게 작동함.

2. 메서드에 final 사용
java
복사
편집
class Parent {
    public final void show() {
        System.out.println("Hello");
    }
}
서브클래스에서 오버라이드 금지.

이점:

의도한 동작을 유지: 중요한 로직이 자식 클래스에서 변경되지 않도록 보장.

보안성 향상: 예기치 않은 확장을 막아 코드 안정성 증가.

3. 클래스에 final 사용
java
복사
편집
final class MyClass {
    // ...
}
상속이 불가능한 클래스.

이점:

디자인 고정: 의도적으로 확장을 금지해 API 설계 안정성 확보.

성능 최적화 가능: JIT 컴파일러가 final 클래스는 더 공격적으로 최적화 가능.

추가 이점
가독성 향상: 값을 변경하지 않을 것임을 명시적으로 표현.

에러 예방: 실수로 변수 재할당, 메서드 오버라이드 등 방지.

필요 이상으로 final을 남용하면 유연성이 떨어질 수 있으므로, 변경 가능성이 없다고 확신할 때 사용
</div>
</details>

<details>
<summary>그렇다면 컴파일 과정에서, final 키워드는 다르게 취급되나요?</summary>
<div>

`final` 키워드는 **컴파일러와 JVM이 코드를 다르게 최적화할 수 있도록 힌트를 주는 역할**을 합니다. 컴파일 과정에서 `final`이 있으면 다음과 같은 방식으로 특별히 취급됩니다:

---

### 1. **`final` 변수 (특히 상수)는 인라인(inline) 처리됨**

```java
final int A = 10;
int b = A + 5;
```

* 컴파일 시점에 `A`는 리터럴 `10`으로 직접 치환되어 사용됨.
* **결과적으로 더 빠른 실행**과 **메모리 절약** 가능.
* 단, `static final` 변수는 상수 풀(Constant Pool)에 저장되어 전역적으로 재사용 가능.

---

### 2. **`final` 메서드는 오버라이딩이 불가능하므로 디스패치 최적화 가능**

```java
final void doSomething() {}
```

* 메서드가 서브클래스에서 바뀔 수 없으므로 **정적 바인딩(static binding)** 사용 가능.
* 일반적인 메서드는 **동적 바인딩**을 거쳐야 하지만, `final`이면 그 과정을 생략 가능 → **호출 속도 향상**.

---

### 3. **`final` 클래스는 전체 구조가 고정되므로 더 과감한 최적화 가능**

```java
final class MyUtil { ... }
```

* JVM이 클래스의 구조나 메서드 호출 방식이 절대 바뀌지 않는다고 확신할 수 있어, **JIT 컴파일러가 aggressive한 인라이닝이나 옵티마이제이션 수행** 가능.

---

### 4. **`final` 지역 변수는 람다나 익명 클래스에서 참조 가능**

```java
Runnable r = () -> {
    System.out.println(a); // a는 final 또는 effectively final이어야 함
};
```

* 컴파일러는 해당 변수를 \*\*closure(클로저)\*\*에 캡처해야 하므로, 값이 바뀌지 않음을 확신해야 함.

---

요약하면, **`final`은 컴파일러에게 "이 값/구조는 바뀌지 않는다"는 확신을 주어 더 효율적인 바이트코드와 실행 성능을 가능하게 합니다**.

`final` 키워드는 JIT(Just-In-Time) 컴파일러가 **더 aggressive(공격적인) 인라이닝 및 최적화**를 수행할 수 있도록 도와줍니다. 그 이유와 작동 방식은 다음과 같습니다:

---

### ✅ **JIT 컴파일러 최적화란?**

* JIT은 자바 바이트코드를 **런타임 시 기계어로 변환하면서 성능 최적화**를 수행합니다.
* JIT의 목표는 CPU가 더 빠르게 실행할 수 있도록 **중복 제거**, **함수 인라이닝**, **루프 최적화** 등을 적용하는 것입니다.

---

### 💡 `final`이 어떻게 도움이 되는가?

#### 1. **메서드 인라이닝 (Method Inlining)**

* 인라이닝이란, **함수를 호출하지 않고, 그 내부 코드를 호출 지점에 직접 삽입**하는 최적화입니다.
* 일반 메서드는 오버라이딩 가능성이 있어 런타임까지 어떤 구현이 호출될지 확정할 수 없습니다.
* 하지만 `final` 메서드는 **절대 오버라이딩되지 않음** → **JVM이 인라인을 바로 적용 가능**.

예:

```java
final void printHello() {
    System.out.println("Hello");
}
```

* 이 함수가 자주 호출된다면, 호출부에 `System.out.println("Hello");`를 직접 넣을 수 있습니다.
* → **함수 호출 비용 제거** + **JVM 추가 최적화 가능**.

---

#### 2. **클래스 인라이닝**

* `final` 클래스는 구조가 확정되어 있어, **JVM이 객체 구조를 분석하고 최적화하기 쉬움**.
* 예: `final class Point { int x, y; }`

  * 객체 생성 시 구조가 불변임을 알기 때문에 **메모리 접근이나 생성 비용 최적화 가능**.

---

#### 3. **필드 접근 최적화**

* `final` 필드는 한 번만 설정되고 이후 변경되지 않기 때문에:

  * **CPU 캐시에 안정적으로 유지될 수 있음**
  * **배치된 값이 바로 인라인**되거나 **dead code 제거 대상이 아님**.

---

### 🧠 요약

| 키워드           | JIT 최적화 혜택          |
| ------------- | ------------------- |
| `final` 메서드   | 인라이닝, 정적 바인딩 가능     |
| `final` 클래스   | 인라인 생성, 구조 최적화      |
| `final` 변수/필드 | 값 고정 → 빠른 접근, 상수 대체 |

---

JIT의 이런 최적화는 특히 **반복 호출**, **루프 내 코드**, **작은 유틸리티 메서드**에서 성능 차이를 체감할 수 있습니다.

</div>
</details>

### 인터페이스와 추상 클래스의 차이에 대해 설명해 주세요.

<details>
<summary></summary>
<div>
인터페이스(Interface)와 추상 클래스(Abstract Class)는 모두 Java에서 \*\*추상화(abstraction)\*\*를 제공하는 도구지만, 용도와 사용 방식에 중요한 차이가 있습니다. 아래에서 핵심 차이를 정리해 드리겠습니다.

---

## ✅ 기본 개념

| 항목      | 인터페이스 (Interface)             | 추상 클래스 (Abstract Class)          |
| ------- | ----------------------------- | -------------------------------- |
| 목적      | **계약(Contract)**: 무엇을 할 수 있는가 | **틀 제공(Template)**: 어떻게 동작해야 하는가 |
| 다중 상속   | O (여러 인터페이스 구현 가능)            | X (한 개의 클래스만 상속 가능)              |
| 인스턴스 생성 | X (객체 생성 불가)                  | X (객체 생성 불가)                     |

---

## ✅ 멤버 구성

| 항목  | 인터페이스                           | 추상 클래스                           |
| --- | ------------------------------- | -------------------------------- |
| 필드  | `public static final` 상수만 가능    | 일반 필드 가능 (private, protected 포함) |
| 메서드 | 기본은 추상 메서드, `default/static` 허용 | 추상 메서드 + 일반 메서드 둘 다 가능           |
| 생성자 | 없음                              | 있음                               |

---

## ✅ 사용 목적 차이

| 항목       | 인터페이스                       | 추상 클래스                      |
| -------- | --------------------------- | --------------------------- |
| 언제 사용하나? | 전혀 다른 계열의 클래스에 **공통 기능 명세** | 관련 있는 클래스들의 **공통 기능 구현 제공** |
| 예시       | `Comparable`, `Runnable`    | `Animal`, `Shape` 등         |

---

## ✅ Java 8 이후의 인터페이스 변화

* `default` 메서드: 인터페이스에서도 구현 가능해짐.
* `static` 메서드: 인터페이스에 정적 메서드 정의 가능.
* 하지만 여전히 \*\*상태(필드)\*\*는 가질 수 없으며, `Object` 클래스의 메서드는 오버라이드 불가.

---

## ✅ 예제 비교

### 인터페이스 예

```java
interface Drivable {
    void drive();
}
```

### 추상 클래스 예

```java
abstract class Vehicle {
    int speed;

    void setSpeed(int speed) {
        this.speed = speed;
    }

    abstract void drive();
}
```

---

## 🔄 요약 비교

| 기준     | 인터페이스       | 추상 클래스  |
| ------ | ----------- | ------- |
| 다중 상속  | 가능          | 불가능     |
| 상태(필드) | 가질 수 없음     | 가질 수 있음 |
| 기본 구현  | Java 8부터 가능 | 항상 가능   |
| 생성자    | 없음          | 있음      |
| 관계     | "할 수 있다"    | "이다"    |

---

### ✅ 실무 조언:

* **"서로 관련 없는 객체들에 공통 기능을 부여"** → 인터페이스
* **"공통된 속성과 동작이 있는 상속 구조"** → 추상 클래스

둘을 **동시에 사용할 수도 있고**, **하나의 추상 클래스가 여러 인터페이스를 구현**할 수도 있습니다.

</div>
</details>

<details>
<summary>왜 클래스는 단일 상속만 가능한데, 인터페이스는 2개 이상 구현이 가능할까요?</summary>
<div>

아주 좋은 질문입니다! 핵심은 \*\*"복잡성과 충돌을 방지하면서도 유연성을 제공하려는 설계 철학"\*\*에 있습니다.

---

## ✅ 클래스는 **단일 상속**만 가능한 이유

### ⚠️ 다중 상속의 문제점 (클래스 기준)

다중 상속은 **여러 부모 클래스에서 동일한 이름의 메서드나 필드**를 상속받을 경우, \*\*우선순위를 알기 어려워서 모호성(ambiguity)\*\*이 발생합니다.

예를 들어:

```java
class A {
    void hello() { System.out.println("Hello from A"); }
}

class B {
    void hello() { System.out.println("Hello from B"); }
}

// Java에서는 아래와 같은 다중 상속이 허용되지 않음
class C extends A, B { } // 오류 발생!
```

* 위 상황에서는 `C.hello()`가 A의 것인지 B의 것인지 **결정할 수 없음**.
* 이는 **Diamond Problem**이라고도 불립니다.

➡️ **이런 혼란을 피하기 위해 자바는 클래스에 대해 "단일 상속"만 허용**합니다.

---

## ✅ 인터페이스는 **다중 구현**이 가능한 이유

### ✔️ 인터페이스는 본질적으로 \*\*계약(명세)\*\*만 정의하기 때문

* 인터페이스는 **상태(필드)나 구현을 갖지 않는 추상적인 메서드 선언**만 포함했기 때문에, 충돌 가능성이 거의 없음.
* 즉, 클래스는 **구현과 상태를 상속**하지만, 인터페이스는 \*\*기능의 약속(형태)\*\*만 상속.

예:

```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {
    public void fly() { System.out.println("Flying"); }
    public void swim() { System.out.println("Swimming"); }
}
```

* Duck은 **두 인터페이스 모두의 기능을 명확하게 구현**하면 되기 때문에 충돌 없음.

---

## ✅ Java 8 이후: `default` 메서드로 생긴 잠재적 충돌

인터페이스에 `default` 메서드가 생기면서 충돌 가능성이 생겼지만, Java는 다음과 같이 **명시적 해결**을 요구합니다:

```java
interface A {
    default void hello() { System.out.println("Hello from A"); }
}

interface B {
    default void hello() { System.out.println("Hello from B"); }
}

class C implements A, B {
    public void hello() {
        A.super.hello(); // 또는 B.super.hello();
    }
}
```

➡️ 충돌 시 개발자가 **직접 우선순위를 지정**하게 강제함 → **안전성과 유연성 모두 확보**.

---

## 🧠 결론

| 구분         | 클래스 상속       | 인터페이스 구현         |
| ---------- | ------------ | ---------------- |
| 다중 상속 허용   | ❌            | ✅                |
| 이유         | 충돌 위험, 상태 상속 | 명세만 제공, 구현 충돌 없음 |
| Java 설계 철학 | 안전성 우선       | 유연성 확보           |

---

즉, **인터페이스는 충돌 없이 유연한 기능 조합이 가능하기 때문에 다중 구현을 허용하지만**, **클래스는 복잡한 상태와 구현을 포함하기 때문에 단일 상속만 허용되는 것**입니다.

</div>
</details>

### 리플렉션에 대해 설명해 주세요.

<details>
<summary></summary>
<div>

### 🔍 리플렉션의 핵심 개념

리플렉션을 사용하면 프로그램이 실행 중일 때 다음과 같은 작업이 가능합니다:

- 클래스의 이름, 메서드, 필드, 생성자 등의 **구조를 조회**
- **새로운 인스턴스 생성**
- **메서드 호출**
- **필드(변수) 값 읽기/쓰기**
- **어노테이션 읽기**

---

### 🛠️ 리플렉션의 예 (Java 기준)

```java
Class<?> clazz = Class.forName("com.example.MyClass");  // 클래스 로딩
Object obj = clazz.getDeclaredConstructor().newInstance();  // 인스턴스 생성
Method method = clazz.getMethod("sayHello");  // 메서드 조회
method.invoke(obj);  // 메서드 호출

```

---

### ⚠️ 리플렉션의 단점

1. **성능 저하** – 일반적인 코드보다 느립니다 (JIT 최적화 어려움)
2. **보안 이슈** – private 필드/메서드 접근 가능
3. **유지보수 어려움** – 코드 가독성 낮고 오류가 런타임까지 숨겨짐

---

### 🎯 리플렉션이 유용한 상황

- 프레임워크 개발 (예: 스프링, Hibernate)
- 테스트 도구 (예: JUnit)
- 의존성 주입 / 객체 직렬화 / JSON 매핑
</div>
</details>

<details>
<summary>의미만 들어보면 리플렉션은 보안적인 문제가 있을 가능성이 있어보이는데, 실제로 그렇게 생각하시나요? 만약 그렇다면, 어떻게 방지할 수 있을까요?</summary>
<div>

### 🔐 왜 리플렉션이 보안상 위험한가?

1. **접근 제한 무력화**
    
    `private`, `protected` 멤버에도 `setAccessible(true)`를 통해 접근 가능:
    
    ```java
    Field field = clazz.getDeclaredField("secret");
    field.setAccessible(true);  // 접근 제한 해제
    
    ```
    
2. **코드 무결성 훼손**
    
    원래 의도하지 않은 방식으로 객체를 조작해 버그나 취약점을 만들 수 있음.
    
3. **샌드박스 우회**
    
    제한된 환경(예: JVM 샌드박스, 웹브라우저)에서도 시스템 자원에 접근할 수 있게 될 수 있음.
    
4. **민감한 정보 노출**
    
    예: 인증 토큰, 비밀번호, 내부 설정 등을 리플렉션으로 조회 가능
    

---

### 🛡️ 리플렉션 관련 보안 위험을 방지하는 방법

1. **보안 매니저(Security Manager) 사용** *(Java 환경)*
    
    리플렉션으로 접근할 수 있는 범위를 제어할 수 있었지만, Java 17 이후 deprecated됨.
    
2. **모듈 시스템 활용 (Java 9+)**
    
    `module-info.java`를 통해 특정 패키지나 클래스를 외부에서 접근 불가하도록 선언.
    
3. **접근 권한 체크**
    
    `setAccessible(true)`는 보안 매니저나 정책에 따라 제한 가능.
    
4. **코드 리뷰와 정적 분석 도구 활용**
    
    리플렉션 사용이 포함된 부분을 별도로 검토하고, 정적 분석 도구(예: SonarQube)로 감시.
    
5. **라이브러리 사용 주의**
    
    외부 라이브러리가 리플렉션을 남용할 수 있으므로, 신뢰할 수 있는 라이브러리만 사용.
    
6. **최대한 사용 자제**
    
    리플렉션은 꼭 필요한 경우에만 사용하고, 가능한 대체 방법(예: 인터페이스, 전략 패턴 등)을 고려.
    

---

### ✅ 결론

> 리플렉션은 강력하지만, 잘못 사용하면 보안상 매우 위험한 도구입니다.
> 
> 
> 보안 정책과 설계 원칙에 따라 신중하게 사용하고, **사전 통제**와 **사후 모니터링**이 필요합니다.
>
</div>
</details>

<details>
<summary>리플렉션을 언제 활용할 수 있을까요?</summary>
<div>

리플렉션(reflection)은 **일반적인 개발에서는 가급적 피하는 것이 원칙**이지만, 특정 상황에서는 **다른 방법으로는 해결하기 어려운 유용한 도구**가 됩니다. 아래는 **리플렉션을 사용해도 되는(혹은 사용하는 게 적절한) 대표적인 상황들**입니다.

---

## ✅ 리플렉션을 사용해도 되는/사용이 적절한 상황

---

### 1. **프레임워크나 라이브러리 개발**

- **목적**: 사용자의 클래스 구조를 몰라도 자동으로 DI(의존성 주입), 애노테이션 처리, 설정 바인딩 등을 구현
- **예시**: Spring, Hibernate, Jackson, JUnit 등
- **사용 이유**: 클래스나 필드 정보를 런타임에 동적으로 해석해야 하기 때문

```java
Field field = clazz.getDeclaredField("username");
field.setAccessible(true);
field.set(instance, "admin");

```

---

### 2. **직렬화/역직렬화 처리**

- **목적**: 객체를 JSON, XML 등으로 변환하거나 다시 복원할 때
- **예시**: Gson, Jackson 등이 객체의 필드를 리플렉션으로 탐색해서 값 변환

---

### 3. **애노테이션 기반 처리**

- **목적**: 특정 애노테이션이 붙은 클래스/메서드/필드를 찾아 기능 실행
- **예시**: 테스트 메서드(@Test) 실행, 커스텀 컨트롤러 등록 등

```java
if (method.isAnnotationPresent(MyAnnotation.class)) {
    method.invoke(obj);
}

```

---

### 4. **플러그인 시스템, 모듈 로딩**

- **목적**: 클래스 이름을 문자열로 받아서 동적으로 로딩 및 실행
- **예시**: 게임 모드 시스템, 스크립트 기반 확장

```java
Class<?> pluginClass = Class.forName("com.game.plugins.SpeedBoost");
Plugin plugin = (Plugin) pluginClass.getDeclaredConstructor().newInstance();

```

---

### 5. **테스트 도구에서의 내부 접근**

- **목적**: 테스트 코드에서 private 필드나 메서드를 조작하고 검증할 필요가 있을 때
- **예시**: 단위 테스트에서 내부 상태 강제 설정

---

### 6. **로깅 및 디버깅 도구**

- **목적**: 런타임 객체의 구조를 분석해서 콘솔에 출력하는 등의 작업

---

## ⚠️ 사용을 피해야 할 일반적인 상황

| 상황 | 대체 가능 | 이유 |
| --- | --- | --- |
| 단순 메서드 호출 | O (직접 호출) | 리플렉션은 느리고 복잡 |
| 일반 필드 접근 | O | 유지보수 어려움 |
| 로직 제어 | O (패턴 활용) | 코드 구조가 불투명해짐 |

---

## 🔑 요약

> 리플렉션은 "플러그인처럼 유연한 구조"가 필요한 도구적 환경에서만 사용하는 것이 원칙입니다.
> 애플리케이션 로직 자체에 남용해서는 안 됩니다.
</div>
</details>

### static class와 static method를 비교

<details>
<summary></summary>
<div>

## 📘 개념 비교: `static class` vs `static method`

| 항목 | `static class` | `static method` |
| --- | --- | --- |
| **정의 위치** | 클래스 안에 중첩(nested)되어 정의 | 클래스 내부의 정적 메서드 |
| **사용 주체** | **클래스 자체**가 소유 (인스턴스 불필요) | **클래스 이름**으로 직접 호출 가능 |
| **주로 사용되는 경우** | 유틸리티 클래스나 구조적 데이터 정리 목적 | 헬퍼 함수, 공통 기능 등 |
| **인스턴스 변수 접근** | 외부 클래스 인스턴스에 접근 불가 | 인스턴스 변수에 접근 불가 |
| **언제 사용하나?** | 클래스 간 구조적 연결이 필요할 때 | 상태를 가지지 않는 동작(기능)만 구현할 때 |

---

## 🧱 예제 (Java 기준)

### 🔹 `static method` 예시

```java
public class MathUtils {
    public static int add(int a, int b) {
        return a + b;
    }
}

// 사용
int sum = MathUtils.add(3, 5);

```

- 객체 생성 없이 바로 호출 가능
- 상태를 가지지 않고 입력 → 출력만 존재

---

### 🔹 `static class` 예시

```java
public class Outer {
    static class Inner {
        void print() {
            System.out.println("Static nested class");
        }
    }
}

// 사용
Outer.Inner inner = new Outer.Inner();
inner.print();

```

- 외부 클래스 인스턴스 없이도 내부 클래스 생성 가능
- 외부 클래스의 **인스턴스 변수에는 접근 불가**

---

## 🧠 정리

| 상황 | 사용 추천 |
| --- | --- |
| 기능(행위)만 구현, 상태가 필요 없음 | `static method` |
| 구조상 클래스 내부에 또 다른 클래스를 정의하고 싶음 | `static class` |
| 내부 클래스가 외부 클래스 인스턴스 필요 | **비-static class** 사용 |
</div>
</details>

### Java의 Exception에 대해 설명해 주세요.
<details>
<summary></summary>
<div>

Java에서 `Exception`(예외)은 **프로그램 실행 중 발생할 수 있는 오류 상황을 객체로 표현한 것**입니다. 예외 처리를 통해 프로그램은 **예상치 못한 문제를 중단하지 않고 안정적으로 대응**할 수 있습니다.

---

## ✅ 1. Exception의 분류

Java에서는 `Throwable` 클래스가 **예외 계층의 최상위**입니다. 그 하위로 두 가지 주요 분류가 있습니다:

```
Throwable
 ├── Error             // 시스템 오류 (OutOfMemoryError 등 → 처리 불가)
 └── Exception         // 일반적인 예외 (처리 가능)
      ├── CheckedException   // 반드시 처리해야 하는 예외
      └── UncheckedException // 런타임 예외 (선택적 처리)

```

| 유형 | 설명 | 예시 |
| --- | --- | --- |
| ✅ Checked Exception | 컴파일 시점에 예외 처리 필요 | `IOException`, `SQLException` |
| ⚠️ Unchecked Exception (RuntimeException) | 컴파일러가 강제하지 않음 | `NullPointerException`, `IndexOutOfBoundsException` |

---

## ✅ 2. 예외 처리 방법

### 🔹 try-catch-finally

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("0으로 나눌 수 없습니다.");
} finally {
    System.out.println("항상 실행됩니다.");
}

```

### 🔹 throws

메서드 호출자에게 예외 처리를 **위임**할 수 있습니다.

```java
public void readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);
}

```

---

## ✅ 3. 사용자 정의 예외

자신만의 예외를 만들어서 의미 있는 오류를 표현할 수 있습니다.

```java
public class InvalidUserException extends Exception {
    public InvalidUserException(String message) {
        super(message);
    }
}

```

사용 시:

```java
if (!user.isValid()) {
    throw new InvalidUserException("유효하지 않은 사용자입니다.");
}

```

---

## ✅ 4. 예외 vs 오류 (Error)

| 항목 | Exception | Error |
| --- | --- | --- |
| 목적 | 예외적 상황에 대한 **복구 가능성** 고려 | JVM 내부 오류 등 **복구 불가능** |
| 예시 | `FileNotFoundException`, `IllegalArgumentException` | `OutOfMemoryError`, `StackOverflowError` |
| 처리 | try-catch or throws | 보통 처리하지 않음 |

---

## ✅ 5. 실무 팁

- **예외 메시지는 구체적으로**: 로그 분석 시 원인 파악이 쉬움
- **Exception wrapping**: 다른 계층으로 전달할 때 `throw new CustomException("...", cause)` 형태로 감싸면 stack trace 보존 가능
- **최대한 구체적인 예외로 catch**: `catch (Exception e)`는 가급적 피함
- **예외 남용 주의**: 제어 흐름에 예외를 쓰는 건 성능과 가독성에 좋지 않음

---

### 🔚 정리

> Java의 Exception은 안정적인 프로그램 실행을 위한 핵심 도구입니다.
> 
> 
> 적절히 **예외를 구분하고**, **처리 전략을 세우며**, **사용자 정의 예외로 도메인을 표현**하면
> 
> 견고하고 유지보수성 높은 코드를 만들 수 있습니다.
>
</div>
</details>

<details>
<summary>예외처리를 하는 세 방법에 대해 설명해 주세요.</summary>
<div>

Java에서 예외를 처리하는 **대표적인 3가지 방법**은 다음과 같습니다:

---

### ✅ 1. `try-catch` 문 사용

예외 발생이 예상되는 코드를 `try` 블록에 작성하고, 예외 발생 시 실행할 코드를 `catch` 블록에 작성합니다.

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("예외 발생: " + e.getMessage());
}

```

- **특징**: 예외를 직접 처리하여 프로그램 중단 없이 흐름을 유지
- **사용 시점**: 예외가 발생할 수 있는 위치에서 **즉시 처리**해야 할 때

---

### ✅ 2. `throws` 키워드로 위임

메서드 선언부에서 `throws` 키워드를 사용해 예외 처리를 **호출자에게 위임**합니다.

```java
public void readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);
}

```

- **특징**: 호출한 쪽에서 예외를 처리하게 하여 책임을 분리
- **사용 시점**: 메서드 내부에서 예외 처리하기 적절치 않을 때

---

### ✅ 3. `try-catch-finally` 또는 `try-with-resources`

예외가 발생하든 안 하든 **항상 실행**해야 하는 코드를 `finally`에 작성하거나, 리소스를 안전하게 자동 닫기 위해 `try-with-resources`를 사용합니다.

### ✅ `finally` 예시:

```java
FileInputStream fis = null;
try {
    fis = new FileInputStream("file.txt");
    // 파일 처리
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (fis != null) {
        try {
            fis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

### ✅ `try-with-resources` 예시:

```java
try (FileInputStream fis = new FileInputStream("file.txt")) {
    // 파일 처리
} catch (IOException e) {
    e.printStackTrace();
}

```

- **특징**: 자원 정리를 자동으로 하거나, 항상 실행되어야 할 코드를 보장
- **사용 시점**: 파일, 네트워크 등 **자원 관리**가 필요한 경우

---

### 🔚 요약

| 방법 | 사용 목적 | 사용 시점 |
| --- | --- | --- |
| `try-catch` | 예외를 즉시 처리 | 직접 처리 필요할 때 |
| `throws` | 예외를 위임 | 호출자에게 책임 넘길 때 |
| `finally` / `try-with-resources` | 리소스 정리 보장 | 파일/DB/네트워크 등 |

---

**💡 실무 팁**: 예외는 무조건 잡기보다는, **의미 있는 위치에서 적절히 처리하거나 위임**하는 게 중요합니다.
</div>
</details>

<details>
<summary>CheckedException, UncheckedException 의 차이에 대해 설명해 주세요.</summary>
<div>

## ✅ Checked Exception

| 특징 | 내용 |
| --- | --- |
| 컴파일 시점 | **컴파일러가 강제로 처리** 요구 (`try-catch` 또는 `throws`) |
| 상속 관계 | `Exception`을 상속하지만, `RuntimeException`은 **제외** |
| 예시 | `IOException`, `SQLException`, `ParseException` 등 |
| 목적 | 예외 처리를 강제해서 **신뢰성** 높이기 위함 |

### ✔ 예:

```java
public void readFile(String path) throws IOException {
    FileReader reader = new FileReader(path); // 컴파일 에러 발생 → 예외 처리 필요
}

```

---

## ✅ Unchecked Exception

| 특징 | 내용 |
| --- | --- |
| 컴파일 시점 | **컴파일러가 처리 강제하지 않음** |
| 상속 관계 | `RuntimeException` 및 그 하위 클래스 |
| 예시 | `NullPointerException`, `IllegalArgumentException`, `IndexOutOfBoundsException` 등 |
| 목적 | **프로그래밍 로직 오류** → 개발자가 코드 수정으로 해결해야 함 |

### ✔ 예:

```java
public int divide(int a, int b) {
    return a / b; // b가 0이면 ArithmeticException 발생 (언체크드지만 컴파일 통과)
}

```

---

## ✅ 차이 요약

| 구분 | 체크드 예외 | 언체크드 예외 |
| --- | --- | --- |
| 강제 처리 | O (`try-catch` 또는 `throws`) | X |
| 컴파일러 검사 | O | X |
| 상속 관계 | `Exception` (단, `RuntimeException` 제외) | `RuntimeException` 및 하위 |
| 용도 | 외부 문제 (파일, DB 등) | 프로그래밍 실수 |
| 예 | `IOException`, `SQLException` | `NullPointerException`, `ArrayIndexOutOfBoundsException` |

---

## ✅ 결론

- **체크드 예외**: 발생 가능성이 높고, 회복 가능한 외부 문제
    
    → "파일이 없을 수도 있으니 대비하라"는 메시지
    
- **언체크드 예외**: 주로 프로그래머 실수 (버그)
    
    → "이건 네가 코드 제대로 짜야 한다"는 의미
    

---

실무에서는 **체크드 예외를 싫어하는 개발자도 많지만**, 외부와의 인터페이스가 많거나 안정성이 중요한 시스템에서는 여전히 유용하게 사용됩니다.
</div>
</details>

<details>
<summary>예외처리가 성능에 큰 영향을 미치나요? 만약 그렇다면, 어떻게 하면 부하를 줄일 수 있을까요?</summary>
<div>

**Java의 예외 처리(Exception Handling)는 성능에 영향을 미칠 수 있습니다.**

하지만 중요한 건 "예외 발생 자체"가 느린 것이지, "try-catch 구문이 있다고 해서 성능이 떨어지지는 않는다"는 점입니다.

---

## ✅ 예외 처리와 성능 영향

### ✔ 영향이 있는 부분

- **예외가 실제로 발생**할 때 → **스택 트레이스(stack trace)** 생성, **예외 객체 생성**, **컨트롤 흐름 변경** 등으로 CPU 비용이 큽니다.
- 반복문이나 실시간 처리 코드에서 예외가 **자주 발생**하면 성능 저하가 뚜렷해집니다.

### ✔ 영향이 없는 부분

- 단순히 `try-catch` 구문을 **사용만 했을 때는 성능에 큰 영향 없음**
    
    → 예외가 **발생하지 않으면** 거의 비용이 없습니다.
    

---

## ✅ 부하를 줄이는 방법

| 방법 | 설명 |
| --- | --- |
| ❌ 예외를 **흐름 제어 용도로 사용하지 말 것** | 예: `NumberFormatException`을 try-catch로 감싸고 매번 검사하는 대신, `isDigit` 같은 사전 검사를 사용 |
| ✅ 예외 발생 가능성을 **사전에 방지** | null 체크, 조건문 등으로 예외 발생 조건을 줄임 |
| ✅ 반복문 내부에서는 특히 주의 | 루프 안에서 예외가 반복 발생하면 성능 저하 큼 |
| ✅ 필요한 경우 로그 레벨 조절 | 스택 트레이스를 매번 출력하면 I/O 비용도 증가함 |
| ✅ `try-with-resources` 사용 | 자원 누수를 방지하면서도 깔끔하게 처리 가능 |
| ✅ 예외 클래스 사용을 최적화 | 자주 발생하는 예외는 커스텀 예외로 만들어 필요한 정보만 담고, 스택 트레이스 생성을 제한하는 방식도 고려 가능 (`fillInStackTrace()` 오버라이딩) |

---

## ✅ 예: 흐름 제어에 예외를 쓰면 안 되는 경우

❌ **안 좋은 예 (예외를 조건 분기처럼 사용)**:

```java
for (String s : list) {
    try {
        int num = Integer.parseInt(s);
    } catch (NumberFormatException e) {
        // 무시
    }
}

```

✅ **좋은 예 (예외 자체를 사전에 방지)**:

```java
for (String s : list) {
    if (s.matches("\\d+")) {
        int num = Integer.parseInt(s);
    }
}

```

---

## ✅ 결론

- 예외 처리는 반드시 필요하지만, **예외 발생을 피하는 설계가 더 중요**합니다.
- 특히, 예외가 **빈번히 발생하는 구조라면 반드시 리팩토링**을 고려해야 합니다.
- 실무에서는 예외 발생은 **"정상적이지 않은 흐름"**이라는 점을 기억하세요.

필요하다면 GC, JFR(Java Flight Recorder) 또는 벤치마크 도구로 실측해보는 것도 좋은 접근입니다.
</div>
</details>

### Synchronized 키워드에 대해 설명해 주세요.
<details>
<summary></summary>
<div>

자바의 `synchronized` 키워드는 **동시성 환경에서 공유 자원에 대한 상호 배제를 보장**하기 위해 사용됩니다. 크게 두 가지 용도로 나뉩니다.

---

## 1. 메서드 동기화 (Method Synchronization)

```java
public synchronized void increment() {
    counter++;
}
```

* **대상:** 인스턴스 메서드에 붙이면 해당 객체의 **인스턴스 락**(intrinsic lock, 모니터)을 획득합니다.
* **동작:** 한 스레드가 이 메서드를 호출해 락을 획득하면, 같은 객체의 다른 `synchronized` 메서드는 락이 풀릴 때까지 진입 대기합니다.
* **static 메서드 동기화:** `public static synchronized void foo()` 형태로 쓰면 **클래스 객체**(`Class<?>` 인스턴스)에 대한 락을 사용합니다.

---

## 2. 블록 동기화 (Block Synchronization)

```java
public void transfer(Account from, Account to, int amount) {
    // from 객체의 락만 걸기
    synchronized (from) {
        // critical section: from.balance 변경
        from.withdraw(amount);
    }
    // to 객체의 락만 걸기
    synchronized (to) {
        to.deposit(amount);
    }
}
```

* **유연성:** 메서드 전체가 아니라 필요한 코드 영역만 동기화할 수 있어, 락 경합(lock contention)을 줄이고 성능을 개선할 수 있습니다.
* **임의 객체 락:** `synchronized(this)`, `synchronized(SomeClass.class)`, 또는 임의의 `new Object()` 등 어떤 객체도 모니터로 사용 가능합니다.

---

## 3. 동작 원리

1. **모니터 엔터(Monitor Enter):** 동기화 지점(`synchronized`) 도달 시 JVM이 해당 객체의 모니터 락을 획득하려 시도합니다.
2. **블록 실행:** 락 획득 성공 스레드는 블록(또는 메서드) 내부를 실행합니다.
3. **모니터 엑시트(Monitor Exit):** 블록 종료(정상/예외) 시 JVM이 락을 해제합니다.

JVM은 `monitorenter` / `monitorexit` 바이트코드로 이를 구현하며, 락 해제는 `finally` 블록처럼 보장됩니다.

---

## 4. 재진입성 (Reentrancy)

* 자바의 락은 **재진입 가능(Reentrant)** 합니다.
* 같은 스레드가 이미 획득한 락을 다시 얻으려고 해도 데드락 없이 획득 카운트만 올라갑니다.
* 예시:

  ```java
  public synchronized void outer() {
      inner();  // 같은 객체의 synchronized 메서드 호출
  }

  public synchronized void inner() {
      // outer()에서 획득한 락을 그대로 사용
  }
  ```

---

## 5. 주의할 점

1. **과도한 동기화 범위**

   * 메서드 전체를 `synchronized`로 걸면, 불필요하게 성능 병목이 생길 수 있습니다.
   * 가능한 한 **최소 영역**만 동기화하세요.

2. **교착 상태(Deadlock)**

   * 다중 락을 취할 때 순서를 헷갈리면 데드락이 발생합니다.
   * 락 취득 순서를 일관되게 관리하거나, 타임아웃을 검토하세요.

3. **락 경합(Lock Contention)과 성능**

   * 락 걸린 코드가 길면 많은 스레드가 대기하면서 전체 처리량이 떨어집니다.
   * `java.util.concurrent.locks` 패키지의 `ReentrantLock`·`ReadWriteLock`·`StampedLock` 등의 고급 락을 고려해 보세요.

---

## 6. 대안: java.util.concurrent

* **`ReentrantLock`**: 락 획득 대기 중 인터럽트 가능, 타임아웃, 공정성 옵션 제공
* **`ReadWriteLock`**: 읽기-쓰기 횟수 분리 → 읽기 락은 동시 다중 허용, 쓰기 락은 배타적
* **`StampedLock`**: 낙관적 읽기(optimistic read) 지원으로 성능 향상

---

### 요약

* `synchronized`는 **자바 기본 동기화 메커니즘**으로, 간단히 “메서드 전체” 또는 “블록 일부”에 락을 걸어 스레드 안전(thread safety)을 확보합니다.
* **재진입 가능**하고 JVM 레벨에서 자동으로 락 관리(`monitorenter`/`monitorexit`)를 해 주지만, **성능 병목**과 **데드락** 위험이 있으므로 락 범위 최소화, 교착 회피 전략, 필요 시 `java.util.concurrent` 고급 락 활용이 필요합니다.

</div>
</details>

<details>
<summary>Synchronized 키워드가 어디에 붙는지에 따라 의미가 약간씩 변화하는데, 각각 어떤 의미를 갖게 되는지</summary>
<div>

Java의 `synchronized` 키워드는 “어떤 락(lock)에 걸어 동기화할 것인가”를 지정하는 역할을 합니다. 붙이는 위치에 따라 잡는 락의 종류와 범위가 달라지고, 따라서 의미와 동작 방식도 약간씩 달라집니다. 크게 네 가지 경우로 나눌 수 있습니다.

---

## 1. 인스턴스 메서드에 붙일 때

```java
public synchronized void foo() { … }

```

- **잡는 락**: 이 메서드를 호출한 *인스턴스 객체(this)* 의 모니터 락
- **동작 범위**: 해당 객체의 다른 `synchronized` 인스턴스 메서드들과 상호 배제(Mutex)
- **의미**:
    - 스레드 A가 `obj.foo()` 진입 시 `obj`의 락을 획득
    - 락을 해제하기 전까지 `obj`의 다른 synchronized 인스턴스 메서드 진입(예: `obj.bar()`)은 블록됨
    - 다른 인스턴스(`obj2`)의 synchronized 메서드는 영향 없음

---

## 2. 정적(static) 메서드에 붙일 때

```java
public static synchronized void bar() { … }

```

- **잡는 락**: 해당 클래스의 `Class` 객체 메타 모니터(예: `MyClass.class`)
- **동작 범위**: 같은 클래스에 선언된 *모든* static `synchronized` 메서드들끼리 상호 배제
- **의미**:
    - 스레드 A가 `MyClass.bar()` 진입 시 `MyClass.class` 락 획득
    - 해제 전까지 `MyClass` 내 다른 모든 static synchronized 메서드 진입은 블록됨
    - 인스턴스 메서드의 synchronized와는 완전히 독립된 락

---

## 3. 인스턴스 단위의 블록 동기화

```java
synchronized(this) {
    // critical section
}

```

- **잡는 락**: `this` 객체의 모니터 락 (인스턴스 메서드 synchronized와 동일)
- **동작 범위**: 블록 `{}` 안에서만 동기화 적용
- **의미**:
    - 더 세밀한 영역(critical section)만 묶어서 락을 걸고 싶을 때 사용
    - 필요하다면 특정 필드를 가진 객체를 락 대상으로 지정할 수도 있음
        
        ```java
        synchronized(someSharedObject) { … }
        
        ```
        

---

## 4. 클래스 단위의 블록 동기화

```java
synchronized(MyClass.class) {
    // critical section for class-wide lock
}

```

- **잡는 락**: `MyClass.class` 객체의 모니터 락 (static synchronized와 동일)
- **동작 범위**: 해당 블록 안에서만 적용
- **의미**:
    - static 메서드 synchronized보다 더 작은 영역만 클래스 단위 락을 걸고 싶을 때
    - 클래스 전체(static) 리소스 접근 시 사용

---

### 요약 비교

| 위치 | 잡는 락 대상 | 적용 범위 |
| --- | --- | --- |
| `synchronized void m()` | `this` | 해당 인스턴스의 모든 synchronized 인스턴스 메서드 |
| `static synchronized void m()` | `MyClass.class` | 해당 클래스의 모든 static synchronized 메서드 |
| `synchronized(this){…}` | `this` | 블록 내부 |
| `synchronized(obj){…}` | `obj` | 블록 내부 |
| `synchronized(MyClass.class){…}` | `MyClass.class` | 블록 내부 |

각각 “어떤 락”을 걸 것인지(인스턴스 vs 클래스, 메서드 전체 vs 특정 블록)와 “동기화 대상”이 달라진다고 이해하시면 됩니다.
</div>
</details>

<details>
<summary>호율적인 코드 작성 측면에서, Synchronized는 좋은 키워드일까</summary>
<div>

`synchronized` 키워드는 자바에서 가장 기본적인 동기화 수단으로, 사용법이 직관적이고 JVM 수준에서 락 관리(바이어스 락, 경량 락 등) 최적화가 이루어지기 때문에 “무조건 나쁘다”고 할 수는 없습니다. 다만 **효율적인(throughput이 높고 지연(latency)이 낮은) 코드**를 작성하려면 몇 가지 고려할 점이 있습니다.

---

## 장점

1. **간단하고 직관적**
    - 메서드 선언부나 블록 앞에 붙이기만 하면 되므로, 복잡한 락 획득/해제 코드를 직접 작성할 필요가 없습니다.
2. **JVM 최적화**
    - 자바 6 이후로 바이어스 락(Biased Locking), 경량 락(Lightweight Locking), 헤비급 락(Heavyweight Locking) 사이 자동 전환이 일어나 락 획득/해제 오버헤드를 줄여 줍니다.

---

## 단점 및 한계

1. **컨텐션(Contention)에 취약**
    - 여러 스레드가 동일 락을 경쟁할 때 모니터 진입 대기(queue)가 길어져 전체 처리량이 급격히 떨어질 수 있습니다.
2. **범위 관리가 어렵다**
    - 메서드 전체를 락으로 걸면 사실상 “하나의 작업 단위”가 되는 코드가 길어져서, 실제로는 동기화가 필요 없는 코드까지 묶이기 쉽습니다.
3. **대체 수단에 비해 유연성 부족**
    - `ReadWriteLock`, `StampedLock`, `Lock` 인터페이스, 원자 클래스(`AtomicInteger` 등)에 비해 읽기·쓰기 분리, 타임아웃 락 대기, 공정성(fairness) 설정 등의 고급 기능을 지원하지 않습니다.

---

## 효율적인 대안 및 권장 사용법

1. **최소한의 범위만 동기화**
    
    ```java
    // Bad: 메서드 전체
    public synchronized void addData(Data d) {
        // 데이터 변환 + I/O + 리스트 추가
    }
    
    // Good: 실제 공유자원 접근부만
    public void addData(Data d) {
        Data transformed = transform(d);
        ioOperation(transformed);
        synchronized(list) {
            list.add(transformed);
        }
    }
    
    ```
    
2. **java.util.concurrent 활용**
    - **`ConcurrentHashMap`, `CopyOnWriteArrayList`** 등 락 분할(sharding)된 컬렉션
    - **`ReentrantLock`**: tryLock, 타임아웃, 공정성 설정 가능
    - **`ReadWriteLock`**: 읽기 작업이 많고, 쓰기는 상대적으로 적을 때 유리
    - **`StampedLock`**: 낙관적 락(optimistic read)을 지원해 읽기 성능 극대화
3. **원자성 보장 클래스**
    - `AtomicInteger`, `AtomicReference`, `LongAdder` 등으로 단일 값 동기화
4. **불변 객체(Immutable Objects)**
    - 불변 객체는 아예 동기화가 필요 없으므로, 설계 단계에서 가능한 한 활용

---

## 결론

- **소규모 프로젝트나 간단한 동기화**: `synchronized` 만으로도 충분히 관리·유지보수가 쉽고 안전합니다.
- **높은 동시성·고성능이 요구되는 영역**: 컨텐션 감소, 세밀한 락 제어, 공정성·타임아웃 등의 기능이 필요한 경우 `java.util.concurrent` 패키지의 고급 락/컬렉션을 적극 활용하는 것이 좋습니다.

따라서 “synchronized가 무조건 나쁘다”기보다는, **적절한 상황**에서 **최소 범위**로 사용하고, **성능 병목**이 예상되면 더 유연한 동시성 도구로 대체하는 것이 **효율적인 코드 작성**의 핵심입니다.
</div>
</details>

<details>
<summary>Synchronized를 대체할 수 있는 자바의 다른 동기화 기번에 대해 설명</summary>
<div>

Java의 `synchronized`를 대체하거나 보완할 수 있는 주요 동기화(Concurrency) 기법 및 도구들을 정리하면 다음과 같습니다.

---

## 1. `java.util.concurrent.locks` 패키지

### 1.1 `ReentrantLock`

- **특징**
    - `synchronized`보다 세밀한 락 제어 가능 (tryLock, lockInterruptibly, 공정성(fairness) 옵션)
    - 락 획득 실패 시 즉시 돌아오는 `tryLock()` 제공
    - 블록 해제 시 반드시 `unlock()` 호출 필요 (finally 블록에서 해제 권장)
- **사용 예**
    
    ```java
    private final ReentrantLock lock = new ReentrantLock(true); // fair lock
    
    public void critical() {
        if (lock.tryLock()) {
            try {
                // 공유 자원 접근
            } finally {
                lock.unlock();
            }
        } else {
            // 락 획득 실패 시 대체 로직
        }
    }
    
    ```
    

### 1.2 `ReadWriteLock` / `ReentrantReadWriteLock`

- **특징**
    - 읽기(Read) 락과 쓰기(Write) 락을 분리
    - **다중 읽기**는 동시에 허용(read lock 공유), 쓰기 작업 시에는 단독 락(write lock)
    - 읽기 위주의 워크로드에서 성능 향상
- **사용 예**
    
    ```java
    private final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();
    
    public void read() {
        readLock.lock();
        try {
            // 읽기 전용 작업
        } finally {
            readLock.unlock();
        }
    }
    
    public void write() {
        writeLock.lock();
        try {
            // 쓰기 전용 작업
        } finally {
            writeLock.unlock();
        }
    }
    
    ```
    

### 1.3 `StampedLock`

- **특징**
    - `ReadWriteLock`과 유사하나, **낙관적 읽기(optimistic read)** 지원
    - 잠금 획득 없이도 먼저 읽기를 시도, 충돌 감지 후 필요 시 재시도
    - 읽기 성능 극대화
- **사용 예**
    
    ```java
    private final StampedLock sl = new StampedLock();
    private long data;
    
    public long optimisticRead() {
        long stamp = sl.tryOptimisticRead();
        long result = data;
        if (!sl.validate(stamp)) {
            stamp = sl.readLock();
            try {
                result = data;
            } finally {
                sl.unlockRead(stamp);
            }
        }
        return result;
    }
    
    public void write(long update) {
        long stamp = sl.writeLock();
        try {
            data = update;
        } finally {
            sl.unlockWrite(stamp);
        }
    }
    
    ```
    

---

## 2. 원자성(Atomic) 클래스

### 2.1 `java.util.concurrent.atomic` 패키지

- **주요 클래스**: `AtomicInteger`, `AtomicLong`, `AtomicReference`, `LongAdder`, `DoubleAccumulator` 등
- **특징**
    - CAS(Compare-And-Swap) 기반으로 락 없이 원자적 연산 보장
    - 단일 변수의 증가·감소, 비교 후 교체 등을 높은 성능으로 처리
- **사용 예**
    
    ```java
    private final AtomicInteger counter = new AtomicInteger(0);
    
    public void increment() {
        counter.incrementAndGet();
    }
    
    public boolean compareAndSetExample(int expect, int update) {
        return counter.compareAndSet(expect, update);
    }
    
    ```
    

---

## 3. 동시성 컬렉션

- **`ConcurrentHashMap`**
    - 내부적으로 세그먼트/버킷 단위 락 분할(sharding)
    - `synchronized` Map 대비 훨씬 높은 동시성 처리
- **`CopyOnWriteArrayList` / `CopyOnWriteArraySet`**
    - 쓰기 시 배열 전체를 복사하므로 읽기 위주 상황에 적합
    - iteration 중 안전하게 읽기 가능
- **`BlockingQueue` 계열** (`ArrayBlockingQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue` 등)
    - 생산자-소비자 패턴에서 대기(blocking)와 용량(capacity) 제어

---

## 4. 동기화 보조 도구들 (`java.util.concurrent`)

| 도구 | 용도 |
| --- | --- |
| `Semaphore` | 지정된 허용 개수만큼 동시 진입 허용 (리소스 풀 등) |
| `CountDownLatch` | N개의 이벤트(스레드)가 완료될 때까지 대기 |
| `CyclicBarrier` | 정해진 개수의 스레드가 모두 도달할 때까지 대기 → 동시에 출발(start) |
| `Phaser` | 재사용 가능한 barrier, dynamic한 파티션 크기 조정 가능 |
| `Exchanger` | 두 스레드 간 데이터 교환 |

---

## 5. 불변 객체(Immutable Objects)와 함수형 스타일

- **불변 객체**는 설계 단계에서 상태 변경을 제거하여 락 자체가 불필요
- Java의 `record`, `final` 필드, 라이브러리(Guava의 `ImmutableList` 등)를 활용
- 부작용(side-effect)을 최소화하는 함수형 프로그래밍 패턴 도입

---

## 정리

| 기법 | 장점 | 단점 |
| --- | --- | --- |
| `synchronized` | 간단·직관, JVM 최적화 내장 | 세밀한 제어 부족, 블록 범위 관리 어려움 |
| `ReentrantLock` | 공정성, 인터럽트 가능한 락, tryLock 등 세밀 제어 | finally 블록 해제 코딩 필요 |
| `ReadWriteLock` | 읽기/쓰기 분리 → 읽기 성능 향상 | 구현 복잡도 약간 증가 |
| `StampedLock` | 낙관적 읽기 지원 → 더 높은 읽기 처리량 | validate 후 재시도 로직 필요 |
| `AtomicXxx` | 락 없는 원자 연산 → 높은 성능 | 단일 변수 수준, 복합 연산에선 부적합 |
| 동시성 컬렉션·동기화 보조 도구 | 생산자-소비자, 배리어 등 고수준 동시성 패턴 지원 | 쓰기 비용, 용도별 학습 필요 |
| 불변 객체 | 아예 동기화 불필요 | 설계 복잡도 증가, 객체 생성 비용 |

애플리케이션의 **요구사항**(읽기/쓰기 비율, 지연 허용치, 공정성 여부 등)에 따라 위 도구들을 적절히 조합·선택하시면 `synchronized`만 사용할 때보다 **성능**과 **유연성** 모두 개선할 수 있습니다.
</div>
</details>