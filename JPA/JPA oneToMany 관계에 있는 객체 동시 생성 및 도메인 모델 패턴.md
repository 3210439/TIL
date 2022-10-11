# JPA oneToMany 관계에 있는 객체 동시 생성 및 도메인 모델 패턴

오늘 김영한 님의 강의를 보면서 정리를 해두면 좋을 것 같은 부분을 발견했다. 주문이 발생하게 되면 주문된 아이템도 같이 생성해야 된다. 그리고 주문과 주문 아이템은 oneToMany 관계를 이루고 있다. 주문 아이템도 저장하기 위해서 주문 아이템 레포지토리를 사용해서 save 할 수도 있지만 oneToMany 관계에서 casecade 타입을 All로 하였을 경우 부모 쪽 테이블 repository에서 같이 생성할 수 도 있다. 해당 말만 들어선 이해하기 어려울 수 있다. 그래서 예제를 준비했다.

```
@OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
private List<OrderItem> orderItems = new ArrayList<>();
```

주문과 주문 아이템은 원 투 매니 관계이다.

주문을 하였다는 것은 여러 아이템을 선택했다는 뜻이기도 하기에 OrderItem… orderItems로 값을 입력 받는다. (…) 가변인자는 객체를 하나만 입력받아도 되고 여러개 입력받아도 되고 인자를 입력받지 않아도 된다. 하지만 주의할 점은 가변 인자의 경우 인자의 개수가 정해저 있지 않기 때문에 항상 마지막 파라미터로 사용해야 컴파일 에러가 발생하지 않는다.

```
public static Order createOrder(Member member, Delivery delivery, OrderItem... orderItems) {
    Order order = new Order();
    order.setMember(member);
    order.setDelivery(delivery);
    for (OrderItem orderItem : orderItems) {
        order.addOrderItem(orderItem);
    }
    order.setStatus(OrderStatus.ORDER);
    order.setOrderDate(LocalDateTime.now());
    return order;
}
```

## 주의할점

위와같이 동시에 생성할 때는 주의해야한다. orderItem의 경우 명확히 order에서만 참조하며 order에서만 관리하기 때문에 위와같이 사용해도 문제가 없는 것이다. 만약에 다른 곳에서 orderItem을 참조하고 있다고 가정하였을 경우 큰 문제가 발생하게 된다. orderItem은 order와 cascade.all 관계이기 때문에 order가 지워질 때 orderItem 또한 지워진다. 이렇게 될 경우 orderItem을 참조하는 곳에서 문제가 발생하게 된다. 다른 곳에서 참조하고 있지 않고 한곳에서만 사용하며 생명주기를 같이할 때 cascade.all 을 사용하는 것이 좋다.

## 생성 방식은 한가지 방식으로

위와같이 createOrder 메소드를 생성하였다. 하지만 다른 개발자는 해당 메소드를 사용하지 않고 개발 할 수 있다. 시간이 지나고 유지보수를 하게 되었을 때 생성 방식이 여러가지일 경우 유지보수하기 좋지 않을 수 있다. 그렇기에 생성자에 protected 접근 제어자를 붙여서 강제로 생성 방식을 한가지 방식으로 제한 하는 방법이 있다.

```

protected Order() {

}
```

더 간단한 방식으로 아래와 같은 방식이 있다.

`@NoArgsConstructor(access = AccessLevel.*PROTECTED*)`

해당 어노테이션을 엔티티에 붙이면 된다.

### 도메인 모델 패턴

마지막으로 김영한님 강의를 보면서 개발하는데 중요한 도메인 모델 패턴을 보았고 실제로 도메인 모델 패턴으로 개발하면 코드가 깔끔하게 될 것이라는 것을 느꼈다.

주문 서비스의 주문과 주문 취소 메서드를 보면 비즈니스 로직 대부분이 엔티티에 있다. 서비스 계층은 단순히 엔티티에 필요한 요처을 위임하는 역할을 한다. 이처럼 엔티티가 비즈니스 로직을 가지고 객체 지향적인 특성을 적극 활용하는 것을 도메인 모델 패턴이라고 한다.

이와 반대되는 것으로 트랜잭션 스크립트 패턴이라는 것이 존재한다.