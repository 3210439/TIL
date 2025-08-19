# 아이템 1: 생성자 대신 정적 팩터리 메서드를 고려하라

클래스의 인스턴스를 생성할 때, 생성자(`new`) 대신 **정적 팩터리 메서드**(static factory method)를 사용하면 더 유연하고 효율적인 설계가 가능합니다. 정적 팩터리 메서드는 `public static` 메서드로, 객체를 생성해 반환합니다. 아래는 장점, 단점, 그리고 명명 규칙을 정리한 내용입니다.

## 정적 팩터리 메서드의 장점

1. **의미 있는 이름을 가질 수 있다**  
   메서드 이름을 통해 객체 생성 의도를 명확히 전달할 수 있습니다.  
   **예시**: `BigInteger.valueOf(100)`는 `new BigInteger("100")`보다 직관적입니다.

2. **인스턴스 생성을 제어할 수 있다**  
   새 객체를 매번 생성하지 않고, 캐싱된 객체를 재사용하거나 싱글톤 패턴을 적용해 메모리 효율성을 높이고 인스턴스 수를 관리할 수 있습니다.  
   **예시**: `Boolean.valueOf(true)`는 항상 `Boolean.TRUE`를 반환합니다.

3. **하위 타입 객체를 반환할 수 있다**  
   반환 타입을 인터페이스나 상위 클래스로 지정해 하위 타입 객체를 반환, 유연성을 높입니다.  
   **예시**: `Collections.unmodifiableList()`는 `List` 인터페이스를 반환하지만 내부 구현체를 선택합니다.

4. **입력 매개변수에 따라 다른 객체를 반환할 수 있다**  
   매개변수에 따라 적합한 클래스의 객체를 동적으로 반환합니다.  
   **예시**: `EnumSet.of()`는 요소 개수에 따라 `RegularEnumSet` 또는 `JumboEnumSet`을 반환합니다.

5. **미래의 확장성을 보장한다**  
   메서드 작성 시점에 구체적인 반환 클래스가 없어도, 나중에 새로운 클래스를 추가해 반환 가능합니다.  
   **예시**: `DriverManager.getConnection()`은 드라이버 구현체를 동적으로 반환합니다.

## 정적 팩터리 메서드의 단점

1. **상속이 불가능할 수 있다**  
   `public` 또는 `protected` 생성자가 없으면 하위 클래스를 만들 수 없습니다.  
   **해결책**: 상속 대신 컴포지션(composition)을 활용하거나, 필요한 경우 `protected` 생성자를 추가합니다.

2. **발견 가능성이 낮다**  
   생성자와 달리 API 문서에서 눈에 띄지 않아 사용이 어려울 수 있습니다.  
   **해결책**: 표준화된 명명 규칙을 사용해 메서드 이름을 명확히 설정합니다.

## 정적 팩터리 메서드의 명명 규칙

명확하고 직관적인 이름을 사용해 발견 가능성을 높이는 패턴:
- `from`: 단일 매개변수를 받아 객체로 변환 (예: `Date.from(instant)`)
- `of`: 여러 매개변수를 받아 객체 생성 (예: `Set.of(elements)`)
- `valueOf`: 값 기반 객체 생성 (예: `BigInteger.valueOf(100)`)
- `instance` 또는 `getInstance`: 싱글톤/캐싱된 인스턴스 반환 (예: `Singleton.getInstance()`)
- `create` 또는 `newInstance`: 새로운 인스턴스 생성 (예: `Object.newInstance()`)
- `getType`: 특정 타입 객체 반환 (예: `Files.getFileStore()`)
- `newType`: 특정 타입의 새 객체 반환 (예: `BufferedReader.newBufferedReader()`)
- `type`: 간결한 타입 이름 (예: `Collections.list()`)

## 언제 정적 팩터리 메서드를 사용할까?

- 객체 생성 로직이 복잡하거나, 조건에 따라 다른 객체를 반환해야 할 때
- 인스턴스 캐싱이나 싱글톤 패턴으로 메모리 효율성을 높이고자 할 때
- API를 간결히 유지하면서 유연한 설계를 원할 때
- 반환 타입을 추상화해 미래 확장성을 고려할 때

## 코드 예시

```java
public class User {
    private final String name;
    private final int age;

    private User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 정적 팩터리 메서드: 이름이 있는 객체 생성
    public static User withNameAndAge(String name, int age) {
        return new User(name, age);
    }

    // 정적 팩터리 메서드: 캐싱된 객체 반환
    private static final User ANONYMOUS = new User("Anonymous", 0);
    public static User getAnonymousUser() {
        return ANONYMOUS;
    }
}
