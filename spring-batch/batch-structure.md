## 스프링배치 구조도

<img width="873" height="645" alt="image" src="https://github.com/user-attachments/assets/0d378df6-2e82-4a52-9da0-379ea8381ecd" />

- Job : 전체 배치 실행 단위. 하나 이상의 Step을 포함하며, 각 Step은 순차적.병렬적으로 실행 가능
- Step : Job 내부에서 개별 작업을 수행하는 단위. Step은 데이터를 Chunk(여러 건씩) 혹은 Tasklet(단건) 방식을 처리할 수 있음.
- ItemReader : 데이터소스를 디비나 파일, API등으로부터 한 건씩 읽어오는 역할
  - 다양한 구현체 (JdbcBatchItemReader, JpaPagingItemReader), 개발자가 직접 커스텀 구현도 가능
  - read()를 통해 읽어옴 한 번 호출될 때마다 하나의 아이템을 반환하며, 더 이상 읽을 데이터가 없으면 `null`을 반환

- ItemProcessor : 읽어온 아이템을 어떤 로직을 실행해서 변환/가공할 수 있음
- ItemWriter : 처리된 아이템들을 하나의 Chunk 배치 단위로 받아서 DB에 저장하거나 파일에 기록 하는 등의 출력 작업을 수행

#### Chunk 기반 실행
만약 Chunk size가 10이라고 했을때, read() 가 10번 호출되어서 각각 1개씩 아이템을 읽어, 총 10개의 아이템을 한 번에 batch로 처리하는 구조가 된다.

#### 예시 동작

1. Step 이 시작하고, 반복적으로 read() 호출하여 아이템을 모으기
2. 각 아이템별로 process() 로 변환/가공
3. Chunk 단위로 write() 일괄 저장/처리
4. Chunk 처리 후 트랜잭션 커밋 또는 롤백
5. 더 읽을 데이터가 없으면 Step을 종료하고 다음 Step이 계속됨

```java
@Bean
public Step myStep(JobRepository repo, PlatformTransactionManager tm) {
  return new StepBuilder("myStep", repo)
    .<InputType, OutputType>chunk(10, tm)
    .reader(itemReader())
    .processor(itemProcessor())
    .writer(itemWriter())
    .build());
}
```
- `itemReader.read()`가 반복 호출되어서 Chunk 크기 만큼 데이터를 모은다.
- `itemWriter.write()`는 반복 호출되지 않고 chunk size가 10이고 reader가 모아둔 아이템이 10개라고 하면 한번에 리스트를 받아서 처리한다.

##### chunk 단위로 트랜잭션이 동작하네?
그렇다면 100개 아이템을 배치처리를 한다고 하고 chunk 단위가 10이라고하면 트랜잭션을 10개로 쪼개서 동작을 한다.
만약 40-50개 처리중 중간에 에러가 발생하면 앞에 배치처리해둔 작업 아이템들은 롤백처리가 된다.
작업 상황에 맞게 chunk size 설정이 중요하다는 것을 알 수 있다.

### 정리
스프링 배치 아키텍처 구조를 보면, Step 단위에서 반복적으로 `read()` 메서드로 데이터를 읽고 필요 시 가공하여 batch로 처리하는 구조이다.
이후 Chunk 처리가 끝나면 트랜잭션을 커밋할지 롤백할지 결정한다.
다음은 내부적으로 스프링 배치가 사용하던 구조가 변경되어, PlatformTransactionManager를 기준으로한 트랜잭션 제어 동작에 대해서 알아보도록 하겠다.
-> 
