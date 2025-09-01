>해당 디렉토리는 스프링 개발 및 운영을 위한 것들을 모두 모아두고 다시 볼 수 있도록 정리해두기 위한 모음집입니다.

스프링으로 개발을 진행하며 알아야 '스프링스럽게 개발할 수 있으려면 어떤 방법으로 해야할까?'를 고민하며 정리하였습니다.


스프링 개발을 진행할때, 필요한 것들은 아래와 같습니다.

- 개발 아키텍처
- 객체지향 원칙 및 설계
- 테스트
- 예외
- 빌드, 배포, 테스트(운영 전략)
- 안정적인 배포 및 시스템 설계는 어떻게 이루어지는가?


---


#### Spring으로 API 서버를 개발할때의 테스트 대상 및 방법

| 테스트 종류                   | 설명                     | 예시                        | 도구                                                 |
| ------------------------ | ---------------------- | ------------------------- | -------------------------------------------------- |
| 단위 테스트(Unit Test)        | 메서드/클래스 단위로 작은 범위를 테스트 | Service, Domain, Util     | JUnit + Mockito                                    |
| 슬라이스 테스트(Slice Test)     | 컨트롤러/레포지토리만 띄워서 부분 테스트 | @WebMvcTest, @DataJpaTest | Spring Test, MockMvc                               |
| 통합 테스트(Integration Test) | 스프링 컨텍스트 전체를 띄워서 통합검증  | REST API 요청-응답 시나리오       | @SpringBootTest, TestRestTemplate 또는 WebTestClient |
| 인수 테스트 / E2E             | 실제 사용자 시나리오 기반 테스트     | 회원가입 -> 로그인 -> 리소스 요청     | RestAssured, Postman Test 등                        |


##### 애플리케이션 아키텍처

- [엔터프라이즈 애플리케이션 아키텍처](https://gist.github.com/paulstatezny/9547489)
- [로버트C 마틴 - 클린 아키텍처](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)



#### Test-Driven-Development

##### Test Driven 한 테스트 작성 순서

[1] 실패하는 테스트 작성 <br/>
[2] 테스트 통과할 최소한의 기능 구현 <br/>
[3] 코드 리팩터링(중복을 제거) <br/>


- [[@Mock은 언제쓸까?]]
- 통합테스트를 구성할때 주의해야하는 부분
  - [Spring Guite - 테스트 전략 , Yun님](https://cheese10yun.github.io/spring-guide-test-1/)


##### 객체지향 원칙관련 가이드 문서

- [우아한 객체지향 조영호님](https://www.youtube.com/watch?v=dJ5C4qRqAgA&t=4781s)
- [블랙잭 MVC 패턴 관련 질문](https://github.com/next-step/kotlin-blackjack/pull/344)
- [블랙잭 미션 유지보수 관련 코멘트](https://github.com/next-step/kotlin-blackjack/pull/344)
- [두 개의 취향: 과학적 언어 표현과 청테이프라는 만능 도구](https://johngrib.github.io/wiki/article/science-and-delete/)







