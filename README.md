# React 19

## 선행 학습
> 1. Javascript (ES6+)
> 2. Static Web Service (HTML, CSS)
> 3. Asynchronous (AJAX, Fetch API, AXIOS)

## Install

+ CRA (`npx create-react-app my-app`)

+ VITE (`npm create vite@latest my-app`)
  + `18 > 19` update
  ```cmd
  npm install react@19 react-dom@19
  ```

+ NEXT (`npx create-next-app@latest`)
  + 추가 설치
  ```cmd
  npm install next@latest react@latest react-dom@latest
  ```

## Bootstrap Install
```cmd
npm install bootstrap bootstrap-icons
```

+ `jsx` 추가 하기
```js
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';
```

## URI 만들기
```cmd
npm install react-router-dom
```

## 비동기 통기 `AXIOS`
```cmd
npm install axios
```

> POST 요청
```js
axios.post('/')
  .then((response) => console.log(response))
  .catch((error) => console.log(error));
```

## 소스 경로 별명 적용하기
> `vite.config.js` 설정 추가
```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { fileURLToPath, URL } from 'url'; // 추가

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
        '@': fileURLToPath(new URL('./src', import.meta.url)), // 추가
    },
},
});
```

## URL 기본(루트) 경로 적용하기
> `vite.config.js` 설정 추가
```js
export default defineConfig({
  plugins: [react()],
  base: "/", // 추가
});
```

## Host(호스트), IP 접속 허용 적용하기
> `vite.config.js` 설정 추가
```js
export default defineConfig({
  plugins: [react()],
  server: {
    host: true, // 추가
  }, 
});
```

## Generating Asymmetric Keys with OpenSSL
1. [windows openssl 설치]("https://slproweb.com/products/Win32OpenSSL.html")
2. openssl 버젼 확인
```cmd
openssl -v
```
3. Generate a KeyPair
```cmd
openssl genrsa -out keypair.pem 2048
```
4. Generate a Public Key
```cmd
 openssl rsa -in keypair.pem -pubout -out publicKey.pem 
```
5. Generate a Private Key
```cmd
openssl pkcs8 -topk8 -inform PEM -outform PEM -nocrypt -in keypair.pem -out privateKey.pem
```
6. Spring boot : `properties` 생성
```
@ConfigurationProperties(prefix = "jwt")
 public record RSAKeyRecord (
  RSAPublicKey rsaPublicKey, RSAPrivateKey rsaPrivateKey
 ) {}
```
7. 등록한 Properties 활성화 적용하기
```
@EnableConfigurationProperties(RSAKeyRecord.class)
@SpringBootApplication
public class SpringSecurityApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringSecurityApplication.class, args);
    }

}
```
8. Location of file in properties
```yml
jwt:
  rsa-private-key: classpath:certs/privateKey.pem
  rsa-public-key: classpath:certs/publicKey.pem
```
9. Jwt 설정 적용하기
```java
@Configuration
@RequiredArgsConstructor
public class JwtConfig {

  private final RsaKeyProperties rsaKeys;

  @Bean
  public JwtEncoder jwtEncoder() {
    JWK jwk = new RSAKey.Builder(rsaKeys.publicKey()).privateKey(rsaKeys.privateKey()).build();
    JWKSource<SecurityContext> jwks = new ImmutableJWKSet<>(new JWKSet(jwk));
    return new NimbusJwtEncoder(jwks);
  }

  @Bean
  public JWKSet jwkSet() {
    RSAKey.Builder builder = new RSAKey.Builder(rsaKeys.publicKey())
        .keyUse(KeyUse.SIGNATURE)
        .algorithm(JWSAlgorithm.RS256)
        .keyID("public-key-id");
    return new JWKSet(builder.build());
  }

  @Bean
  public JwtDecoder jwtDecoder() {
      JWKSource<SecurityContext> jwkSource = new ImmutableJWKSet<>(jwkSet());
      return new NimbusJwtDecoder(jwkSource);
  }

}
```
10. JwkDecoder 를 위한 jwkSet 요청 URI 생성
```java
@RestController
@RequiredArgsConstructor
public class JwkSetController {

  private final JWKSet jwkSet;

  @GetMapping("/.well-known/jwks.json")
  public Map<String, Object> keys() {
    return jwkSet.toJSONObject();
  }

}
```
11. jwkSet 요청 URI를 이용하여 JwkDecoder 하는 Bean 생성
```java
public class SecurityConfig {

  private String jwkSetURI = "/.well-known/jwks.json";

  @Bean
  public JwtDecoder jwtDecoder() {
    return NimbusJwtDecoder.withJwkSetUri(jwkSetURI).build();
  }

}
```
