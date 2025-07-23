## 1. DataSource 추상화 vs 직접 JDBC

* **직접 `DriverManager.getConnection()`**: 프로토타이핑이나 정말 단순한 스크립트 수준에만 권장. 커넥션 풀, 장애 복구, 보안, 모니터링 불가능.
* **`javax.sql.DataSource`**: JNDI, 커넥션 풀, Spring 등 모든 프레임워크에서 쓰는 표준 추상화. `DataSource` → `Connection` 획득 → `PreparedStatement` 실행 → `close()` 시 실제로 커넥션 풀로 반납.

```java
// 순수 Java 예시
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:postgresql://db.example.com:5432/appdb");
config.setUsername("app_user");
config.setPassword("encrypted-or-vault-retrieved-pw");
config.setMaximumPoolSize(50);
config.setMinimumIdle(10);
config.setConnectionTimeout(3000);
config.setLeakDetectionThreshold(2000);
config.setValidationTimeout(1000);
// 추가 튜닝 옵션…
HikariDataSource ds = new HikariDataSource(config);

try (Connection conn = ds.getConnection()) {
    // work...
}
```

## 2. 커넥션 풀 선택 & 튜닝

1. **HikariCP**: 경량·고성능, 대부분 벤치마크 1위  
2. Apache DBCP2, C3P0: 레거시 시스템 호환 시 사용

### HikariCP 핵심 튜닝

* `maximumPoolSize`: 동시 최대 커넥션 수 (CPU 코어 ×2~4, OLTP 특성 고려)  
* `minimumIdle`: 풀의 최소 유휴 커넥션  
* `connectionTimeout`: 커넥션 풀에서 빌드 대기 최대 시간(밀리초)  
* `idleTimeout`: 유휴 커넥션 반납 기준  
* `maxLifetime`: 커넥션 교체 주기 (DB 세션 유휴 타임아웃보다 작게)  
* `leakDetectionThreshold`: 커넥션 누수 감지 임계치  

```java
config.setMaximumPoolSize(100);
config.setMinimumIdle(20);
config.setConnectionTimeout(5000);         // 5초
config.setIdleTimeout(600000);             // 10분
config.setMaxLifetime(1800000);            // 30분
config.setLeakDetectionThreshold(2000);    // 2초 이상 반납 안 하면 경고
```

## 3. 환경별 설정 분리 & 보안

* **외부 프로퍼티/암호화**: Vault, AWS Secrets Manager, GCP Secret Manager 등으로 암호화 키 관리  
* **Spring @ConfigurationProperties** + **`spring.profiles.active`** 로 dev/stage/prod 분리  
* **JNDI 바인딩**: WAS(Tomcat, WildFly 등)에서 DataSource 정의 후 애플리케이션은 JNDI lookup만

```yaml
# application-prod.yml
spring:
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    hikari:
      jdbc-url: ${DB_URL}
      username: ${DB_USER}
      password: ${DB_PASS}
      maximum-pool-size: 80
      leak-detection-threshold: 1500
```

```java
@Configuration
@EnableConfigurationProperties(DatabaseProperties.class)
public class DataSourceConfig {
    @Bean
    @Primary
    public DataSource dataSource(DatabaseProperties prop) {
        HikariConfig cfg = new HikariConfig();
        cfg.setJdbcUrl(prop.getUrl());
        cfg.setUsername(prop.getUsername());
        cfg.setPassword(prop.getPassword());
        // ... 나머지 튜닝
        return new HikariDataSource(cfg);
    }
}
```

## 4. 메트릭·모니터링 연동

* **Micrometer + Prometheus + Grafana**: 커넥션 풀, 쿼리 타임, 스루풋, leak 감지 등  
* **JMX 노출**: 운영 중 MBeans로 실시간 상태 조회  
* **Health Check**: Spring Actuator `healthIndicator` + 커스텀 쿼리 (예: `SELECT 1`)

```java
// Micrometer Registry 자동 연동 (Spring Boot)
@Bean
public HikariDataSource hikariDataSource(HikariConfig config, MeterRegistry registry) {
    HikariDataSource ds = new HikariDataSource(config);
    ds.setMetricRegistry(new DropwizardMetricRegistryBridge(registry));
    return ds;
}
```

## 5. 고가용성 & 장애 대비

