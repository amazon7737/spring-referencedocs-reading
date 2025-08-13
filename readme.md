# 목차
---

0. [Spring Bean]()
1. [IoC, DI]()
2. [AOP]()
3. [Servlet]()
4. [Tomcat의 동작 순서](#Tomcat의-동작-순서)
5. [MVC]()
6. [빌드 도구](#빌드-도구)
7. [네트워크](#네트워크)
   - [TCP](#TCP/IP-송수신-동작-순서)
   - [HTTP](#HTTP)
9. [그리며 연습했던 draw io 파일들](#Draw)

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

#### 네트워크

##### TCP/IP 송수신 동작 순서

![image](https://raw.githubusercontent.com/amazon7737/spring-framework-read-docs/refs/heads/main/images/tcp-connection-seq.png)


서버는 기본적으로 Process라고 할 수 있고, Socket이 열려있을 것이다. Process는 Socket에게 할수있는 권한은 RWX 가 있다. Read, Send, Launch 여기서 Send를 한다고 가정하겠다.

전송하고자 하는 파일이 1.4MB라고할때, 먼저 HDD에서 파일을 읽어들인다. File System이 있을것이고 Server에 할당된 Memory가 64KB라고할때 파일을 쪼개어 64KB조각을 만든다. 그리고 Socket이 TCP 버퍼쪽으로 COPY 시킨다. 이것을 읽어들여서 가져다주는 것이 Buffered I/O이다. 적재되어있는 조각을 IP로 넘기는데 이때 Segment 라는 단위로 쪼개어 보낸다. 64KB 조각에서 더 쪼개는것임.

IP에 들어온 Segment는 Driver를 통해서 나가 Packet에 담긴다. 그리고 L2영역인 NIC로 나갈때는 Frame으로 들어간다. 하나의 Packet이 Frame으로 또 적재되는 것이다.

그리고 Frame을 목적지를 향해 전송시키는데, 이때 여러번 Frame을 갈아타면서 목적지에 도달할 수 있다.

수신자에게 전달되었다. 수신자에 도달하자마자 Frame에서 Packet을 꺼내어 NIC를 넘어간다. (DeCapsulation)

그리고 IP를 거치며, Packet안에 Segment를 꺼내고 그걸 TCP 버퍼에 저장시킨다. 이걸 성공적으로 적재하게되면 서버에게 ACK 패킷을 전송한다.

서버는 ACK 패킷을 받으면, 전송했던 데이터가 잘 전달되었다고 판단하고, 다음 데이터를 전달한다.

이때, TCP 버퍼는 Window Size 라는 여유공간이 있다. ACK 패킷에는 여유공간인 Window Size를 명시하여 보내는데 만약 여유공간이 부족하다고 서버가 판단하면 대기(Wait)가 걸린다. 여유공간이 있을때까지 판단한다.

수신자 측에서는 여유공간이 만들어질려면 적재된 Segment를 Socket 쪽으로 올려줘야한다 이때 일어나는것이 File I/O 이다. 데이터를 퍼올리는 속도가 빠를수록 TCP 속도에 판결이 나는 것이다.

** Window Size가 만약 부족하면 서버는 대기가 걸리는데, 여유공간이 있을때까지 어떻게 판단하지?
서버는 더이상 데이터를 보내지 않는다. 서버에서는 Persist Timer를 시작한다. 일정 시간 후 Window Probe 패킷을 전송하며 수신자는 Window Probe에 대해 반드시 ACK 응답을 주어야 한다.
Window Size가 여전히 0이면 다시 타이머 재시작, 0이 아니면 전송을 재개한다.
(Window Probe는 1바이트 크기의 특수 세그먼트)

#### HTTP

##### HTTP Method

![image](https://raw.githubusercontent.com/amazon7737/spring-framework-read-docs/refs/heads/main/images/HTTP-Method.png)



# draw
---
1. [IoC, DI, AOP](https://drive.google.com/file/d/1_gsWZYOrFJUKD-br1loYv8xpsyYrHisF/view?usp=sharing)
2. [Servlet, Architecture, Pattern](https://github.com/amazon7737/Spring-comes-who/blob/main/1%EC%A3%BC%EC%B0%A8/servlet%2Carchitecture%2Cpattern.png)

