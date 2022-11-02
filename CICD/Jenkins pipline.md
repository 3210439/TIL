# Jenkins pipline

젠킨스 2 버전 이상부터 pipline을 재대로 활용할 수 있다.

젠킨스 공식 홈페이지에서 CD를 서포트 하기 위해 사용된다.

단계적인 작업을 지원

Jenkinsfile로 작성되며 2가지 타입이 존재한다.

### Scripted pipeline

- inject groovy script
- Java API reference
- 개발자에게 자유도가 높지만 작업의 명세가 복잡하여 선언적 파이프라인이 나타난다.

### Declarative pipeline

- Jenkins DSL
- isolate complex logic into Jenkins plugin
- 간단하고 명로하다.

이 글에서는 Declarative pipeline에 대해서 깊게 다루겠다.

Jenkins pipeline의 문법은 세션과 지시어로 나뉜다.

### Section

- agent
- stages
    - 순차적인 작업의 명세인 stage 들의 묶음
- steps
    - stage안에서의 실행되는 단계
- post

### Directive(지시어)

- parameters
- environments
- when
- …

### Agent

- 어디서 작업을 실행할지 지정한다.
- pipeline or stage가 실행될 노드 지정
- 위치에 따라 역할이 다르다.
- 위에 전체 적용 각 영역에 있는 agent는 그영역에서만
    - none
    - any
    - label
    - node
    - docker
    - dockerfile
    - kubernetes
    

### Post

- 위치에 따라 stages들의 작업이 끝난 후 추가적인 steps 혹은 stage에 steps들의 작업이 끝난 후 추가적인 step
- 여러가지 조건을 걸 수 있다.
- always(항상 실행해라)
- changed
- fixed
- regression
- aborted
- failure
- success
- unstable
- unsuccessful
- cleanup

### enviroment

- key = value
- pipline 내부에서 사용할 환경변수 설정
- credentials() 를 통해 Jenkins credential에 접근 가능

### prameters

- pipeline을 trigger할 때 입력 받아야할 변수를 정의
- Type
    - string
    - text
    - booleanParam
    - choice
    - password
- when
    - stage를 실행 할 조건 설정