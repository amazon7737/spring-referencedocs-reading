## Spring Application 을 실행하였을때

- Spring Application을 실행하면, 아래와 같은 로그가 생성된다.

<img width="1537" height="551" alt="image" src="https://github.com/user-attachments/assets/96d167fd-f92c-40fc-85e7-f842a66612b1" />

- 'org.springframework.boot:spring-boot-starter-web' 패키지가 포함된 Spring Application 이 실행된 로그이다.

<img width="1044" height="255" alt="image" src="https://github.com/user-attachments/assets/d840c132-2435-4a0e-bb2d-30bbc06fcc64" />

1. 내장톰캣(embedded tomcat)이 8080포트로 초기화를 이룬다.
2. Servlet engine이 실행된다.
3. Tomcat에 내장된 `ServletWebServerApplicationContext` 가 초기화를 이루며 빈에서 활성화가 된다.
4. `TomcatWebServer`가 실행이 완료된다.

### TomcatWebServer

- HTTP 요청이 들어올때, 먼저 OS레벨에서 TCP 연결을 받아들인다.
- Apache 문서에 확인해보면, 

<img width="636" height="244" alt="image" src="https://github.com/user-attachments/assets/a38d1cb7-c63c-45b3-b88e-9a09439062d1" />

- SYN 패킷은 연결 시작 요청을 알린다. 초기 시퀀스 넘버(ISN, Initial Sequence Number)를 포함한다

<img width="640" height="304" alt="image" src="https://github.com/user-attachments/assets/f1805801-1897-4263-8380-eaa01193d061" />

- ACK 패킷은 요청이 제대로 수신되었음을 알린다. 서버는 SYN + ACK 패킷으로 응답한다.

<img width="609" height="252" alt="image" src="https://github.com/user-attachments/assets/4b24d5da-9c2a-49f4-badf-00d3712cca9b" />

- 클라이언트는 ACK 패킷을 보내서 연결을 완료한다.

<img width="776" height="462" alt="image" src="https://github.com/user-attachments/assets/ab362fdf-d5e9-4ed6-a27c-659488f5838b" />

- OS 레벨에서 관리되는 TCP 미연결 큐는 ACK 패킷이 돌아오기를 대기하는 요청들이 모인다.

<img width="1322" height="559" alt="image" src="https://github.com/user-attachments/assets/aa8303c1-68c2-4d61-adb9-cb9f1dc6d83e" />

- TCP 연결이 완료된 요청은 애플리케이션이 사용할 수 있도록 완료된 연결 큐로 이동한다.

<img width="1456" height="852" alt="image" src="https://github.com/user-attachments/assets/c9c93b71-2e3d-489c-8a2d-c072a8d66f89" />

- Tomcat의 수락자 스레드(Accept Thread)는 완료된 연결 대기열에서 연결을 수락한다.

<img width="1453" height="820" alt="image" src="https://github.com/user-attachments/assets/bfc0773f-083c-44a5-9477-3f6601a84faa" />

- Tomcat에는 워커스레드를 모아둔 스레드 풀이 존재한다.
- 사용 가능한 스레드풀에 워커스레드가 있는지 확인한다.
- 사용가능하지 않은 경우, 활성 스레드 수가 maxThreads보다 작으면 워커 스레드를 생성한다.
- 그렇지 않으면, 워커 스레드가 사용 가능해질때까지 기다린다.

<img width="1205" height="682" alt="image" src="https://github.com/user-attachments/assets/3592b847-88bc-4cc6-a8f8-a4f955b1f9c5" />

- AcceptThread는 새롭게 들어온 연결을 `ServerSocket`을 통해 받는다.
- accept된 연결을 내부적으로 해당 소켓을 WorkerThread에게 넘긴다.

<img width="1450" height="836" alt="image" src="https://github.com/user-attachments/assets/1ef87288-f4d4-4439-9d92-167e7acb156b" />

- WorkerThread는 소켓의 HTTP 요청 데이터를 읽고, 비즈니스 로직 실행을 담당한다.
  
- 동작:
  - 워커 스레드는 연결에서 입력을 읽고, 요청을 처리하고, 클라이언트에 응답을 보내는 실제 작업을 수행한다.
  - 연결이 유지되지 않으면 연결을 종료하고 빈 스레드 풀에 저장된다.
  - 연결 유지 상태인 경우, 연결에 더 많은 데이터가 있을때까지 기다린다.
  - keepAliveTimeout까지 데이터를 사용할 수 없으면 연결을 종료하고 빈 스레드 풀에 저장된다.

### 정리

<img width="1635" height="635" alt="image" src="https://github.com/user-attachments/assets/5bdf7eeb-9464-4270-b29a-aa4610873e7f" />

- 클라이언트에게 요청을 받아, TCP 연결을 완료하고 Tomcat이 요청을 처리하는 과정을 살펴보았다. 다음은 워커 쓰레드가 요청을 받아 실제로 Spring 에서 어떤 흐름으로 작동하는지 알아보겠다.
-> Spring MVC
