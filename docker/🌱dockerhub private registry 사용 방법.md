# ๐ฑdockerhub private registry ์ฌ์ฉ ๋ฐฉ๋ฒ

### Docker์ ํต์ฌ ๊ธฐ๋ฅ

Docker์ ๊ณ ์ ํ ๊ธฐ๋ฅ ์ค ํ๋๋ Docker ์ปจํ์ด๋๊ฐ ์ ํ๋ฆฌ์ผ์ด์์ ์คํํ๊ธฐ ์ํ ๋์ผํ ๊ฐ์ ํ๊ฒฝ์ ์ ๊ณตํ๋ ๊ฒ์ด๋ค.

### Dockerhub registry๋?

- ๋ ์ง์คํธ๋ฆฌ๋ stateless, ํ์ฅ์ฑ์ด ๋ฐ์ด๋ ์๋ฒ ์ธก ์ ํ๋ฆฌ์ผ์ด์์ผ๋ก, ๋์ปค ์ด๋ฏธ์ง๋ฅผ ๋ฐฐํฌํ  ์ ์์ต๋๋ค. ๋ ์ง์คํธ๋ฆฌ๋ ์คํ ์์ค๋ค.
- private docker registry ์ค์ ์ ๋ฌด๋ฃ์ด๋ฉฐ, ๊ฐ์ธ ๋ ์ง์คํธ๋ฆฌ์์ ์ด๋ฏธ์ง์ ์ก์ธ์คํ๋ ๋ชจ๋  ๋ช๋ น์ ๋จ์ํ๋ฉฐ ๋์ปค ํ๋ธ์ ๋ช๋ น๊ณผ ๊ฑฐ์ ๋์ผํ๋ค.

### ์ฌ์ฉํ๋ ์ด์ 

- ์ด๋์ ์ด๋ฏธ์ง๋ฅผ ์ ์ฅํ ์ง ์๊ฒฉํ๊ฒ ๊ด๋ฆฌํ๋ค.
- ์ด๋ฏธ์ง ๋ฐฐํฌ ํ์ดํ๋ผ์ธ์ ์์ ํ ์์ ํ๋ค.
- ์ด๋ฏธ์ง ์ ์ฅ ๋ฐ ๋ฐฐํฌ๋ฅผ ์ฌ๋ด ๊ฐ๋ฐ ์ํฌํ๋ก์ฐ์ ๊ธด๋ฐํ๊ฒ ํตํฉํ  ์ ์๋ค.

### ์ฌ์ฉํ๊ธฐ ์  ์ค์ 

```kotlin
vi /etc/docker/daemon.json
```

๋ช๋ น์ด๋ฅผ ํตํด ์๋ ๋ช๋ น์ด๋ฅผ ์ฝ์ํ๋ค.

```kotlin
{
    "insecure-registries":[
        "localhost:5000"
    ]
}
```

ํด๋น ๋ณ๊ฒฝ์ ์ ์ฉํ๊ธฐ ์ํด์ docker Daemon ํ์ผ์ ๋ฆฌ๋ก๋ ํด์ผํ๋ค.

```kotlin
sudo systemctl daemon-reload
```

### ์ฌ์ฉ์ ์ํ ๋ช๋ น์ด

private resistry๋ฅผ ์คํ์ํต๋๋ค.

```kotlin
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

### Private Registry์ ์ด๋ฏธ์ง push ํ๊ธฐ

ํ์คํธ๋ฅผ ์ํด์ centos image๋ฅผ ๋ค์ด๋ก๋ํ๋ค.

```kotlin
docker pull centos
```

์ด๋ฏธ์ง์ ํ๊ทธ๋ฅผ ์ฌ์ฉํ๋ค.

```kotlin
docker tag centos:latest localhost:5000/baeldung-centos
```

์ด๋ฏธ์ง๋ฅผ push ํ๋ค.

```kotlin
docker push localhost:5000/baeldung-centos
```

๋ง์ง๋ง์ผ๋ก ๋์ปค ์ด๋ฏธ์ง๊ฐ ์ ๋ค์ด๋ก๋ ๋๋์ง ํ์ธํ๋ค.

```kotlin
docker pull localhost:5000/baeldung-centos
```

### Private ****Registry****๋ฅผ ์ํด์ ์ธ์ฆ ์ถ๊ฐํ๊ธฐ

๋ง์ฝ ์ธ๋ถ์ ํจ๋ถ๋ก ๋ธ์ถ ๋๋ฉด ์๋๋ ์ด๋ฏธ์ง ๊ฐ์ ๊ฒฝ์ฐ ์ธ๋ถ๋ก๋ถํฐ ๋ณดํธ๊ฐ ํ์ํฉ๋๋ค. ๊ทธ๋ด๊ฒฝ์ฐ registry์ ์ ๊ทผํ  ๋ ์ธ์ฆ์ด ํ์ํ๋ฉฐ htpasswd ์ธ์ฆ์ด ํ์ํฉ๋๋ค.

๋จผ์  Docker ๋ ์ง์คํธ๋ฆฌ ์๊ฒฉ ์ฆ๋ช์ ์ ์ฅํ  ๋ณ๋์ ๋๋ ํฐ๋ฆฌ๋ฅผ ์์ฑํฉ๋๋ค.

```kotlin
mkdir -p Docker_registry/auth
```

์๋ ๋ช๋ น์ด๋ฅผ ํตํด์ htpasswd ๋ฅผ ํตํด ์ธ์ฆ ๋ฐ์ ์ ์ ๋ฅผ ์์ฑํฉ๋๋ค.

ํด๋น ์ ์ ์ ๊ดํ ์์ธํ ์ ๋ณด๋ auth/htpasswd ํ์ผ์ ์ ์ฅ๋ฉ๋๋ค.

```kotlin
cd Docker_registry && docker run \
> --entrypoint htpasswd \
> httpd:2 -Bbn baeldung-user baeldung > auth/htpasswd
```

์ด์  ์คํํ ๋ ์ง์คํธ๋ฆฌ๋ฅผ ์ข๋ฃํฉ๋๋ค.

```kotlin
docker stop ๋ ์ง์คํธ๋ฆฌ ์ด๋ฏธ์ง & docker rm ๋ ์ง์คํธ๋ฆฌ ์ด๋ฏธ์ง
```

๋ณด์์ด ์ ์ฉ๋ ๋ ์ง์คํธ๋ฆฌ๋ฅผ ๋ค์ ์คํํฉ๋๋ค.

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

๋ก๊ทธ์ธ ๋ฐฉ๋ฒ

```kotlin
docker login  localhost:5000 -u baeldung-user -p baeldung
```

### ์ธ๋ถ์์ ๋ก๊ทธ์ธํ๊ธฐ ์ํ ์ค์ 

```kotlin
vi /etc/docker/daemon.json
```

```kotlin
{

    "insecure-registries": ["์๋ฒIP:5000"]

}
```

daemon.json ํ์ผ์ ์์ ํ์๋ค๋ฉด,  ๋์ปค๋ฅผ ์ฌ์์ ํ๋ค.

```kotlin
systemctl restart docker
```

๋ง์ฝ systemctl์ด ์๋๋ฉด ์๋ ๋ช๋ น์ด๋ฅผ ์ฌ์ฉํด๋ณด์

```kotlin
service docker restart
```

์ฐธ์กฐ

[https://docs.docker.com/registry/](https://docs.docker.com/registry/)

[https://www.baeldung.com/ops/docker-private-registry](https://www.baeldung.com/ops/docker-private-registry)