1. **Multi-AZ / Replica**

   * 읽기/쓰기 분리: **AbstractRoutingDataSource** 또는 MyBatis plugin  
   * R/W 분기 로직: 트랜잭션 어노테이션 기반으로 `Tx.readOnly()` 여부 판단  
2. **자동 재시도**

   * Spring Retry, Resilience4j CircuitBreaker/Retry 사용  
3. **ProxySQL / Pgpool-II**

   * DB 앞단에 논리 프록시 배치해 Read/Write 자동 분산 및 Failover 처리

```java
public class ReplicationRoutingDataSource extends AbstractRoutingDataSource {
    @Override
    protected Object determineCurrentLookupKey() {
        return TransactionSynchronizationManager.isCurrentTransactionReadOnly() 
               ? "replica" 
               : "primary";
    }
}
```

## 6. 다중 DataSource & 분산 트랜잭션

* **JTA / XA 트랜잭션**: Atomikos, Narayana, Bitronix  
* **Spring `@Transactional` with JtaTransactionManager`**  
* **Two-Phase Commit** 필요 시 활성화

```java
// Atomikos 예시
@Bean(initMethod = "init", destroyMethod = "close")
public DataSource xaDataSource() {
    AtomikosDataSourceBean xaDs = new AtomikosDataSourceBean();
    xaDs.setXaDataSourceClassName("org.postgresql.xa.PGXADataSource");
    Properties p = new Properties();
    p.setProperty("user", dbUser);
    p.setProperty("password", dbPass);
    p.setProperty("url", dbUrl);
    xaDs.setXaProperties(p);
    xaDs.setPoolSize(30);
    return xaDs;
}

@Bean
public PlatformTransactionManager txManager() {
    return new JtaTransactionManager();
}
```

## 7. 성능 최적화 팁

* **PreparedStatement 캐싱** (`cachePrepStmts=true`, `prepStmtCacheSize=250`, `prepStmtCacheSqlLimit=2048`)  
* **Fetch Size 조절**: 대용량 페이징, `stmt.setFetchSize(1000)`  
* **AutoCommit 비활성화**: 작은 트랜잭션 묶음으로 묶어 네트워크 왕복 줄이기  
* **트랜잭션 격리 수준**: 필요 최소한의 수준(READ_COMMITTED 권장)  
* **스키마 캐싱**: 드라이버 옵션(예: MySQL `cacheServerConfiguration=true`)

## 8. 스키마 관리 & 마이그레이션

* **Flyway / Liquibase** 연동  
* CI/CD 파이프라인에서 자동 적용, 롤백 스크립트 유지

```yaml
# application.yml 예시
spring:
  flyway:
    enabled: true
    locations: classpath:db/migration
    baseline-on-migrate: true
```

## 9. 테스트 전략

* **Testcontainers**: 실제 DB 컨테이너로 통합 테스트  
* **Embedded DB (H2, HSQL)**: 로컬 개발, 단위 테스트  
* **Mocking DataSource**: Mockito, Spring `@MockBean DataSource`

```java
@Testcontainers
@SpringBootTest
public class UserRepositoryTest {
    @Container
    public static PostgreSQLContainer<?> pg = 
        new PostgreSQLContainer<>("postgres:15")
          .withDatabaseName("testdb")
          .withUsername("sa")
          .withPassword("sa");

    @DynamicPropertySource
    static void dbProps(DynamicPropertyRegistry reg) {
        reg.add("spring.datasource.url", pg::getJdbcUrl);
        reg.add("spring.datasource.username", pg::getUsername);
        reg.add("spring.datasource.password", pg::getPassword);
    }
    // … 테스트 메서드
}
```

### 마무리

위 설정을 종합하면…

1. **순수 Java / JNDI / Spring** 어디서나 일관된 DataSource 추상화  
2. **HikariCP** 튜닝으로 TPS·응답속도 극대화  
3. **보안·모니터링** + **장애 대비** 로 프로덕션 안정성 확보  
4. **멀티 DS·분산 트랜잭션**까지 커버하는 엔터프라이즈 아키텍처

10년차 현업에서 요구하는 모든 시나리오(성능, 보안, 가용성, 운영성)를 반영한 ‘끝판왕’ DB 커넥션 설계 예시입니다. 필요에 맞게 골라 쓰시면 됩니다.
