## 아이템 5: 자원을 직접 명시하지 말고 의존 객체 주입을 사용하기

클래스의 동작이 사용하는 자원에 따라 달라질 때, **정적 유틸리티 클래스**나 **싱글턴 패턴**은 적합하지 않습니다. 이러한 방식은 유연성과 테스트 가능성을 저하시킵니다.

### 핵심 원칙
- 클래스가 하나 이상의 자원에 의존하고, 그 자원이 동작에 영향을 미친다면, 자원을 클래스 내부에서 직접 생성하지 말고 **생성자(또는 정적 팩터리, 빌더)**를 통해 필요한 자원을 주입하세요.
- 이를 **의존 객체 주입(Dependency Injection)**이라고 하며, 다음과 같은 이점을 제공합니다:
  - **유연성**: 자원을 쉽게 교체할 수 있어 다양한 환경에서 클래스를 재사용 가능.
  - **재사용성**: 동일한 클래스를 다른 자원으로 동작하도록 설정 가능.
  - **테스트 용이성**: 테스트 시 모의 객체(mock)를 주입하여 독립적인 단위 테스트 가능.

### 예시
```java
// 나쁜 예: 자원을 클래스 내부에서 직접 생성
class SpellChecker {
    private static final Dictionary dictionary = new Dictionary();
    private SpellChecker() {} // 싱글턴
}

// 좋은 예: 의존 객체 주입
class SpellChecker {
    private final Dictionary dictionary;
    public SpellChecker(Dictionary dictionary) {
        this.dictionary = dictionary;
    }
}
```

### 추가 팁
- 의존 객체 주입은 스프링 프레임워크 같은 DI 컨테이너를 사용하면 더욱 편리합니다.
- 팩터리 메서드나 빌더 패턴을 활용해 복잡한 객체 생성 로직을 캡슐화할 수 있습니다.

---

## 아이템 6: 불필요한 객체 생성을 피하라

불필요한 객체 생성은 성능 저하를 초래할 수 있으므로, 객체를 재사용하거나 적절한 자료형을 선택해 이를 방지하세요.

### 주요 사례
1. **String 객체 생성**
   - `new String("example")` 대신 `"example"` 리터럴을 사용하세요. String은 불변 객체이므로 같은 문자열 리터럴은 JVM에서 재사용됩니다.
   - `String.matches()`는 정규표현식을 내부적으로 컴파일하므로, 성능이 중요한 반복 작업에서는 `Pattern.compile()`을 사용해 정규표현식을 미리 컴파일하세요.
     ```java
     // 나쁜 예
     String s = "example";
     if (s.matches(".*example.*")) {}

     // 좋은 예
     Pattern pattern = Pattern.compile(".*example.*");
     if (pattern.matcher(s).matches()) {}
     ```

2. **오토박싱(Autoboxing)**
   - 기본 타입(`int`, `long` 등)을 사용할 때 박싱된 타입(`Integer`, `Long` 등)을 사용하면 불필요한 객체가 생성됩니다.
   - 예:
     ```java
     // 나쁜 예: 오토박싱으로 불필요한 Long 객체 생성
     Long sum = 0L;
     for (long i = 0; i < 1000; i++) {
         sum += i; // Long 객체를 반복 생성
     }

     // 좋은 예: 기본 타입 사용
     long sum = 0L;
     for (long i = 0; i < 1000; i++) {
         sum += i;
     }
     ```

### 추가 팁
- 불변 객체는 캐싱하거나 풀(pool)을 사용해 재사용하세요 (예: `Integer.valueOf()`).
- 객체 생성 비용이 큰 경우, 객체 풀(Object Pool) 패턴을 고려할 수 있습니다.

---

## 아이템 7: 다 쓴 객체 참조를 해제하라

다 쓴 객체의 참조를 유지하면 **메모리 누수(Memory Leak)**가 발생할 수 있습니다. 이를 방지하려면 참조를 적절히 해제해야 합니다.

