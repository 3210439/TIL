# 🦜 Mockk(Mockito-Kotlin)

이번에 코프링으로 개발하면서 unit test를 mockito를 활용하여 진행하게 되었다. unit test에 관해 찾아보는 중 kotlin에서 사용하는 mockk 라는 라이브러리를 발견하였다. 일반적으로 많이 사용되는 mockito-core 라이브러리도 고민 해보았지만 kotlin스럽게 개발할 수 있는 mockk에 더 끌리게 되었다. 그렇게 mockk 라이브러리를 이용하여 테스트를 진행했고 Service Layer 계층에 관해 unit test를 하였고 이를 정리해보았다.

### 의존성 설정

`testImplementation("io.mockk:mockk:1.12.0")`

**신 버전(1.13.3)이 아닌 구 버전 사용 이유**

신 버전 사용 후 현재 사용중인 코틀린 버전과 충돌하여 구 버전 이용

## Setting

mockk 을 이용한 객체 생성은 여러가지 방식이 존재한다. 그중에서도 Junit5 를 사용할 시 편리하게 사용할 수 있는 기능이 존재해서 해당 기능을 사용하기로 결정했다.

```
@ExtendWith(MockKExtension::class)
internal class PromotionServiceTest {

    @MockK
    private lateinit var promotionRepoMock: PromotionRepo

    @InjectMockKs
    private lateinit var promotionServiceMock: PromotionService
```

위와 같이 사용할 서비스와 레포지토리에 대해서 의존성을 부여하고 있다. 사용한 어노테이션에 대하여 정리하여 보았다.

`@ExtendWith(MockKExtendsion::class)`

- 해당 어노테이션은 Junit 5에서 유효하다.
- 파라미터에 (MockK, RelaxedMockK)를 사용할 수 있게 한다.
- 클래스 속성들에는 (MockK, RelaxedMockK, SpyK) 어노테이션을 사용할 수 있게 한다.

`@Mockk`

- 해당 어노테이션을 사용하기 위해서는 MockKAnotations.init()이 호출 되어야된다. 하지만 Junit5를 사용하고 있다면 MockKExtension 어노테이션을 사용해서 이를 대신할 수 있다.
- 현재 테스트에 필요없지만 의존성이 부여가 필요할 경우 해당 Mockk 어노테이션을 사용해서 이를 해결할 수 있다. 해당 어노테이션을 부여한 객체에서 특정 기능에 특정 리턴 값이 필요하다면 every를 사용해서 설정할 수 있다. 하지만 필요 없을 경우라면 그냥 relaxed 값에 true를 넣어주면 된다.

`@InjectMockKs`

- 자동으로 @Mock 어노테이션이 붙은 객체를 주입 받는다.

## 저장 기능 test

저장 기능을 테스트하기 위해서 아래와 같이 로직을 작성하였다. 크게 given, when, then으로 나누어 보았다.

- given
    - 데이터 저장 요청에 필요한 DTO인 PromotionSaveRequest와 저장 후 필요한 return 값을 생성한다.
    - every { 함수 } returns 리턴 값 형식으로 mocking한 객체의 함수에 대해 리턴 값을 설정한다.
- when
    - Serivce의 save 기능을 실행한다.
- then
    - verify 메소드를 통해서 save 메소드가 실행되었는지 체크한다. 이 때 인수값을 넣는 부분에 any()를 사용하여서 어떤 인수가 들어오든 체크하도록 한다. exactly = 1 설정을 통해서 한번 실행 되었는지 확인한다.
    - assertEquals를 통해서 리턴 값이 재대로 나타났는지 확인한다.
    - id 값은 0인데 이는 초기 객체 직접 생성시 아이디 값은 설정할 수 없어서 기본으로 0으로 초기화 되도록 설정 되었는데 이때문에 0으로 설정 되고 있다.

```
@Test
fun save() {
    // given
    val request = PromotionSaveRequest(
        name = "test01",
        endTime = LocalDate.parse("2022-10-10")
    )
    val promotion = Promotion(
        name = "test01",
        endTime = LocalDate.parse("2022-10-10")
    )
every{promotionRepoMock.save(any())}returns promotion

    // when
    val save = promotionServiceMock.save(request)

    // then
verify(exactly = 1){promotionRepoMock.save(any())}
// id 값은 객체를 생성할 때 지정할 수 없음으로 0이될 수 밖에 없다.
    assertEquals("id: 0가 저장되었습니다.", save)
}
```

## 삭제 기능 test

삭제 기능을 mockk을 사용하면서 많은 생각을 하게되었다. 실제로 DB에 접속해서 데이터를 조회 후 제거하는 것이아니라 mocking 하여 데이터를 지운 것 처럼 하는 느낌이라 테스트 후에 실제 동작은 또 다를 수 있다고 느껴졌다. 

삭제 기능을 구현하면서 이번에는 capture 라는 기능을 사용하였다. capture를 사용한 이유는 삭제 메소드가 실행 될 때 삭제할 객체가 인수로 제대로 들어갔는지 확인하기 위함이다.

`capture`

- capture를 사용해서 함수의 인자 값이 재대로 넘어 갔는지 확인할 수 있다.
- capture를 사용하기 위해서 capture 함수의 인자 값으로 slot 타입의 객체를 넘겨준다. slot에 인수로 넘어간 객체가 저장되게 되고 slot.captured 안에 인수가 들어가 있다.
- 형식은 다음과 같다. slot = slot<Promotion>() 제네릭에 파라미터의 타입을 입력하면 된다.

```
@Test
fun delete() {
    // given
    val promotion = Promotion(
        name = "test01",
        endTime = LocalDate.parse("2022-10-10")
    )
    val slot = slot<Promotion>()

    // when
every{promotionRepoMock.findById(1L).get()}returns promotion
every{promotionRepoMock.delete(capture(slot))}justruns

    promotionServiceMock.delete(1L)

verify{promotionRepoMock.findById(1L).get()}
verify{promotionRepoMock.delete(any())}

assertEquals(promotion.name, slot.captured.name)
    assertEquals(promotion.endTime, slot.captured.endTime)

}
```

### 마주한 에러

`io.mockk.MockKException: no answer found for`

위 에러는 호출 대상에 대한 스텁 정의를 하지 않아서 발생한 에러이다.

위 에러를 해결 하기 위해서 relaxed mock을 사용할 수 있다. 

```bash
// 예시
val mock = mockk<Example>(relaxed = true)
mock.someMethod(1) // --> 0 리턴
```

에러 해결책은 아래 블로그 참조

[https://javacan.tistory.com/entry/kotlin-mock-framework-mockk-intro](https://javacan.tistory.com/entry/kotlin-mock-framework-mockk-intro)

`Only one matching call to PromotionRepo(#1)/save(Any) happened, but arguments are not matching`

```bash
every { promotionRepoMock.save(any()) } returns promotion
```

every를 사용하여 리턴 값을 지정할 때 인수 값으로 any()를 사용하였더니 위와같은 에러를 마주하였다. any()의 경우 추측으로 사용 되는 함수이고 eq()가 자동으로 사용된 것으로 보인다.

[https://mockk.io/#matchers](https://mockk.io/#matchers)

위 사이트를 이용해서 인자 값의 타입만 확인하도록 변경하였다.

### 참조

[https://github.com/mockk/mockk](https://github.com/mockk/mockk)
