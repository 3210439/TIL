# Spring Cloud Eureka 간략한 정리

Status: In progress

## Eureka란?

Eureka는 클라우드 환경의 다수의 서비스(예: API 서버)들의 로드 밸런싱 및 장애 조치 목적을 가진 미들웨어 서버이다.

참조

[[Spring Cloud] Eureka 개념 및 예제](https://cjw-awdsd.tistory.com/m/52)

Eureka는 로드밸런싱 및 미들웨어 기능을 제공하는 Eureka server 와 각각의 나눠진 서비스들 Eureka client로 나뉜다.

### Eureka Server 사용법

- main 메소드에 `@EnableEurekaServer` 어노테이션을 추가한다.
- yml 파일을 아래와 같이 세팅한다.

```
server:
  port: 8761

spring:
  application:
    name: discoveryservice

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

- yml파일의 얄팍한 설명
    - server의 포트번호는 8761번이다.
    - 애플리케이션이름을 discoveryservice로 설정한다.
    - 마지막 설정은 Eureka Server에 접속하게 되면 관리하고 있는 클라이언트 정보를 가지고있는 창이 나타나는데 위 설정을 통해 서버의 아이피 주소가 나타나지 않게 할 수 있다.

### Eureka client 사용법

- main 메소드에 `@EnableDiscoveryClient` 어노테이션을 추가한다.
- yml 파일을 아래와 같이 세팅한다.

```
server:
  port: 0

spring:
  application:
    name: user-service
eureka:
  instance:
    instance-id: ${spring.cloud.client.hostname}:${spring.application.instance_id:${random.value}}
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://127.0.0.1:8761/eureka
```

- yml 파일의 얄팍한 설명
    - 포트 번호 0번으로 두게되면 랜덤으로 포트 번호가 설정되게 된다. 따라서 개발자가 의도적으로 포트번호를 설정할 필요 없어서 로드 밸런싱에 효과적이다.
    - application 이름을 user-service로 설정해두었다. eureka 서버에서 해당 이름을 통해 구별할 수 있다.
    - instance-id를 위와같이 설정함으로써 랜덤 포트를 사용하더라도 여러개의 클라이언트로 서버에서 식별할 수 있게 된다.
    - eureka-server의 정보를 입력하며 server에서 클라이언트를 식별할 수 있도록 설정하였다.

### 터미널에서 사용 방법

터미널에서 메이븐으로 9004포트에 jar 파일 실행하는 명령어

```jsx
java -jar -Dserver.port=9004 /.target/user-service-0.0.1-SNAPSHOT.jar
```

###