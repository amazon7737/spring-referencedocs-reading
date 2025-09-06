## launchJob() 호출 시 Spring Batch의 내부동작

1. JobRepository 에서 이전 실행 이력 조회

`SimpleJobRepository.getLastJobExecution()` 같은 메서드가 호출됨

이때 실행되는 쿼리:

```sql
SELECT JOB_INSTANCE_ID, JOB_NAME
FROM BATCH_JOB_INSTANCE
WHERE JOB_NAME = ? AND JOB_KEY = ?
```

2. 없으면 새로운 JobInstance 생성
필요하다면 `INSERT INTO BATCH_JOB_INSTANCE ...` 실행.

3. JobExecution, StepExecution 을 테이블에 기록하면서 실제 Job 실행.

## 실제 그렇게 동작하는지 확인하기 위한 테스트를 구성

[jojoldu님의 spring-batch-in-action](https://github.com/jojoldu/spring-batch-in-action)을 참고하여 테스트를 구성하였다.

