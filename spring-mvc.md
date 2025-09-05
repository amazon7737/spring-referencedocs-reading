

<img width="1144" height="542" alt="image" src="https://github.com/user-attachments/assets/ba6359e0-20de-46fe-86a0-872d57019859" />

- DispatcherServlet
  - Spring MVC의 앞단 컨트롤러 역할을 하는 클래스입니다.
  - 요청들을 받아, 어떤 Handler(Controller)로 넘길지 결정합니다.

- Handler(Controller)
  - 요청 URL과 메서드(GET/POST 등) 를 기반으로 요청을 핸들러 메서드에 매핑해줍니다.
  - 요청 데이터 검증, 서비스 계층 호출을 담당합니다.

#### View 계층

- View Resolver
  - Controller에서 반환한 결과를 어떤 View로 렌더링할지 결정합니다.
  - JSP, Thymeleaf, JSON 등.. 다양한 뷰타입을 지원
 
- View
  - 사용자에게 응답할 HTML, JSON, XML 등의 데이터입니다.


### 정리

<img width="1352" height="492" alt="image" src="https://github.com/user-attachments/assets/a3f3e6eb-cfa3-43ad-95cf-f5b4bf6aeaea" />

Spring MVC 는 L4(전송계층), Web Server, WAS(Web Application Server), Servlet, Spring MVC(Filter, DispatcherServlet, Controller, Service, Repository, DB, View Resolver, View) 와 같은 모듈별의 구조를 이루는 아키텍처를 정리해보았습니다. 이후에는 각 모듈별에 대한 자세한 동작과 역할들에 대해서 알아보면 좋을 것 같습니다.
