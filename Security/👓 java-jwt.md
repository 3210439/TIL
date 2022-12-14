# ๐ย java-jwt

์ด๋ฒ์๋ ์ค๋ฌด์์ ์ฌ์ฉํ๋ java-jwt์ ๋ํด ์ ๋ฆฌํด๋ณด์๋ค. ํด๋น ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ github์์ ํ์ธํด ๋ณผ ์ ์๋ค.  

### Libraray import ๋ฐฉ๋ฒ by gradle

```bash
implementation 'com.auth0:java-jwt:4.0.0'
```

### JWT Token ์์ฑ ๋ฐฉ๋ฒ

jwt token์ ๊ฒ์ฆํ๊ณ  ์๋ชํ๊ธฐ ์ํด์ HMAC256 ์๊ณ ๋ฆฌ์ฆ์ ํ๋ค. HMAC256 ์๊ณ ๋ฆฌ์ฆ์ ์ฌ์ฉํ๊ธฐ ์ํด์๋ ์ํฌ๋ฆฟ ์ด๋ผ๋ ๋ฐ์ดํธ ๋ฐฐ์ด ํ์์ ํ๋ผ๋ฏธํฐ๊ฐ ํ์ํ๋ค. ํด๋น ๊ฐ์ ์ธ๋ถ๋ก ๋ธ์ถ ๋์ง ์๋๋ก ์กฐ์ฌํด์ผ ํ๋ค.

์์ฑ ๋ฐฉ๋ฒ by kotlin

```bash
val algorithm = Algorithm.HMAC256(secret.toByteArray())
```

๊ทธ ๋ค์ ํ ํฐ์ ์ข๋ฅ๋ฅผ ์ ํํ๋ค. refresh ํ ํฐ์ธ์ง ์๋๋ฉด access ํ ํฐ์ธ์ง๋ฅผ ์์์ผ ํ๋ค.

ํ ํฐ์ด access ํ ํฐ์ด๋ฉด 6์๊ฐ refresh ํ ํฐ์ด๋ฉด 12์๊ฐ์ ํ ๋นํ๋ค.

```
val tokenExpiredTime = when (jwtType) {
    JwtType.ACCESS_TOKEN-> jwtExpirationTime.getTime(6)
    JwtType.REFRESH_TOKEN-> jwtExpirationTime.getTime(12)
}
```

์ด์  ํ ํฐ์ ์์ฑํ๋ค. ํ ํฐ์ subject ์ด๋ฆ์ refresh, access ์ค ์ ํํ ์ด๋ฆ์ด ๋ค์ด๊ฐ๊ณ  ์ด ๊ฐ์ ํตํด์ ํ ํฐ์ด access ์ธ์ง refresh์ธ์ง ๊ตฌ๋ถํ๋ค.

issuer์ ์์ฑ์ ์ด๋ฆ ๊ทธ๋ฆฌ๊ณ  expiresAt ๊ฐ์ ๋ง๋ฃ ์๊ฐ ๊ทธ๋ฆฌ๊ณ  Claim์ ๊ฒ์ฆํ  ๋ ์ฌ์ฉํ  ๊ฐ์ ๋ฃ์ด์ฃผ๋ฉด ๋๋๋ฐ email์ ๋ฃ์๋ค. ๊ทธ๋ฆฌ๊ณ  ๋ง์ง๋ง์ sign ๋ถ๋ถ์ ์ฌ์ฉํ ์๊ณ ๋ฆฌ์ฆ์ ๋ฃ๋๋ค.

```
return JWT.create()
    .withSubject(jwtType.name)
    .withIssuer(issuer)
    .withExpiresAt(Date(System.currentTimeMillis() + tokenExpiredTime))
    .withClaim("email", email)
    .sign(algorithm)
```

### ํ ํฐ ๊ฒ์ฆ ๋ฐฉ๋ฒ

ํ ํฐ์ ๊ฒ์ฆํ๊ธฐ ์ํด์๋ JWTVerifier๋ฅผ ์ฌ์ฉํด์ผ ๋๋ค. ์ฌ์ฉํ๊ธฐ์ํด JWT.required()๋ฅผ ํตํด

Verifier๋ฅผ ์์ฑํ  ์ ์๋ค. Verifier๋ฅผ ์์ฑํ๊ธฐ ์ํด์๋ ์ด๋ค ์๊ณ ๋ฆฌ์ฆ์ผ๋ก verify ํ  ๊ฒ์ธ์ง ์ธ์๊ฐ์ ๋๊ฒจ์ฃผ๊ณ  Verifier์ verify๋ฅผ ํตํด์ ํ ํฐ ๊ฐ์ ๊ฒ์ฆํ๋ฉด ๋๋ค.

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
        log.error{"์๋ชป๋ ์๋ช์๋๋ค. [token: $token]"}
throw AlgorithmMismatchException("์๋ชป๋ ์๋ช์๋๋ค.")
    } catch (e: JWTDecodeException) {
        log.error{"ํ ํฐ ํ์์ด ์๋ชป๋์์ต๋๋ค. [token: $token]"}
throw JWTDecodeException("ํ ํฐ ํ์์ด ์๋ชป๋์์ต๋๋ค.")
    } catch (e: TokenExpiredException) {
        log.error{"ํ ํฐ ์ ํจ๊ธฐ๊ฐ ๋ง๋ฃ [token: $token]"}
throw TokenExpiredException("ํ ํฐ ์ ํจ๊ฐ๊ฐ์ด ๋ง๋ฃ๋์์ต๋๋ค.")
    }
}
```

### ๊ฒ์ฆ๋ ํ ํฐ์ ๊ฐ ๊ฐ์ ธ์ค๊ธฐ

ํ ํฐ์ด ๊ฒ์ฆ ๋์๋ค๋ฉด ํ ํฐ์ ํฌํจ๋ ๊ฐ์ ์ฌ์ฉํ  ์ ์๋ค. ํ ํฐ์ ๊ฒ์ฆํ๋ฉด

DecodedJWT ํ์์ ๊ฐ์ ๋ฐ๋๋ฐ, ์ด ๊ฐ์์ ํ ํฐ์ ๋ฃ์ด ๋์๋ ๊ฐ์ ์ฌ์ฉํ  ์ ์๋ค.

์ฌ๊ธฐ์ ํ ํฐ์ ๋ฃ์ด๋ ๊ฐ์ claim์ด๋ผ๊ณ  ํ๋ค. claim์ ์ด๋ฆ๊ณผ ๊ฐ์ ์์ผ๋ก ์ด๋ฃจ์ด์ ธ ์๋ค.

claim์ ์ด๋ฉ์ผ์ ๋ฃ์ด์ ๊ธฐ์กด ์ ์ ์ธ์ง ์๋์ง ํ์ธํ  ์ ์๋ค.

```
fun getClaim(token: String, claim: String): String {
    val result = validationToken(token)
        .getClaim(claim)
        .asString()

return result
}
```

์ฐธ์กฐ: [https://github.com/auth0/java-jwt](https://github.com/auth0/java-jwt)