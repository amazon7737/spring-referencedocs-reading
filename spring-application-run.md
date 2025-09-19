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

