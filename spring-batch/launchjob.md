## launchJob() 호출 시 Spring Batch의 내부동작

1. JobRepository 이전 실행 이력 조회
- JobLauncher가 실행 요청을 받으면 JobRepository에서 해당 Job과 JobParameter로 마지막 실행이 있었는지 확인 (JOB_INSTANCE / EXECUTION)
- 이미 실행 중이거나 정상 종료된 Job은 중복 실행 방지 (상태 체크 후 예외 발생)

2. JobExecution 생성
- JobParameters 의 유효성 검증 후, JobRepository에 새로운 JobExecution을 생성하여 실행 엔티티로 등록
- JobExecutionListener의 beforeJob()이 먼저 호출

3. Job , Step 실행
- Job의 execute() 호출 : Job에 등록된 여러 Step을 순차적으로 실행 (StepHandler 통해 StepExecution 생성 및 관리)
- 각 Step 실행 전에 StepExecutionListener의 beforeStep(), 실행 후 afterStep()이 동작
- Step이 실패하거나 중단되면 이후 Step은 실행되지 않고, JobExeuction의 상태와 ExitStatus도 이에 맞게 업데이트

4. Job 종료 및 결과 처리
- 모든 Step이 정상 완료되면, JobExecution의 BatchStatus를 COMPLETED로, Listener의 afterJob()이 호출되어 종료 상태 업데이트.
- 최종적으로 JobExecution의 결과는 JobRepository에 영속화

<img width="417" height="303" alt="image" src="https://github.com/user-attachments/assets/15727c65-c0b1-45e6-ac36-36a841bdcfe3" />

### 정리

<img width="808" height="156" alt="image" src="https://github.com/user-attachments/assets/571dd703-d5c9-41a7-b88a-067d831ae533" />

위는 배치의 내부 실행 흐름이다. 해당 흐름을 통해서 스프링 배치의 Job를 실행 호출할때 순서대로 동작하게 된다.
