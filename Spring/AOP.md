# AOP

## 관점 지향 프로그래밍 - Aspect Oriented Programming

---

**AOP란?**

AOP는 Aspect Oriented Programming의 약자로 관점 지향 프로그래밍이라고 불린다. 관점 지향은 어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 모듈화 하겠다는 것이다.

- 비즈니스 로직에 핵심적이지 않은 동작들을 신경 쓰지 않고 코딩할 수 있게 합니다.
- 특정한 함수 호출 전이나 후에 뭔가 공통적인 처리가 필요하다면 → AOP
- OOP로 처리하기에는 다소 까다로운 부분을 AOP라는 처리 방식을 도입하여 손쉽게 공통 기능을 추가/수정/삭제 할 수 있도록 함
- AOP 같은 경우 코드를 처음 보는 입장에서 이해하기 어렵게 될 수 있음으로 신중하게 적용해야 한다.

### AOP의 기본 개념

---

**Aspect**

여러 클래스나 기능에 걸쳐서 있는 관심사, 그리고 그것들을 모듈화함

AOP 중에서 가장 많이 활용되는 부분은 @Transactional (트랜잭션 관리) 기능

**Advice**

조언, AOP에서 실제로 적용하는 기능(로깅, 트랜잭션, 인증 등)을 뜻함

**Join point**

모듈화된 특정 기능이 실행될 수 있는 연결 포인트

**Pointcut**

Join point 중에서 해당 Aspect를 적용할 대상을 뽑을 조건식

예) 어노테이션이 붙어 있는가?

**Target Object**

Advice가 적용될 대상 오브젝트

**AOP Proxy**

대상 오브젝트에 Aspect를 적용하는 경우 Advice를 덧붙이기 위해 하는 작업을 AOP Proxy라고 한다.

주로 CGLIB(Code Generation Library, 실행 중에 실시간으로 코드를 생성하는 라이브러리) 프록시를 사용하여 프록싱 처리를 한다.

**Weaving**

Advice를 비즈니스 로직 코드에 삽입하는 것을 말한다.

### AspectJ 지원

---

AspectJ는 AOP를 제대로 사용하기 위해 꼭 필요한 라이브러리

기본적으로 제공되는 Spring AOP로는 다양한 기법(Pointcut 등)의 AOP를 사용할 수 없음

**Aspect의 생성**

```jsx
import org.aspectj.lang.annotation.Aspect;

@Aspect
@Component
public class UseAspect{
}
```

**Pointcut 선언**

```jsx
import org.aspectj.lang.annotation.Aspect;

@Aspect
@Component
public class UseAspect{

		@Pointcut("excution(* transfer(..))")
		private void anyOldTransfer() {}
}
```

- 해당 Aspect의 Advice(실행할 액션)이 적용될 Joint point를 찾기 위한 패턴 또는 조건 생성
- 포인트 컷 표현식이라고 부른다.

**Pointcut 결합**

```jsx
import org.aspectj.lang.annotation.Aspect;

@Aspect
@Component
public class UseAspect{

		@Pointcut("excution(public * *(..))")
		private void anyPublicOperation() {} // public 메서드 대상 포인트 컷

		@Pointcut("within(com.xyz.myapp.trading..*)")
		private void anyOldTransfer() {} // 특정 패키지 대상 포인트 컷

		@Pointcut("anyPublicOperation() && inTrading()")
		private void anyOldTransfer() {} // 위의 두 조건을 and(&&) 조건으로 결합한 포인트 컷
}
```

## Advice 정의

포인트컷들을 활용하여 포인트컷의 전/후/주변에서 실행될 액션을 정의한다.

### Before Advice

dataAccessOperation()이라는 미리 정의된 포인트 컷의 바로 전에 doAccessCheck가 실행

```jsx
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.AfterReturning;

@Aspect
public class AfterReturningExample {

	@AfterReturning("com.xyz.myapp.CommonPointcuts.dataAccessOperation()")
	public void doAccessCheck() {
		// ...
	}
}
```

### After Returning Advice

dataAccessOperation()라는 미리 정의된 포인트컷에서 return이 발생된 후 실행

```jsx
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.AfterReturning;

@Aspect
public class AfterReturningExample {

	@AfterReturning("com.xyz.myapp.CommonPointcuts.dataAccessOperation()")
	public void doAccessCheck() {
		// ...
	}
}
```

### Around Advice

businessService()라는 포인트컷의 전/후에 필요한 동작을 추가함

```jsx
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.ProceedingJoinPoint;

@Aspect
public class AroundExample {

	@Around("com.xyz.myapp.CommonPointcuts.businessService()")
	public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
		// start stopwatch
		Object retVal = pjp.proceed();
		// stop stopwatch
		return retVal;
	}
}
```