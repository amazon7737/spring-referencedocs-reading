# 목차
---

1. [IoC, DI]()
2. [AOP]()
3. [Servlet]()
4. [Tomcat의 동작 순서](#Tomcat의-동작-순서)
5. [MVC]()
6. [빌드 도구](#빌드-도구)
7. [그리며 연습했던 draw io 파일들](#Draw)

#### Tomcat의 동작 순서
---

Tomcat에는 connection을 수락하는 accept thread가 있습니다. OS와 클라이언트간에 TCP 핸드셰이크를 이룰때, 연결을 보관하는 큐가 하나이거나 여러개일 수 있다.

![image](https://raw.githubusercontent.com/amazon7737/spring-framework-read-docs/refs/heads/main/images/tomcat-draw.png)

1. TCP connection 으로 클라이언트가 요청
2. TCP 핸드셰이크가 완료되지 않은 미완료 연결을 보관
3. 완료되면 연결은 애플리케이션에서 사용할 수 있는 완료된 연결 큐로 이동 (acceptCount 매개변수가 이 큐의 크기를 제어하는데 사용)
4. Tomcat 의 accept thread는 처리 가능한 워커쓰레드가 스레드풀 유후 상태인지 확인
5. 워커 스레드가 하나라도 비면, Tomcat Connector가 완료 큐에서 맨앞 요청을 수락(accept)하여 할당되어 처리
6. 큐 역시 다찬다면, 추가 요청은 타임아웃/거절 처리

정리: 내부 대기 큐가 웹서버내에서 이루어지는게 아니라, 요청을 받는 OS레벨에서의 요청 대기 큐가 있다. 여기에는 TCP 미연결을 보관하는 큐와 TCP 연결 성공으로 인해서 애플리케이션이 연결된 세션을 가져갈 수 있도록 하는 큐 이렇게 2개가 보관되어있고 이것은 소켓자체에서 이루는 것이다.

Reference : https://netflixtechblog.com/tuning-tomcat-for-a-high-throughput-fail-fast-system-e4d7b2fc163f


#### 빌드 도구


##### 메이븐(Maven)이 빌드할때 동작하는 순서

![image](https://raw.githubusercontent.com/amazon7737/spring-framework-read-docs/refs/heads/main/images/maven-default-cycle.png)


Default 라이프 사이클 절차

`validate` : 프로젝트가 정확하고 필요한 정보가 모두 있는지 확인 <br/>
`compile` : 프로젝트의 소스코드를 컴파일 <br/>
`test` : 컴파일된 소스코드를 테스트 <br/>
`package` : 컴파일된 코드를 가져와서 JAR 같은 배포 가능한 형식으로 패키징 <br/>
`verify` : 품질 기준이 충족되는지 확인하기 위해 검사 시행 <br/>
`install` : 로컬 저장소에 패키지 설치해서 로컬의 다른 프로젝트에서 종속성으로 사용 <br/>
`deploy` : 원격저장소로 복사 <br/>


Reference
- https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html
- https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Built-in_Lifecycle_Bindings
- https://dev.to/mohamed_el_laithy/maven-lifecycle-simplified-animated-visual-guide-575




# draw
---
1. [IoC, DI, AOP](https://drive.google.com/file/d/1_gsWZYOrFJUKD-br1loYv8xpsyYrHisF/view?usp=sharing)
2. [Servlet, Architecture, Pattern](https://github.com/amazon7737/Spring-comes-who/blob/main/1%EC%A3%BC%EC%B0%A8/servlet%2Carchitecture%2Cpattern.png)

