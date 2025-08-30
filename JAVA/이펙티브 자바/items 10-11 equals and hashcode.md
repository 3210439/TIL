# Item 10: equals는 일반 규약을 지켜 재정의하라

자바에서 `equals` 메서드는 객체의 논리적 동치성(logical equality)을 판단하는 데 사용됩니다. `Object` 클래스에서 제공하는 기본 `equals` 메서드는 객체의 물리적 동일성(참조 동일성)을 비교하지만, 논리적 동치성을 비교해야 하는 경우에는 `equals` 메서드를 적절히 재정의해야 합니다. 이 글에서는 `equals` 메서드를 재정의할 때 지켜야 할 규약과 주의 사항을 살펴보겠습니다.

## equals를 재정의하지 않는 것이 좋은 경우

다음과 같은 상황에서는 `equals` 메서드를 재정의하지 않는 것이 적절합니다:

- **각 인스턴스가 본질적으로 고유한 경우**  
  예를 들어, `Thread`와 같이 각 객체가 고유한 식별성을 가지는 경우에는 논리적 동치성을 비교할 필요가 없습니다.

- **논리적 동치성을 검사할 필요가 없는 경우**  
  객체의 논리적 동치성을 비교할 일이 없는 클래스에서는 `equals`를 재정의할 필요가 없습니다.

- **상위 클래스의 equals가 하위 클래스에 적합한 경우**  
  상위 클래스에서 이미 적절히 재정의된 `equals` 메서드가 하위 클래스에서도 충분히 동작한다면 재정의가 필요 없습니다. 예: `java.util.AbstractList`의 `equals`는 하위 클래스에서도 잘 동작합니다.

- **클래스가 private 또는 package-private이고 equals 호출이 없는 경우**  
  `equals` 메서드가 호출될 일이 없는 클래스라면 재정의할 필요가 없습니다. 실수로 호출되는 것을 방지하기 위해 다음과 같이 방어적으로 작성할 수 있습니다:

  ```java
  @Override
  public boolean equals(Object o) {
      throw new AssertionError("equals 메서드가 호출되지 않아야 합니다.");
  }
  ```

## equals를 재정의하는 것이 좋은 경우

`equals` 메서드는 객체의 **논리적 동치성**을 비교해야 할 때 재정의해야 합니다. 특히, 상위 클래스의 `equals`가 논리적 동치성을 비교하도록 설계되지 않은 경우가 이에 해당합니다. 예를 들어, `Set`이나 `Map`의 키로 사용되는 객체는 논리적 동치성을 기반으로 동작하므로 `equals`를 적절히 재정의해야 합니다.

## equals 메서드의 일반 규약

`equals` 메서드를 재정의할 때는 다음 다섯 가지 규약을 반드시 준수해야 합니다:

1. **반사성(reflexivity)**: null이 아닌 모든 참조 값 `x`에 대해, `x.equals(x)`는 `true`를 반환해야 합니다.
2. **대칭성(symmetry)**: null이 아닌 모든 참조 값 `x`, `y`에 대해, `x.equals(y)`가 `true`이면 `y.equals(x)`도 `true`여야 합니다.
3. **추이성(transitivity)**: null이 아닌 모든 참조 값 `x`, `y`, `z`에 대해, `x.equals(y)`가 `true`이고 `y.equals(z)`가 `true`이면, `x.equals(z)`도 `true`여야 합니다.
4. **일관성(consistency)**: null이 아닌 모든 참조 값 `x`, `y`에 대해, `x.equals(y)`를 반복 호출하면 항상 동일한 결과를 반환해야 합니다(객체의 상태가 변경되지 않는 한).
5. **null-아님**: null이 아닌 모든 참조 값 `x`에 대해, `x.equals(null)`은 `false`를 반환해야 합니다.

## equals 재정의 시 주의 사항

- **구체 클래스의 확장과 값 추가**: 구체 클래스를 확장하면서 새로운 값을 추가하는 경우, `equals` 규약을 만족시키는 것은 불가능합니다. 이를 해결하려면 상속 대신 **컴포지션**(composition)을 사용하세요.
- **신뢰할 수 없는 자원 사용 금지**: `equals` 메서드는 클래스 내부의 필드만 사용해야 하며, 외부의 신뢰할 수 없는 자원(예: 파일, 네트워크)에 의존해서는 안 됩니다.
- **입력 타입은 Object여야 함**: `equals` 메서드의 매개변수는 반드시 `Object` 타입이어야 하며, 특정 클래스 타입으로 제한하면 규약을 위반하게 됩니다.

## equals 재정의 단계

`equals` 메서드를 재정의할 때는 다음 단계를 따르는 것이 좋습니다:

1. **== 연산자로 참조 동일성 확인**: 입력이 자기 자신의 참조인지 확인합니다.
2. **instanceof 연산자로 타입 확인**: 입력이 올바른 타입인지 확인합니다.
3. **형변환**: 입력을 올바른 타입으로 형변환합니다.
4. **핵심 필드 비교**: 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 확인합니다.
5. **float/double 비교**: `float`와 `double` 필드는 각각 `Float.compare(float, float)`와 `Double.compare(double, double)`를 사용해 비교합니다.

