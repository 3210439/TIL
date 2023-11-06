## 스프링 데이터 JPA를 이용한 조회 기능
### 스펙
* 정의
  * JPA에서 스펙은 리포지토리를 통해 데이터를 조회할 때 사용하는 조건을 표현하는 방법이다.
  * JPA는 다양한 검색 조건을 조합하기 위해 CriteriaBuilder와 Predicate를 사용하므로 JPA를 위한 스펙은 CriteriaBuilder와 Predicate를 이용해서 조건을 구현해야 한다.
* 특징
  * 동적 쿼리: 스펙을 사용하면 쿼리의 조건을 동적으로 변경할 수 있다.
  * 복잡한 조건 표현: 스펙을 사용하면 AND, OR, NOT과 같은 복잡한 조건을 표현할 수 있다.
  * 유지 보수: 스펙을 사용하면 쿼리의 조건을 별도의 클래스로 구현하여 유지보수를 쉽게 할 수 있다.
* 예시
```
public class OrdererSpec implements Specification<Order> {

    private String orderer;

    public OrdererSpec(String orderer) {
        this.orderer = orderer;
    }

    @Override
    public Predicate toPredicate(Root<Order> root, CriteriaBuilder cb) {
        return cb.equal(root.get("orderer").get("name"), orderer);
    }
}
```
조회 방법
```
List<Order> orders = orderRepository.findAll(new OrdererSpec("madvirus"));

```


### 정렬
* 정의
  * 정렬은 데이터를 특정 기준으로 순서대로 나열하는 기능이다. 
  * JPA에서는 Sort 인터페이스를 사용해서 정렬 조건을 지정한다.
* 예시
```
Sort sort = Sort.by(Order_.orderDate).descending();

List<Order> orders = orderRepository.findAll(sort);

```

### 페이징
* 정의
  * 데이터를 페이지 단위로 조회하는 기능이다.
  * JPA에서는 Pageable 인터페이스를 사용하여 페이징 조건을 지정할 수 있다.
* 예시
```
Pageable pageable = Pageable.of(0, 3);
Page<Order> orders = orderRepository.findAll(pageable)
```
* Page 객체 구성요소
  * content: 조회된 데이터의 리스트
  * totalElements: 전체 데이터의 개수
  * totalPages: 전체 페이지 수
  * number: 현재 페이지 번호

### @Subselect
* 정의
  * JPQL에서 Subselect를 사용하는 대신 Java 코드에서 Subselect를 작성할 수 있도록 지원하는 어노테이션이다.
* 장점
  * 코드의 가독성 향상
  * 유지 보수의 용이성
* Subselect 어노테이션의 속성
  * query: Subselect로 사용할 JPQL 쿼리를 지정한다.
  * resultClass: Subselect의 결과 클래스를 지정한다.
  * resultSetMapping: Subselect의 결과를 매핑할 매핑을 지정한다.
  * fetchMode: Subselect의 결과를 가져올 모드를 지정한다.
  * cacheable: Subselect의 결과를 캐시할지 여부를 지정한다.
* 예시
```
@Entity
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String customer;

    @Subselect(query = "SELECT c FROM Customer c WHERE c.name = :customer", resultClass = Customer.class)
    private Customer customerDetails;

}
```
