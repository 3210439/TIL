# Jenkins

## 개요

---

무료, 자동화 서비스 소프트웨어 배포를 자동화 해주며 무료이다.

- 오픈 소스란 특성 때문에 다양한 reference 존재한다.
- 다양한 플러그인 제공한다.
    - Pipeline
    - Authentication/Authorization
    - Git
    - Docker
- 다양하게 확장 가능하다.

## 어떻게 구현하는지

---

Jenkins installation by docker

```jsx
version: '3.9'
services:
	jenkins:
	image: jenkins/jenkins:latest
	container_name: jenkins
	envirement:
		- "TZ=Asia/Seoul"
	ports:
		- "8080:8080"
	volumnes:
		- "./data:/var/jenkins_home"
```

**Jenkins 홈페이지에서의 기능 설명**

`Jenkins 관리 → 시스템 설정` 

- 시스템 전체적인 것을 관리할 수 있다.
- url
- github server
- SMTP 서버 ( E-mail로 알려줌)

`Jenkins 관리 → Plugin 관리`

- 원하는 플러그인 목록에서 설치하거나 제거할 수 있다.

`Jenkins 관리 → Credentials` 

- 패스워드, 유저네임 설정
- aws api를 사용할 경우 secret key
- github의 access 토큰 등등 저장

`메인 화면`

- 탭을 통해서 여러가지 잡을 넣을 수 있다.

`freestyle project`

- 여러가지 설정할 수 있다.

`Pipeline project`

- freestyle 과 비슷하지만 pipeline의 Definition을 스크립트 형태로 설정할 수 있다.