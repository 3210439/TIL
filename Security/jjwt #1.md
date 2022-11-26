# jjwt #1

JJWT는 JVM과 Android 위에서 JSON Web Tokens (JWTs)를 식별하고 생성하기 위한 라이브러리를 이해하고 사용하기 가장 간편하도록 하는데 초점을 맞췄습니다.

JJWT는 순수 Java 구현체로 생성되었으며 Apache 2.0 License를 따르는 오픈 소스입니다.

### Json Web Token이란?

JWT는 간결하고 검증 가능한 형태로 두 당사자간에 정보를 전송하는 수단입니다.

JWT의 body에 인코딩된 정보들을 claim 이라고 합니다. JWT의 확장된 형태는 JSON 형식이므로 각각의 클레임은 JSON 객체의 키입니다.

JWT는 서명 또는 암호화 될 수 있습니다. 이는 JWT 사용자에게 강력한 검증 가능을 주게됩니다. 수신자는 서명을 확인하여 JWT가 조작되지 않았다는 것을 확인하기 때문에 높은 신뢰도를 가지고 있습니다.

서명된 JWT의 간별한 표현은 각각 .로 구분되는 세 부분으로 구성된 문자열입니다.

각 파트는 Base64 URL로 인코딩 되어있습니다.

1. 첫 번째 파트는 헤더입니다. JWT에 사용된 알고리즘을 식별합니다.
2. 두 번째 파트는 바디입니다. JWT에서 인코딩된 클레임이 들어있습니다.
3. 마지막 파트는 서명입니다. 헤더에 지정된 알고리즘을 통해 헤더와 본문의 조합을 전달받아 만들어집니다.

### Gradle 디펜던시 설정(공식 문서엔 구버전으로 되어있음)

```
implementation 'io.jsonwebtoken:jjwt-api:0.11.5'
runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.11.5'
runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.11.5'
```

### 간단한 시작

```
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.security.Keys;
import java.security.Key;

// We need a signing key, so we'll create one just for this example. Usually
// the key would be read from your application configuration instead.
Key key = Keys.secretKeyFor(SignatureAlgorithm.HS256);

String jws = Jwts.builder().setSubject("Joe").signWith(key).compact();
```

위의 코드를 설명하면 아래와 같다.

1. JWT를 구축하여 등록된 클레임 sub의 값이 jeo로 설정된다.
2. HMAC-SHA-256 알고리즘이 적용된 키를 통해 JWT를 서명한다.
3. 마지막으로 String 형태로 압축한다. 서명된 JWT를 JWS라고 부른다.

jws의 형태는 아래와 같다.

```jsx
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJKb2UifQ.1KP0SsvENi7Uz1oQc07aXTL7kpQG5jBNIybqr60AlD4
```

jwt를 식별하는 방법

```jsx
assert Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(jws).getBody().getSubject().equals("Joe");
```

여기서 parseClaimsJws 메소드를 잘 사용하고 있는지 주의해야된다. 만약에 잘못된 메소드를 사용할 경우 UnsupportedJwtException이 발생하게 된다.

jwt를 식별하면서 2가지 일이 발생한다.

- key는 JWT의 서명을 식별하는데 사용된다. 식별하는데 실패할 경우 SignatureException이 발생하게 된다.
- JWT가 식별되면 claim을 파싱하여 sub에 들어있는 value값이 joe인지 구별한다.

JWT 식별 실패를 대비해서 try catch 문을 사용한다.

```
try {

    Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(compactJws);

    //OK, we can trust this JWT

} catch (JwtException e) {

    //don't trust the JWT!
}
```

SignatureException의 부모는 JwtException이므로 위 catch문에 걸리게 된다.

### Signed JWTs

서명된 JWT는 2가지 기능을 제공한다.

- JWT가 누구에의해 생성 되었는지 보증한다. - 진실성
- JWT가 조작되거나 변경되었는지 보증한다. - 무결성

JWT 가 생성되는 방식

1. header 와 body가 아래와 같이 준비되어있다.

header

```jsx
{
	"alg": "HS256"
}
```

body

```jsx
{
	"sub": "Joe"
}
```

1. 위 내용을 정리하면 아래와 같다.

```jsx
String header = '{"alg":"HS256"}'
String claims = '{"sub":"Joe"}'
```

1. 위 내용을 UTF-8 byte로 변경한다음 Base64-encode로 인코딩한다.

```jsx
String encodedHeader = base64URLEncode( header.getBytes("UTF-8") )
String encodedClaims = base64URLEncode( claims.getBytes("UTF-8") )
```

1. 인코딩된 헤더와 claim들을 . 을 기준으로 결합시킨다.

```jsx
String concatenated = encodedHeader + '.' + encodedClaims
```

인코딩된 헤더와 클레임이 결합된 문자열을 HMAC-SHA-256 알고리즘을 사용해서 암호화한다.

```jsx
Key key = getMySecretKey()
byte[] signature = hmacSha256( concatenated, key )
```

암호화된 문자열을 signature로 부른다. 이를 아까 인코딩된 header와 claim이 결합된 문자열과 합처서 jws를 만든다.

```jsx
`String jws = concatenated + '.' + base64URLEncode( signature )`
```

jws문자열의 내용은 아래와 같다.

```jsx
`eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJKb2UifQ.1KP0SsvENi7Uz1oQc07aXTL7kpQG5jBNIybqr60`
```

JJWT에서는 위와같은 과정을 알아서 자동으로 처리해준다. 위와같은 작업을 수동으로 할 경우 코드를 잘못 만들 수 있고 보안에 취약해질 수 있다.

공식 문서 참조

[https://github.com/jwtk/jjwt#jws-key](https://github.com/jwtk/jjwt#jws-key)