### 메모리 누수 주요 원인
1. **직접 메모리 관리 클래스**
   - 예: 사용자 정의 스택에서 `pop` 메서드가 객체 참조를 제거하지 않으면 누수가 발생.
     ```java
     // 나쁜 예
     public Object pop() {
         return elements[--size]; // 참조가 유지됨
     }

     // 좋은 예
     public Object pop() {
         Object result = elements[--size];
         elements[size] = null; // 참조 해제
         return result;
     }
     ```

2. **캐시**
   - 캐시에 저장된 객체가 더 이상 필요 없어도 참조가 유지되면 메모리 누수가 발생.
   - 해결책: `WeakHashMap`이나 `SoftReference`를 사용해 GC가 필요 없는 객체를 회수하도록 함.

3. **리스너 및 콜백**
   - 등록된 리스너나 콜백이 해제되지 않으면 메모리 누수가 발생.
   - 해결책: 명시적으로 리스너를 제거하거나 약한 참조(`WeakReference`) 사용.

### 추가 팁
- 객체 참조를 `null`로 설정하는 것은 예외적인 경우에만 사용하세요. 일반적으로는 지역 변수의 스코프를 최소화하거나, 적절한 자료구조를 사용해 참조를 관리하세요.
- 메모리 누수 디버깅 시, 힙 덤프 분석 도구(예: VisualVM, Eclipse MAT)를 활용하세요.

---

## 아이템 8: finalizer와 cleaner 사용을 피하라

자바는 객체 소멸을 위해 `finalizer`(자바 8 이하)와 `cleaner`(자바 9 이상)를 제공하지만, 일반적으로 사용하지 않는 것이 좋습니다.

### 문제점
1. **예측 불가능성**
   - `finalizer`와 `cleaner`는 즉시 실행되지 않을 수 있으며, 실행 시점을 보장할 수 없습니다.
   - 제때 실행되어야 하는 작업(예: 파일 닫기)에는 적합하지 않습니다.

2. **성능 문제**
   - `finalizer`와 `cleaner`는 GC 프로세스를 방해해 성능 저하를 초래합니다.

3. **보안 문제**
   - `finalizer`는 **finalizer 공격**에 취약합니다. 악의적인 서브클래스가 `finalize` 메서드를 오버라이드해 객체를 부활시킬 수 있습니다.
   - 해결책: `final`로 선언된 빈 `finalize` 메서드를 추가해 공격 방지.
     ```java
     protected final void finalize() {} // finalizer 공격 방어
     ```

### 대안
- **AutoCloseable 구현**
  - `try-with-resources`를 사용해 자원을 명시적으로 해제.
    ```java
    try (Resource resource = new Resource()) {
        // 자원 사용
    } // 자동으로 close() 호출
    ```

- **Cleaner 사용 시 주의**
  - `cleaner`는 `finalizer`보다 덜 위험하지만, 여전히 예측 불가능합니다.
  - 안전망 역할이나 중요하지 않은 네이티브 자원 회수에만 사용하세요.
  - 예: 네이티브 메모리 해제를 위한 `Cleaner` 등록.
    ```java
    import java.lang.ref.Cleaner;

    public class Resource implements AutoCloseable {
        private static final Cleaner cleaner = Cleaner.create();
        private final Cleaner.Cleanable cleanable;

        public Resource() {
            cleanable = cleaner.register(this, () -> cleanup());
        }

        private static void cleanup() {
            // 네이티브 자원 정리
        }

        @Override
        public void close() {
            cleanable.clean();
        }
    }
    ```

### 추가 팁
- `try-with-resources`와 `AutoCloseable`을 사용하면 명시적 자원 관리가 훨씬 간단하고 안전합니다.
- `finalizer`와 `cleaner`는 반드시 필요한 경우에만 사용하고, 성능 및 안정성 테스트를 철저히 수행하세요.
