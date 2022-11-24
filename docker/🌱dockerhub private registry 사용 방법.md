# 🌱dockerhub private registry 사용 방법

### Docker의 핵심 기능

Docker의 고유한 기능 중 하나는 Docker 컨테이너가 애플리케이션을 실행하기 위한 동일한 가상 환경을 제공하는 것이다.

### Dockerhub registry란?

- 레지스트리는 stateless, 확장성이 뛰어난 서버 측 애플리케이션으로, 도커 이미지를 배포할 수 있습니다. 레지스트리는 오픈 소스다.
- private docker registry 설정은 무료이며, 개인 레지스트리에서 이미지에 액세스하는 모든 명령은 단순하며 도커 허브의 명령과 거의 동일하다.

### 사용하는 이유

- 어디에 이미지를 저장할지 엄격하게 관리한다.
- 이미지 배포 파이프라인을 완전히 소유한다.
- 이미지 저장 및 배포를 사내 개발 워크플로우에 긴밀하게 통합할 수 있다.

### 사용하기 전 설정

```kotlin
vi /etc/docker/daemon.json
```

명령어를 통해 아래 명령어를 삽입한다.

```kotlin
{
    "insecure-registries":[
        "localhost:5000"
    ]
}
```

해당 변경을 적용하기 위해서 docker Daemon 파일을 리로드 해야한다.

```kotlin
sudo systemctl daemon-reload
```

### 사용을 위한 명령어

private resistry를 실행시킵니다.

```kotlin
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

### Private Registry에 이미지 push 하기

테스트를 위해서 centos image를 다운로드한다.

```kotlin
docker pull centos
```

이미지에 태그를 사용한다.

```kotlin
docker tag centos:latest localhost:5000/baeldung-centos
```

이미지를 push 한다.

```kotlin
docker push localhost:5000/baeldung-centos
```

마지막으로 도커 이미지가 잘 다운로드 되는지 확인한다.

```kotlin
docker pull localhost:5000/baeldung-centos
```

### Private ****Registry****를 위해서 인증 추가하기

만약 외부에 함부로 노출 되면 안되는 이미지 같은 경우 외부로부터 보호가 필요합니다. 그럴경우 registry에 접근할 때 인증이 필요하며 htpasswd 인증이 필요합니다.

먼저 Docker 레지스트리 자격 증명을 저장할 별도의 디렉터리를 생성합니다.

```kotlin
mkdir -p Docker_registry/auth
```

아래 명령어를 통해서 htpasswd 를 통해 인증 받은 유저를 생성합니다.

해당 유저에 관한 자세한 정보는 auth/htpasswd 파일에 저장됩니다.

```kotlin
cd Docker_registry && docker run \
> --entrypoint htpasswd \
> httpd:2 -Bbn baeldung-user baeldung > auth/htpasswd
```

이제 실행한 레지스트리를 종료합니다.

```kotlin
docker stop 레지스트리 이미지 & docker rm 레지스트리 이미지
```

보안이 적용된 레지스트리를 다시 실행합니다.

```kotlin
docker run -itd \
  -p 5000:5000 \
  --name registry \
  -v "$(pwd)"/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry
```

로그인 방법

```kotlin
docker login  localhost:5000 -u baeldung-user -p baeldung
```

### 외부에서 로그인하기 위한 설정

```kotlin
vi /etc/docker/daemon.json
```

```kotlin
{

    "insecure-registries": ["서버IP:5000"]

}
```

daemon.json 파일을 수정하였다면,  도커를 재시작 한다.

```kotlin
systemctl restart docker
```

만약 systemctl이 안되면 아래 명령어를 사용해보자

```kotlin
service docker restart
```

참조

[https://docs.docker.com/registry/](https://docs.docker.com/registry/)

[https://www.baeldung.com/ops/docker-private-registry](https://www.baeldung.com/ops/docker-private-registry)
