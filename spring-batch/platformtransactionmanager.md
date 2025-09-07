## PlatformTransactionManager

`PlatformTransactionManager`는 스프링배치에서 트랜잭션의 시작, 커밋, 롤백 등 트랜잭션 생명주기를 관리하는 핵심 컴포넌트이다.

Spring Batch 5.0이후 JobRepository와 TransactionManager를 명시적으로 설정해야하는 것으로 바뀌었다.
그러하여, 이번에 알아보게 되었고 다음으로 주요 메서드에 대해서 알아보자.

### 주요 메서드
- `getTransaction()` : 트랜잭션 시작
- `commit()` : 성공적 종료
- `rollback()` : 실패시 롤백


### 구현체 예시
- `DataSourceTransactionManager` (JDBC)
  - `DataSource`를 기반으로 하는 트랜잭션 매니저, JDBC 직접 사용할떄 사용
  - 디비 커넥션 풀에서 Connection을 가져와 트랜잭션을 시작하고 커밋/롤백함
  - JDBC API 기반의 단순 SQL 쿼리를 처리할 때 적합하고 빠르다.
  - 트랜잭션 경계 내에서 `Connection`의 `setAutoCommmit(false)`, `commit()`, `rollback()`을 사용하여 미리 정의된 트랜잭션 설정을 적용함.
  - 여러 데이터 소스를 사용하게 되면 각 데이터 소스마다 별도의 트랜잭션 관리가 필요할 수 있다.

```java
@Bean
public DataSourceTransactionManager transactionManager(DataSource dataSource) {
    return new DataSourceTransactionManager(dataSource);
}
```

- `JpaTransactionManager` (JPA)
  - JPA 환경에서 EntityManagerFactory를 사용하여 트랜잭션을 관리하는 매니저이다.
  - JPA 엔티티의 상태를 관리하고 영속성 컨텍스트 내에서 트랜잭션을 통제한다.
  - `DataSourceTransactionManager` 보다 무거움, JPA 환경에서 필수적으로 필요한존재

### 정리

두 매니저는 Spring의 `PlatformTransactionManager`를 구현하며, 각 환경에 최적화된 트랜잭션 처리를 제공합니다. Spring Batch에서는 배치 메타데이터 관리는 보통 `DataSourceTransactionManager`를, 업무 도메인 JPA 트랜잭션은 `JpaTransactionManager`를 함께 사용하기도 합니다
