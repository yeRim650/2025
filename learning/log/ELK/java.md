## 목차

1. [개요](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EA%B0%9C%EC%9A%94)
2. [공통 준비사항](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EA%B3%B5%ED%86%B5-%EC%A4%80%EB%B9%84%EC%82%AC%ED%95%AD)
3. [방법 A: Logback + `logstash-logback-encoder`](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EB%B0%A9%EB%B2%95-a-logback--logstash-logback-encoder)
4. [방법 B: Log4j2 + Logstash Layout](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EB%B0%A9%EB%B2%95-b-log4j2--logstash-layout)
5. [테스트 & 검증 체크리스트](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%ED%85%8C%EC%8A%A4%ED%8A%B8--%EA%B2%80%EC%A6%9D-%EC%B2%B4%ED%81%AC%EB%A6%AC%EC%8A%A4%ED%8A%B8)
6. [결론 & 선택 팁](https://chatgpt.com/?model=o4-mini-high&temporary-chat=true#%EA%B2%B0%EB%A1%A0--%EC%84%A0%ED%83%9D-%ED%8C%81)

---

## 개요

이 가이드라인은 Java/Spring Boot 애플리케이션에서 ELK 스택(Logstash → Elasticsearch → Kibana)에 **구조화된 JSON 로그**를 전송하는 두 가지 방법을 정리합니다.

- **방법 A**: Spring Boot 기본 로깅(Logback) + `logstash-logback-encoder` TCP appender
- **방법 B**: Log4j2 + Logstash JSON Layout + SocketAppender

각 방법별 의존성 추가, 설정 파일 예시, 실행 방법을 포함한 POC 환경을 제공합니다.

---

## 공통 준비사항

1. **ELK Docker Compose** (`docker-compose.yml`)
    
    ```yaml
    version: '3.8'
    services:
      elasticsearch:
        image: docker.elastic.co/elasticsearch/elasticsearch:8.6.3
        environment:
          - discovery.type=single-node
          - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
        ports:
          - "9200:9200"
      logstash:
        image: docker.elastic.co/logstash/logstash:8.6.3
        ports:
          - "5000:5000"
        volumes:
          - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
        depends_on:
          - elasticsearch
      kibana:
        image: docker.elastic.co/kibana/kibana:8.6.3
        environment:
          - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
        ports:
          - "5601:5601"
        depends_on:
          - elasticsearch
    
    ```
    
    ```bash
    # ELK 스택 기동
    docker-compose up -d
    
    ```
    
2. **Logstash 파이프라인** (`logstash.conf`)
    
    ```
    input {
      tcp { port => 5000 codec => json }
    }
    filter {
      # (선택) timestamp 변환, 필드 보강 등
    }
    output {
      elasticsearch {
        hosts => ["elasticsearch:9200"]
        index => "java-app-logs-%{+YYYY.MM.dd}"
      }
      stdout { codec => rubydebug }  # 개발 시 콘솔 디버그용
    }
    
    ```
    

---

## 방법 A: Logback + `logstash-logback-encoder`

1. **의존성 추가** (`build.gradle` 또는 `pom.xml`)
    
    ```groovy
    // Gradle
    dependencies {
      implementation 'org.springframework.boot:spring-boot-starter-web'
      implementation 'net.logstash.logback:logstash-logback-encoder:7.3'
    }
    
    ```
    
    ```xml
    <!-- Maven -->
    <dependency>
      <groupId>net.logstash.logback</groupId>
      <artifactId>logstash-logback-encoder</artifactId>
      <version>7.3</version>
    </dependency>
    
    ```
    
2. **`logback-spring.xml` 설정** (classpath 루트에 배치)
    
    ```xml
    <configuration>
      <springProfile name="!test">
        <!-- Console (JSON) -->
        <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
          <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder"/>
        </appender>
    
        <!-- Logstash TCP -->
        <appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
          <destination>localhost:5000</destination>
          <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder"/>
        </appender>
    
        <root level="INFO">
          <appender-ref ref="CONSOLE"/>
          <appender-ref ref="LOGSTASH"/>
        </root>
      </springProfile>
    </configuration>
    
    ```
    
3. **로깅 사용 예시** (`DemoApplication.java`)
    
    ```java
    package com.example.demo;
    
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    public class DemoApplication {
      private static final Logger log = LoggerFactory.getLogger(DemoApplication.class);
    
      public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
        log.info("Method A: Logback JSON Logger 시작",
                 Map.of("service", "spring-boot-logback", "version", "1.0"));
        try {
          throw new RuntimeException("테스트 예외");
        } catch (Exception e) {
          log.error("에러 발생!", e);
        }
      }
    }
    
    ```
    
4. **실행 & 확인**
    
    ```bash
    ./gradlew bootRun
    
    ```
    
    - Logstash(`docker logs logstash`)와 Kibana Discover에서 JSON 로그 필드 확인

---

## 방법 B: Log4j2 + Logstash Layout

1. **의존성 추가** (`build.gradle` 또는 `pom.xml`)
    
    ```groovy
    // Gradle
    dependencies {
      implementation 'org.apache.logging.log4j:log4j-core:2.17.1'
      implementation 'org.apache.logging.log4j:log4j-api:2.17.1'
      implementation 'com.vlkan:log4j2-logstash-layout:0.14'
    }
    
    ```
    
    ```xml
    <!-- Maven -->
    <dependency>
      <groupId>com.vlkan</groupId>
      <artifactId>log4j2-logstash-layout</artifactId>
      <version>0.14</version>
    </dependency>
    
    ```
    
2. **`log4j2.xml` 설정** (classpath 루트에 배치)
    
    ```xml
    <Configuration status="WARN">
      <Appenders>
        <!-- JSON 콘솔 -->
        <Console name="Console" target="SYSTEM_OUT">
          <LogstashLayout/>
        </Console>
        <!-- TCP Appender -->
        <Socket name="Logstash" host="localhost" port="5000" protocol="TCP">
          <LogstashLayout/>
        </Socket>
      </Appenders>
      <Loggers>
        <Root level="info">
          <AppenderRef ref="Console"/>
          <AppenderRef ref="Logstash"/>
        </Root>
      </Loggers>
    </Configuration>
    
    ```
    
3. **로깅 사용 예시** (`App.java`)
    
    ```java
    package com.example;
    
    import org.apache.logging.log4j.LogManager;
    import org.apache.logging.log4j.Logger;
    
    public class App {
      private static final Logger log = LogManager.getLogger(App.class);
    
      public static void main(String[] args) {
        log.info("Method B: Log4j2 Logstash Layout 시작",
                 Map.of("service", "java-log4j2", "env", "prod"));
        try {
          String s = null;
          s.length();
        } catch (Exception e) {
          log.error("NullPointerException 발생", e);
        }
      }
    }
    
    ```
    
4. **실행 & 확인**
    
    ```bash
    java -jar build/libs/your-app.jar
    
    ```
    
    - Logstash 및 Kibana에서 수신된 로그 확인

---

## 테스트 & 검증 체크리스트

- [ ]  `docker-compose ps` 로 ELK 스택 정상 기동 확인
- [ ]  방법 A 실행 시 Logstash가 JSON 수신 오류 없이 처리
- [ ]  방법 B 실행 시 Logstash가 JSON 수신 오류 없이 처리
- [ ]  Kibana Discover에 `java-app-logs-*` 인덱스 패턴 생성 후 필드별 검색·필터링
- [ ]  (선택) Grafana 연동, 대시보드 샘플 생성

---

## 결론 & 선택 팁

- **Spring Boot + Logback** 환경이라면 → **방법 A**
    - Spring 프로퍼티/프로파일 연동이 쉽고, `logback-spring.xml` 한 곳에서 관리
- **다양한 Java 애플리케이션**(Spring이 아니거나 기존 Logback 미사용)이라면 → **방법 B**
    - Log4j2 설정만으로 모든 Java 앱에 동일하게 적용 가능

필요에 따라 두 방법을 비교·검증한 뒤,

- “기존 로깅 프레임워크와의 호환성”
- “포맷 커스터마이징 수준”
- “설정 복잡도”