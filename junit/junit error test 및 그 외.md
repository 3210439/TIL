# junit error test 및 그 외

예외 상황이 제대로 발생하는지 확인해야 되는 순간이 있다. 그럴 경우 아래와 같은 방식으로 코드를 작성할 수 있다. try/catch문으로 에러를 잡게되면 코드 가독성이 떨어진다. 더 나은 방법으로 @Test어노테이션에 인수로 expected 값에 발생하는 error를 넣어주면 된다. 그리고 예외가 발생되지 않았을 경우 Assert.fail를 사용하여 강제로 테스트 실패를 발생시키도록 한다.

```
@Test(expected = IllegalStateException.class)
public void 중복_회원_예외() throws Exception {
    // given
    Member member1 = new Member();
    member1.setName("kim");

    Member member2 = new Member();
    member2.setName("kim");

    // when
    memberService.join(member1);
    memberService.join(member2);

    //then
    Assert.fail("에외가 발생해야 한다.");

}
```

build.gradle 파일에 아래와 같이 선언하게 되면 in-memory 방식으로 h2 db를 사용할 수 있다.

`runtimeOnly 'com.h2database:h2'`

junit 테스트시 yml 파일이나 properties 파일에서 db 설정을 하지 않았을 경우 위와같이 h2 설정이 되어있다면 자동으로 인메모리 방식으로 h2 db가 실행된다.

## TIP)

엔티티에서 해결할 수 있는 비즈니스 로직은 엔티티 내부에 선언하는 것이 좋다.

### Exception 생성 방식

RuntimeException을 확장하면 된다.

```
public class NotEnoughStockException extends RuntimeException {

    public NotEnoughStockException() {
    }

    public NotEnoughStockException(String message) {
        super(message);
    }

    public NotEnoughStockException(String message, Throwable cause) {
        super(message, cause);
    }

    public NotEnoughStockException(Throwable cause) {
        super(cause);
    }

}
```