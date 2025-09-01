# @Mock은 언제쓸까?

@Mock은 행위나 반환값을 정의해서 테스트 대상 코드와의 상호작용을 가짜로 생성합니다. 실제 구현이 필요 없고, 동작만 시뮬레이션 할때 사용합니다.

```java
@Mock
UserRepository userRepository;

@Test
void mock_예제() {
  when(userRepository.findById(1L)).thenReturn(Optional.of(new User("kang")));
    
  Optional<User> result = userRepository.findById(1L);
  
  assertEquals("kang", result.get().getName());
  verify(userRepository).findById(1L);
}
```

Stub 은 고정된 동작을 정의할때 사용합니다. `when().thenReturn(...)` 와 같은 구문이 stub 동작을 정의하는 방식입니다.

테스트 대상에게 정해진 입력을 주고, 그에 따라 정해진 출력을 받아야 할때 사용합니다. '이 상황에서는 무조건 이 값을 반환하라' 는 기준값을 고정할 수 있습니다.

```java
when(paymentGateway.charge(anyInt()))
  .thenReturn("success");
```

행위 검증보다는 상태 검증에 가깝습니다.

@Spy 는 실제 객체를 감시합니다. 실제 객체를 쓰되, 특정 메서드만 mocking하고 싶을때 예를 들어서 일부만 override하거나, 내부 호출 확인이 필요한 경우에 사용합니다.

```java
@Spy
List<String> spyList = new ArrayList<>();

@Test
void spy_예제() {
  spyList.add("A");
  spyList.add("B");

  verify(spyList).add("A");
  assertEquals(2, spyList.size());
}
```

```java
@Spy
MyService realService = new MyService();

@Test
void spy_특정메서드만_모킹() {
  doReturn("mocked result")
    .whtn(realService).getExternalData();
    
  assertEquals("mocked result", realService.getExternalData());
  // 나머지 메서드는 진짜 로직 실행
}
```


Spy 의 실제 실습 예제를 보지 않아서 실제로 감이 오지 않는데요.
*추후에 직접 실습해보기*

