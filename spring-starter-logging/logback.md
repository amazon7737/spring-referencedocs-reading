
#### 로깅 라이브러리 - Logback

로깅(logging)이란 애플리케이션이 동작하는 동안 시스템의 상태나 동작 정보를 시간순으로 기록하는 것을 의미

Logback : [log4j](https://logging.apache.org/log4j/2.x/index.html#:~:text=Apache%20Log4j%20is%20a%20versatile,ed%20by%20a%20big%20community.) 이후에 출시된 로깅 프레임워크로서 [slf4j](https://www.slf4j.org/)를 기반으로 구현되었으며, 과거에 사용하던 log4j에 비해 월등한 성능을 자랑한다고 함.

https://logback.qos.ch/

- spring-boot-starter-web 라이브러리 내부에 내장되어 있어, 의존성을 따로 추가하지 않아도 사용가능
<img width="868" height="328" alt="image" src="https://github.com/user-attachments/assets/282610bc-4ce3-4c68-91e1-2742db87f682" />


Logback의 특징

- TRACE, DEBUG, INFO, WARN, ERROR 로 로그 레벨 설정 가능
	- ERROR : 심각한 문제 발생으로 애플리케이션 작동 불가능한 경우
	- WARN : 시스템 에러의 원인 경고 수준
	- INFO : 애플리케이션 상태 변경과 같은 정보 전달에 사용
	- DEBUG : 애플리케이션의 디버깅 메시지 표시
	- TRACE : DEBUG 레벨보다 더 상세한 메시지 표현

- 운영 환경, 개발 환경 각각 다르게 출력 레벨을 설정해서 로그를 확인할 수 있음
- Logback의 설정 파일을 일정 시간마다 스캔해서 애플리케이션을 재가동하지 않아도 설정 변경 가능
- 별도의 프로그램 지원 없이도 자체적으로 로그 파일 압축 가능
- 저장된 로그 파일에 대한 보관 기간 등을 설정해서 관리


- Logback vs application.yml 에서 로그레벨 비교
<img width="1266" height="744" alt="image" src="https://github.com/user-attachments/assets/68bcd97b-0fe3-41ef-a3b8-3db44c021307" />

##### Logback의 Appender

Appender는 로그의 형태를 설정하고 어떤 방법으로 출력할지를 설정, 하나의 인터페이스를 의미하며, 하위에 여러 구현체가 존재

구현체들
- ConsoleAppender: 콘솔에 로그를 출력
- FileAppender: 파일에 로그를 저장
- RollingFileAppender : 여러 개의 파일을 순회하면서 로그를 저장
- SMTPAppender: 메일로 로그를 전송
- DBAppender: 데이터베이스에 로그를 저장


```xml
	<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
		<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
			<level>INFO</level>
		</filter>
		<encoder>
			<pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] %logger %msg%n</pattern>
		</encoder>
		...
	<appender name="INFO_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
			<level>INFO</level>
		</filter>
		...
		<encoder>
			<pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] %logger %msg%n</pattern>
		</encoder>
```


- encoder 요소를 통해 로그의 표현 형식을 패턴(pattern) 으로 정의

%Logger{length} : 로거의 이름
%-5level : 로그 레벨, -5는 출력 고정폭의 값
%msg(%message) : 로그 메시지

---

%d : 로그 기록 시간
%p : 로깅 레벨
%F : 로깅이 발생한 애플리케이션 파일명
%M : 로깅이 발생한 메서드 이름
%I : 로깅이 발생한 호출지의 정보
%thread : 현재 스레드명
%t : 로깅이 발생한 스레드명
%c : 로깅이 발생한 카테고리
%C : 로깅이 발생한 클래스명
%m : 로그 메시지
%n : 줄바꿈
%r : 애플리케이션 실행 후 로깅이 발생한 시점까지의 시간
%L : 로깅이 발생한 호출 지점의 라인 수



- Logback의 Appender 상속구조
<img width="424" height="267" alt="image" src="https://github.com/user-attachments/assets/bd16f4d7-e730-456c-b619-6bbec8a3b463" />


#### logback이 실행되는 과정
![image](https://raw.githubusercontent.com/amazon7737/spring-docs-and-notes/refs/heads/main/images/logback.png)

draw.io : https://drive.google.com/drive/u/0/folders/1u43ig9C-r4RutZ-GVDw0_Tk96cJPKwzP