### 예제: equals 메서드 구현

```java
@Override
public boolean equals(Object o) {
    if (o == this) return true; // 참조 동일성 확인
    if (!(o instanceof MyClass)) return false; // 타입 확인
    MyClass other = (MyClass) o; // 형변환
    return this.field1 == other.field1 && // 핵심 필드 비교
           Objects.equals(this.field2, other.field2) &&
           Float.compare(this.field3, other.field3) == 0;
}
```

- **Objects.equals**: `null` 안전한 비교를 위해 사용.
- **Float.compare/Double.compare**: 부동소수점 비교 시 정확성을 보장.

## 추가 팁

- **너무 복잡하게 구현하지 말자**: `equals` 메서드는 간단하고 명확하게 작성해야 유지보수가 쉽습니다.
- **hashCode와 함께 재정의**: `equals`를 재정의하면 반드시 `hashCode`도 재정의해야 합니다(아이템 11 참조).
- **IDE 도구 활용**: IntelliJ, Eclipse 등의 IDE는 `equals`와 `hashCode`를 자동으로 생성하는 기능을 제공합니다.

---

# Item 11: equals를 재정의하려거든 hashCode도 재정의하라

`equals` 메서드를 재정의한 클래스는 반드시 `hashCode` 메서드도 재정의해야 합니다. 그렇지 않으면 `HashMap`, `HashSet` 같은 해시 기반 컬렉션에서 객체가 예상대로 동작하지 않을 수 있습니다. 이는 `Object` 클래스의 `hashCode` 일반 규약을 위반하기 때문입니다.

## hashCode 일반 규약

`Object` 클래스의 API 문서에 명시된 `hashCode`의 규약은 다음과 같습니다:

1. **일관성**: 동일한 객체에서 `hashCode`를 여러 번 호출하면, 객체의 상태가 변경되지 않는 한 항상 동일한 값을 반환해야 합니다.
2. **equals와의 일치**: `equals` 메서드에서 같은 객체로 판단되는 두 객체는 동일한 `hashCode` 값을 반환해야 합니다.
3. **분산성**: `equals`로 다른 객체로 판단되는 객체들은 가능한 한 서로 다른 `hashCode` 값을 반환해야 합니다(필수는 아니지만 성능에 영향을 미침).

## hashCode를 재정의하지 않을 때의 문제

`equals`를 재정의했지만 `hashCode`를 재정의하지 않으면, 논리적으로 동일한 객체가 다른 해시코드를 반환할 수 있습니다. 이는 `HashMap`이나 `HashSet`에서 객체를 찾거나 저장할 때 문제를 일으킵니다. 예를 들어:

- `HashMap`에서 키로 사용된 객체를 찾을 수 없음.
- `HashSet`에서 동일한 객체가 중복으로 추가됨.

## hashCode 구현 가이드라인

1. **핵심 필드 사용**: `equals`에서 사용하는 모든 핵심 필드를 `hashCode` 계산에 포함해야 합니다. 핵심 필드를 생략하면 논리적 동치성을 보장할 수 없습니다.
2. **효율성과 분산성 고려**: 해시코드는 가능한 한 고르게 분포해야 하며, 계산이 너무 복잡하지 않아야 합니다.
3. **API 문서에 계산 방식 비공개**: `hashCode`의 구현 세부 사항을 API 문서에 공개하지 말아야 합니다. 이는 나중에 구현을 변경할 유연성을 제공합니다.

### 예제: hashCode 메서드 구현

```java
@Override
public int hashCode() {
    int result = 17; // 초기값(소수 권장)
    result = 31 * result + field1; // 기본 타입
    result = 31 * result + Objects.hashCode(field2); // 객체 필드
    result = 31 * result + Float.floatToIntBits(field3); // 부동소수점
    return result;
}
```

- **31 사용 이유**: 홀수 소수로 곱셈을 통해 해시 충돌 가능성을 줄입니다.
- **Objects.hashCode**: `null` 안전한 해시 계산.
- **Float.floatToIntBits**: 부동소수점 값을 정수로 변환.

## 추가 팁

- **AutoValue 또는 IDE 활용**: Google의 AutoValue 프레임워크나 IntelliJ, Eclipse 같은 IDE는 `equals`와 `hashCode`를 자동으로 생성해줍니다.
- **성능 최적화**: 지나치게 복잡한 `hashCode` 계산은 성능을 저하시킬 수 있으므로 적절히 균형을 맞추세요.
- **캐싱 고려**: 불변 객체의 경우 `hashCode` 값을 캐싱하여 성능을 최적화할 수 있습니다.

## 결론

`equals` 메서드를 재정의할 때는 반드시 `hashCode`도 재정의해야 합니다. 이는 해시 기반 컬렉션(`HashMap`, `HashSet` 등)에서 올바른 동작을 보장하기 위해 필수적입니다. `hashCode`는 일반 규약을 준수하고, `equals`에서 사용하는 핵심 필드를 모두 포함해야 합니다. AutoValue나 IDE의 자동 생성 기능을 활용하면 규약을 준수하는 `equals`와 `hashCode`를 쉽게 구현할 수 있습니다.
