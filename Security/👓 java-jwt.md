# 👓 java-jwt

이번에는 실무에서 사용하는 java-jwt에 대해 정리해보았다. 해당 라이브러리는 github에서 확인해 볼 수 있다.  

### Libraray import 방법 by gradle

```bash
implementation 'com.auth0:java-jwt:4.0.0'
```

### JWT Token 생성 방법

jwt token을 검증하고 서명하기 위해서 HMAC256 알고리즘을 한다. HMAC256 알고리즘을 사용하기 위해서는 시크릿 이라는 바이트 배열 타입의 파라미터가 필요하다. 해당 값은 외부로 노출 되지 않도록 조심해야 한다.

생성 방법 by kotlin

```bash
val algorithm = Algorithm.HMAC256(secret.toByteArray())
```

그 다음 토큰의 종류를 선택한다. refresh 토큰인지 아니면 access 토큰인지를 알아야 한다.

토큰이 access 토큰이면 6시간 refresh 토큰이면 12시간을 할당한다.

```
val tokenExpiredTime = when (jwtType) {
    JwtType.ACCESS_TOKEN-> jwtExpirationTime.getTime(6)
    JwtType.REFRESH_TOKEN-> jwtExpirationTime.getTime(12)
}
```

이제 토큰을 생성한다. 토큰의 subject 이름은 refresh, access 중 선택한 이름이 들어가고 이 값을 통해서 토큰이 access 인지 refresh인지 구분한다.

issuer에 생성자 이름 그리고 expiresAt 값에 만료 시간 그리고 Claim에 검증할 때 사용할 값을 넣어주면 되는데 email을 넣었다. 그리고 마지막에 sign 부분에 사용한 알고리즘을 넣는다.

```
return JWT.create()
    .withSubject(jwtType.name)
    .withIssuer(issuer)
    .withExpiresAt(Date(System.currentTimeMillis() + tokenExpiredTime))
    .withClaim("email", email)
    .sign(algorithm)
```

### 토큰 검증 방법

토큰을 검증하기 위해서는 JWTVerifier를 사용해야 된다. 사용하기위해 JWT.required()를 통해

Verifier를 생성할 수 있다. Verifier를 생성하기 위해서는 어떤 알고리즘으로 verify 할 것인지 인자값을 넘겨주고 Verifier의 verify를 통해서 토큰 값을 검증하면 된다.

```
private fun validationToken(
    token: String
): DecodedJWT {
    val algorithm = Algorithm.HMAC256(secret.toByteArray())

    try {
        return JWT
            .require(algorithm)
						.withIssuer(issuer)
            .build()
            .verify(token)
    } catch (e: AlgorithmMismatchException) {
        log.error{"잘못된 서명입니다. [token: $token]"}
throw AlgorithmMismatchException("잘못된 서명입니다.")
    } catch (e: JWTDecodeException) {
        log.error{"토큰 형식이 잘못되었습니다. [token: $token]"}
throw JWTDecodeException("토큰 형식이 잘못되었습니다.")
    } catch (e: TokenExpiredException) {
        log.error{"토큰 유효기간 만료 [token: $token]"}
throw TokenExpiredException("토큰 유효가간이 만료되었습니다.")
    }
}
```

### 검증된 토큰의 값 가져오기

토큰이 검증 되었다면 토큰에 포함된 값을 사용할 수 있다. 토큰을 검증하면

DecodedJWT 타입의 값을 받는데, 이 값에서 토큰에 넣어 두었던 값을 사용할 수 있다.

여기서 토큰에 넣어둔 값을 claim이라고 한다. claim은 이름과 값의 쌍으로 이루어져 있다.

claim에 이메일을 넣어서 기존 유저인지 아닌지 확인할 수 있다.

```
fun getClaim(token: String, claim: String): String {
    val result = validationToken(token)
        .getClaim(claim)
        .asString()

return result
}
```

참조: [https://github.com/auth0/java-jwt](https://github.com/auth0/java-jwt)