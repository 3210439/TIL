# 자바 ORM 표준 JPA 프로그래밍 기본편 정리

### JPA 특징

- JPA는 특정 데이터베이스에 종속되지 않는다.
- 객체지향적인 프로그래밍을 할 수 있도록 도와준다.

### Entity Manager Factory

- 이름 그대로 Entity Manager를 생성하는 객체
- 싱글턴 방식으로 사용
- Entity Manager는 쓰레드간에 공유하면 안된다.
- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행해야한다.

### 영속성 컨텍스트(Persistence Context)

- 엔티티를 영구 저장하는 환경
- EntityManager.persist(entity)
- 논리적인 개념
- 엔티티 매니저를 통해서 영속성 컨텍스트에 접근할 수 있다.
- 스프링 프레임워크 같은 컨테이너 환경에서는 엔티티 매니저와 영속성 컨텍스트가 N:1 관계를 가진다.

### 영속성 컨텍스트의 이점

- 1차 캐시
    - persist가 진행되면 바로 DB로 저장되지 않고 영속성 컨텍스트 내부의 1차 캐시에 저장된다. 그리고 해당되는 sql은  영속성 컨텍스트 내부의 쓰기 지연 SQL에 저장된다.
    - find를 사용해서 조회하면 또한 1차캐시에 저장된다.
    - find 사용시 이미 1차 캐시에 저장되있으면 해당 캐시가 사용된다.
- 동일성(identity) 보장
- 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
- 변경 감지(Dirty Checking)
- 지연 로딩(Lazy Loading)

### Entity Manager를 사용한 업데이트 방법

- 업데이트 하기전 find를 통해서 해당 entity를 조회하게 된다. 그러면 영속성 상태가 되며 1차 캐시에 해당 엔티티가 올라가게 된다. 이 때 트랜잭션이 걸려있고 값을 변경하게 되면 트랜잭션이 끝나는 시점에 1차 캐시의 처음값이 담긴 스냅샷과 현재의 값을 비교하여 변경되면 update SQL을 쓰기 지연 SQL 저장소에 저장하게 되고 flush할때 DB로 update 쿼리가 실행되게 된다.

### flush란?

- 영속성 컨텍스트의 변경내용을 데이터베이스에 반영하는 작업이다.
- 플러시가 발생되면 아래 동작이 실행된다.
    - 변경 감지
    - 수정된 엔티티 쓰기 지연 SQL 저장소에 등록
    - 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송
        
        (등록, 수정, 삭제 쿼리)
        

### 영속성 컨텍스트가 flush 되는 시점

- entityManager.flush()가 실행될 때
- 트랜잭션이 커밋 될 때
- JPQL 쿼리 실행될 때 (모드를 설정하여 트랜잭션 커밋 시점에 실행되도록 변경할 수 있다.)

### 엔티티의 생명주기

- 비영속 (new/transient)
    - 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
    - 단순히 객체를 생성한 상태
- 영속 (managed)
    - 영속성 컨텍스트에 관리되는 상태
    - Entity Manager를 통해 persist를 수행한 상태
- 준영속(detached)
    - 영속성 컨텍스트에 저장되었다가 분리된 상태
    - Entity manager의 detach, clear, close이 실행되면 영속 → 준영속이 된다.
- 삭제 (removed) - 삭제된 상태

### JPA 어노테이션

- @Entity: JPA가 관리할 객체
- @Id: 데이터베이스 PK와 매핑
- @Table: 은 엔티티와 매핑할 테이블 지정 ex) @Table(name = “hello”)
- @Column 컬럼 매핑
- @Temporal 날짜 타입 매핑
    - 요즘은 LocalDate, LocalDateTime을 사용하기에 사용 x
- @Enumerated enum 타입 매핑
    - 결과적으로 value = EnumType.STRING으로 사용해야 된다.
- @Lob BLOB,  CLOB 매핑
    - @Lob에는 지정할 수 있는 속성이 없다.
    - 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑
    - CLOB: String, char[], java.sql.CLOB
    - BLOB: byte[], java.sql.BLOB
- @Transient 특정 필드를 컬럼에 매핑하지 않음(자바에서만 사용되는 변수)
    - 필드 매핑 x
    - 데이터베이스에 저장 x, 조회 x
    - 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용

### 데이터베이스 스키마 자동 생성

- DDL을 애플리케이션 실행 시점에 자동으로 생성한다.
- 테이블 중심 → 객체 중심으로 변경
- 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL을 생성한다.
- 이렇게 생성된 DDL은 개발 장비에서만 사용한다.
- 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용된다.

### hibernate.hbm2ddl.auto

| create | 기존테이블 삭제 후 다시 생성 (DROP + CREATE) |
| --- | --- |
| create-drop | create와 같으나 종료시점에 테이블 DROP |
| update | 변경분만 반영(운영 DB에서는 사용하면 안됨) |
| validate | 엔티티와 테이블이 정상 매핑되었는지만 확인 |
| none | 사용하지 않음 |
- DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고 JPA의 실행로직에는 영향을 주지 않는다.

### @SequenceGenerator

- 아이디 값 위나 클래스 위에 사용
- 주의: allocationSize 기본값은 50이다.
- name : 식별자 생성기 이름
- sequenceName 데이터베이스에 등록되어 있는 시퀸스 이름
- initialValue DDL 생성 시에만 사용됨, 시퀸스 DDL을 생성할 때 처음 1 시작하는 수를 지정한다.
- allocationSize 시퀸스 한 번 호출에 증가하는 수 (성능 최적화에 사용된다. 데이터베이스 시퀸스 값이 하나씩 증가하도록 설정되길 원하면 이 값을 반드시 1로 설정해야한다.)
    - 만약 위의 값을 1로 설정하면 데이터를 생성하고 db에 저장하고 조회하는 것을 하나의 데이터마다 수행하여 성능은 떨어질 수 있다.
- catalog, schema: 데이터베이스 catalog, schema 이름