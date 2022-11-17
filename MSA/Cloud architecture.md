# Cloud architecture

## Antifragile

- auto scaling
- microservices
- chaos engineering
- Continuous deployments

## Cloud Native Architecture

### 확장 가능한 아키텍처

- 시스템의 수평적 확정에 유연
- 확장된 서버로 시스템의 부하 분산, 가용성 보장
- 시스템 또는, 서비스 애플리케이션 단위의 패키지(컨테이너 기반 패키지)
- 모니터링

### 탄력적 아키텍처

- 서비스 생성-통합-배포, 비즈니스 환경 변화에 대응 시간 단축
- 분할 된 서비스 구조
- 무상태 통신 프로토콜
- 서비스의 추가와 삭제 자동으로 감지
- 변경된 서비스 요청에 따라 사용자 요청 처러(동적 처리)

### 장애 격리 (Fault isolation)

- 특정 서비스에 오류가 발생해도 다른 서비스에 영향 주지 않음

## Cloud Native Application

- Microservices
- CI/CD
- DevOps
- Containers

### CICD

- 지속적인 통합, CI(Continuous Integration)
    - 통합 서버, 소스 관리(SCM), 빌드 도구, 테스트 도구
    - ex) Jenkins, Team CI, Travis CI
- 지속적 배포
    - Continuous Delivery 수동 반영
    - Continuous Deployment 자동 반영
    - Pipe line
- 카나리 배포와 블루그린 배포

### DevOps

- Development
- QA
- Operations

## Factors(https://12factor.net/)

- Base Code
    - 코드를 한 곳에서 배포하기 위한 목적을 가짐
- Dependency Isolation
- Configurations
    - msa 필요한 작업 환경 설정
- LINKABLE BACKING SERVICES
- Stages of creation
    - 롤백, 자동화 시스템
- Stateless Process
- Port Binding
    - 포트에 자체 기능
- Concurrency
- Disposability
    - 인스턴스 삭제, 정상적인 종료 가능해야 된다.
- Development & Production parity
- Logs
- Admin Processes For Eventual Processes

### Factors + 3

- API first
- Telemetry - 모든 지표는 시각화 되어서 관리될 수 있어야 된다.
- Authentication and authorization