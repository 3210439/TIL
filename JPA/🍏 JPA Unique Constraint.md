# 🍏 JPA Unique Constraint 사용하기

유니크 제약조건을 사용하는 이유는 중복된 값 입력을 방지하기 위해서다.

 유니크 제약 조건을 사용할 수 있는 어노테이션에는 2가지가 있다.

- @Column(unique=true)
- @UniqueConstraint

첫번째 어노테이션으로는 하나의 컬럼에 대해서 유니크 제약 조건을 두번째는 여러 컬럼에 유니크 제약조건을 걸 수 있다.

## 기본 키와 유니크 제약조건의 차이점은?

Unique Constraint를 사용하면 열 또는 열 조합의 데이터가 각 행에 대해서 고유하다. 기본 키 또한 각 행에 대하여 고유한 제약 조건이 있다. 기본 키 제약 조건은 각 테이블마다 하나의 열에 대해서만 조건을 가질 수 있지만 유니크 제약조건은 여러개의 컬럼을 지정할 수 있다. 

우리가 만드는 제약 조건은 테이블을 생성할 때나 insert, update, delete를 사용할 때 사용될 수 있다.

## 싱글 컬럼 그리고 여러개의 컬럼 재약 조건이란 무엇인가?

unique constraints는 열 제약 조건 또는 테이블 제약 조건일 수 있다. 테이블 레벨에서 여러개 컬럼 제약 조건을 사용할 수 있다. 

- 컬럼 레벨 제약조건은 한개의 컬럼에 적용된다.
- 테이블 레벨의 제약 조건은 전체 테이블에 적용된다.

## 테이블 제약조건

복합 유니크 키란 컬럼의 조합으로 만들어진 유니크 키다. 복합 유니크 키를 사용하기 위해서 제약조건을 컬럼 대신 테이블에 적용할 수 있다. 우리는 @UniqueConstraint annotation을 사용해서 이를 적용 시킬 수 있다. 

Unique Constraint 은 DDL에 포함 되어야 된다.

> @Target(value={})
@Retention(value=RUNTIME)
> 
> 
> **public**
> 
> @interface UniqueConstraint {
>     String **name**() **default** "";
>     String[] columnNames();
> }
> 

위의 UniqueConstraint 어노테이션의 구성을 살펴보면 columnNames라는 문자열 배열이 있다. 해당 배열에

복합 유니크 키로 사용하고 싶은 컬럼 이름의 문자열을 넣으면 된다. 사용은 아래와 같이 사용하면 된다.

> @Table(uniqueConstraints = { @UniqueConstraint(columnNames = { "personNumber", "isActive" }) })
> 

위와 같이 사용해도 상관은 없지만 위와 같이 UniqueConstraint의 파라미터중 name에 값을 넣지 않으면 값이 아래와 같이 provider-generated value가 되버린다.

 

> [main] DEBUG org.hibernate.SQL -
    alter table Person add constraint UK5e0bv5arhh7jjhsls27bmqp4a
> 
> 
> **unique**
> 
> (personNumber, isActive)
> 

해당 부분이 거슬리면 아래와 같이 사용하면 된다.

> @Table(uniqueConstraints = { @UniqueConstraint(name = "UniqueNumberAndStatus", columnNames = { "personNumber", "isActive" }) })
> 

## 하나의 엔티티에 여러개의 복합 유니크키 사용하기

테이블은 복합 유니크 키를 여러개 가질 수 있다. 사용방법은 아래와 같다.

> @Table(uniqueConstraints = {
@UniqueConstraint(name = "UniqueNumberAndStatus", columnNames = {"personNumber", "isActive"}),
@UniqueConstraint(name = "UniqueSecurityAndDepartment", columnNames = {"securityNumber", "departmentCode"})})
> 

## 정리

Unique constraint를 사용해서 두 개 이상의 행이 열 또는 열 집합에서 동일한 값을 가질 수 없게 만들 수 있다.

참조

[https://www.baeldung.com/jpa-unique-constraints](https://www.baeldung.com/jpa-unique-constraints)