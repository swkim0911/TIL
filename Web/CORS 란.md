# CORS (Cross-Origin Resource Sharing)란 무엇일까

## CORS란
브라우저에서는 CSRF(Cross-Site Request Forgery) 공격과 같은 보안적인 이유로, 기본적으로 다른 출처(cross-origin)로의 요청이나 응답 접근을 제한하는 **동일 출처 정책(Same-Origin Policy)을 적용**한다.

이 정책은 악성 사이트가 사용자의 인증 정보(쿠키 등)를 이용해 다른 사이트로 임의의 요청을 보내거나 민감한 데이터를 읽어가는 것을 방지하기 위한 것이다.

하지만 실제 웹 환경에서는 다른 출처에 있는 리소스를 사용하는 것은 흔한 일이기 때문에 무작정 제한만을 할 수는 없다. 그래서 등장한 것이 **다른 출처(cross-origin)에서도 리소스를 공유할 수 있도록 세운 정책이 CORS이다.**

만약 웹 브라우저에서 CORS 에러를 마주한 것은, 브라우저의 기본 보안 정책인 SOP(Same-Origin Policy)가 적용되는데, CORS 정책을 따로 설정하지 않아서 리소스가 제한된 것을 의미한다.

정리하면 **CORS는 브라우저가 기본적으로 적용하는 동일 출처 정책(Same-Origin Policy) 때문에 막히는 교차 출처(cross-origin) 요청을 서버가 허용할 수 있도록 정해놓는 HTTP 헤더 기반 메커니즘이다.**

### cross origin
origin은 `scheme(프로토콜) + domain(도메인) + port(포트 번호)`을 말한다. 그래서 cross origin은 다음 중 한 가지라도 다른 경우를 말한다.

1. 프로토콜 - http와 https는 다른 프로토콜이다.
  
2. 도메인 - domain.com과 api.domain.com은 다른 도메인이다.

3. 포트 번호 - 80, 8080 포트는 다르다.

## CORS 기본 동작
1. 브라우저에서 `fetch()`나 `XMLHttpRequest`와 같이 HTTP 요청을 보낼 때 브라우저는 요청에 `origin`헤더를 추가하여 자신의 origin을 담아 보낸다.
  
2. 서버는 응답 헤더에 `Access-Control-Allow-Origin`에 서버 자신의 리소스에 접근할 수 있는 origin을 담아 보낸다.
  
3. 브라우저는 cross-origin 응답에 대해 요청 헤더의 `origin`이 응답 헤더의 `Access-Control-Allow-Origin`에 적합한지 판단한다.
   1. 적합하지 않은 경우: 응답이 200 OK 이더라도 버리고 에러를 출력한다.
   
   2. 적합한 경우: 문제없이 가져온다.


## CORS 동작 시나리오
### Simple requests인 경우
1. 브라우저는 `origin` 헤더에 자신의 출처를 포함해서 서버에 요청한다.
2. 서버의 응답이 클라이언트에 도착했을 때, 브라우저가 보낸 `origin` 헤더와 응답 헤더 `Access-Control-Allow-Origin`의 값을 비교해서 유효한 요청이라면 응답을 정상적으로 받는다. 유효하지 않다면 브라우저는 응답을 버리고 에러를 발생시킨다.

#### Simple request란?
HTTP 메서드가 다음 중 하나이면서
- GET
- HEAD
- POST
  
기본으로 설정되는 헤더는 제외하고, 수동으로 설정할 수 있는 유일한 헤더는 아래와 같이 [CORS 안전 목록](https://developer.mozilla.org/en-US/docs/Glossary/CORS-safelisted_request_header)에 있는 요청 헤더이다.
- Accept
- Accept-Language
- Content-Language
- Content-Type

`Content-Type` 헤더에 지정된 유형은 아래와 같다.
- application/x-www-form-urlencoded
- multipart/form-data
- text/plain

위 조건을 만족하면 Simple requests라고 부른다. 이 요청은 추가적으로 확인(preflight)하지 않고 바로 본 요청을 보낸다.

### Preflighted requests인 경우
브라우저에서 Simple requests 조건을 만족하지 않는 요청을 보내는 경우,
1. 먼저 본(원래) 요청을 보내기 전에 헤더에 `origins` 뿐만 아니라 `Access-Control-Request-Method` 헤더에 본 요청 HTTP 메서드와 `Access-Control-Request-Headers` 헤더에 본 요청시 사용된 헤더를 포함해서 `OPTIONS` 메서드로 서버로 요청을 보낸다.
  
2. 서버에서는 서버에 설정된 CORS 설정을 바탕으로 `Access-Control-Request-Method` 헤더와 `Access-Control-Request-Headers` 헤더를 포함해서 보낸다. 브라우저는 이 응답 헤더를 보고 유효한 요청인지 확인한다. 유효하지 않다면 요청은 중단되고 에러가 발생하며, 만약 유효하다면 본 요청을 서버에게 보내서 리소스를 응답받는다.

#### Preflighted requests란?
Simeple requests와 달리 "preflighted" requests인 경우 브라우저는 실제 요청을 전송하기에 요청이 서버에게 안전한지 확인하기 위해 먼저 `OPTIONS` 메서드를 사용해서 cross-origin의 리소스에 HTTP 요청을 보낸다. 즉, preflighted 요청은 "서버가 교차 출처 요청을 처리할 준비가 돼 있는지" 먼저 붇는 방식으로, 서버가 **명시적으로** 허용했다면 실제 요청을 보내라고 브라우저가 결정하게 해주는 장치이다.

Spring의 CORS 설정 코드를 예로 들자면,

`allowedHeaders("Authorization", "Content-Type")` 이렇게 되어 있을 때, "Authorization"은 브라우저의 custom 헤더이며 이 헤더 요청을 서버에서는 명시적으로 허용하게 설정하는 것이며, "Content-Type"은 CORS에서 공식적으로 허용한 type 외에 `application/json` 과 같은 type을 명시적으로 허용하게 설정한 것이다.

### 출처
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS
- https://hannut91.github.io/blogs/infra/cors
- https://teddy0.tistory.com/7
