## Spring Application 을 실행하였을때

- Spring Application을 실행하면, 아래와 같은 로그가 생성된다.

<img width="1537" height="551" alt="image" src="https://github.com/user-attachments/assets/96d167fd-f92c-40fc-85e7-f842a66612b1" />

- 'org.springframework.boot:spring-boot-starter-web' 패키지가 포함된 Spring Application 이 실행된 로그이다.

<img width="1044" height="255" alt="image" src="https://github.com/user-attachments/assets/d840c132-2435-4a0e-bb2d-30bbc06fcc64" />

1. 내장톰캣(embedded tomcat)이 8080포트로 초기화를 이룬다.
2. Servlet engine이 실행된다.
3. Tomcat에 내장된 `ServletWebServerApplicationContext` 가 초기화를 이루며 빈에서 활성화가 된다.
4. `TomcatWebServer`가 실행이 완료된다.

