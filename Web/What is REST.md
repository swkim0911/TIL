# What is REST
## REST 란?
REST는 프로토콜이나 표준이 아닌 하나의 아키텍처 스타일이다. 
> 아키텍처 스타일이란 "시스템을 구성하는 컴포넌트들의 상호작용하는 방식에 대한 제약조건들의 집합"이다. 

REST는 제약조건들을 분산 하이퍼미디어 시스템(Distributed Hypermedia System), 즉 웹(WWW) 환경에 맞게 정리한 것이다. 따라서 REST는 웹의 구조와 철학을 이론적으로 체계화한 아키텍처 스타일이라고 할 수 있다.

> REST 아키텍처 스타일을 준수하는 웹 API를 REST API(혹은 RESTful API)라고 한다.

## REST의 6가지 기본 제약조건(원칙)
REST는 설계에서 확장성, 무상태성, 단순성(simplicity)을 촉진하는 6가지 제약조건이 있다.

### Uniform Interface(일관된 인터페이스)
REST는 클라이언트와 서버 간의 상호 작용을 위한 일관된 인터페이스를 정의한다. 예를 들어 HTTP 기반 REST API는 표준 HTTP 메서드(GET, POST, PUT, DELETE 등)와 URI(Uniform Resource Identifiers)를 사용하여 **리소스를 식별**한다.

다음 네 가지 제약조건을 통해 REST의 일관된 인터페이스를 구현할 수 있다.

1. 리소스 식별: 인터페이스는 클라이언트와 서버 간 상호작용에 관하여 각 리소스를 고유하게 식별할 수 있어야 한다.

2. Manipulation of Resources through Representations
  
3. Self-descriptive Messages

4. HATEOAS, Hypermedia as the Engine of Application State

### Client-Server
클라이언트-서버 구조는 관심사 분리를 강화하여, 클라이언트와 서버 구성 요소가 독립적으로 변경·확장하는데 도움이 된다.

### Stateless
무상태성은 클라이언트가 서버에 보내는 각 요청이, 해당 요청을 이해하고 처리하는데 필요한 모든 정보를 포함해야 함을 요구한다.

### Cacheable
Cacheable(캐시 가능성) 원칙은 응답 자신이 캐시 가능한지 여부를 명시적 또는 암시적으로 표시해야 한다. 응답이 캐시 가능한 경우, 클라이언트는 동일한 요청에 대해 일정 기간 동안 해당 응답 데이터를 재사용할 수 있는 권한을 갖게 된다.

### Layered System
Layered System(계층형 시스템)은 컴포넌트의 동작을 제약함으로써, 아키텍처를 계층적인 구조로 구성할 수 있다. 각 컴포넌트들은 자신이 직접 상호작용하는 인접한 계층 이외는 볼 수 없다.

일반적인 예로 MVC 패턴이 있는데, MVC 패턴은 관심사의 명확한 분리를 가능하게 해서, 애플리케이션을 더 쉽게 개발 및 유지보수하며, 확장할 수 있게 만든다.
### Code on Demand(Optional)
서버가 클라이언트에게 실행 가능한 코드를 전송하여, 클라이언트의 기능을 확장할 수 있게 하는 선택적 제약조건으로 서버가 데이터뿐 아니라 코드(로직)도 보낼 수 있다는 개념이다.

## 리소스(Resource)란?
**REST에서 기본적인 데이터 표현 단위를 리소스(resource)라고 한다.** 일관되고 견고한 리소스 명명 규칙을 갖추는 것은 장기적으로 뛰어난 설계 결정이 될 것이다.

이름을 붙일 수 있는 모든 정보는 리소스가 될 수 있다. 예를 들어 문서, 이미지, 다른 리소스들의 모음(collection), 일시적인 서비스, 혹은 사람과 같은 비가상(non-virtual) 객체일 수도 있다.

### 리소스 종류

#### 단일(Singleton) 리소스와 컬렉션(Collection) 리소스
리소스는 단일 리소스(Singleton Resource) 또는 컬렉션 리소스(Collection Resource) 가 될 수 있다. 예를 들어, “customers”는 컬렉션 리소스이며, “customer”는 단일 리소스이다. "customers” 컬렉션 리소스는 `/customers` URI로 식별할 수 있다. “customer” 단일 리소스는 `/customers/{customerId}` URI로 식별할 수 있다.

#### 컬렉션과 하위 컬렉션(Sub-collection) 리소스
리소스는 하위 컬렉션(Sub-collection) 리소스를 포함할 수도 있다. 예를 들어, 특정 “customer”의 하위 컬렉션 리소스인 “accounts” 는 `/customers/{customerId}/accounts` URI로 식별할 수 있다

### URI 설계
REST API는 URI(Uniform Resource Identifier)를 사용하여 리소스를 지정한다. REST API 설계자는 API의 클라이언트가 리소스 모델(리소스 집합)을 직관적으로 이해할 수 있도록 URI를 설계해야 한다.

## URI Best Practice of REST API

### 1. 명사를 사용하여 리소스를 표현해야 한다.
RESTful API는 동작이 아닌 사물(thing), 즉 명사(noun)인 리소스를 가리켜야 한다.

리소스는 document, collection, store, 총 세 가지 카테고리로 나눌 수 있다. 리소스를 설계할 때는 반드시 이 중 하나의 범주에 속하도록 하고, 그에 맞는 naming convention을 일관성 있게 사용하는 것이 좋다.

#### 1.1 document(도큐먼트)
도큐먼트 리소스는 객체 인스턴스나 데이터베이스 리코드와 유사한 단일 개념이다. REST에서는 이것을 리소스 컬렉션 내의 단일 리소스로 볼 수 있다. 도큐먼트 리소스를 나타낼 때는 **단수형 이름**을 사용한다.

#### 1.2 collection(컬렉션)
**컬렉션 리소스는 서버가 관리하는 리소스들의 디렉터리이다**. 관리 주체가 서버가 되어서 리소스 생성, 삭제, URI 부여를 서버가 직접 결정한다. 클라이언트 입장에서는 "새 리소스를 추가해주세요"라고 요청만 제안하는 셈이다.

컬렉션 리소스를 나타낼 때는 **복수형 이름**을 사용한다.

#### 1.3 store(스토어)
스토어는 클라이언트가 관리하는 리소스 저장소이다. 관리 주체가 클라이언트가 되어서 리소스 생성, 삭제, URI 부여를 클라이언트가 직접 결정한다. 

스토어 리소스를 나타낼 때는 **복수형 이름**을 사용한다.

### 2. 일관성이 핵심이다.
리소스 이름과 URI 형식을 일관되게 사용하여 모호함을 줄이고, 가독성와 유지보수성을 극대화한다. 일관성을 위해 다음과 같은 설계 지침을 적용할 수 있다.

- 슬래시 `/`는 계층적 관계를 나타내는 데 사용한다.

- URI 끝에 슬래시 `/`를 붙이지 않는다.

- 가독성을 위해 하이픈 `-`을 사용한다.

- 언더스코어 `_`는 사용하지 않는다.

- 소문자를 사용한다.
  
### 3. 파일 확장자를 사용하지 않는다.
만약 API의 미디어 타입(media type)을 파일 확장자를 통해 나타내고 싶다면, `Content-Type` 헤더에 명시된 미디어 타입을 기준으로 본문(body)의 콘텐츠를 처리하는 것이 올바른 방법이다.

### 4. URI에 CRUD 함수 이름을 사용하지 않는다.
URI는 오직 리소스를 식별하기 위한 용도로만 사용되어야 하며, 동작을 나타내는 데 사용되어서는 안된다.
어떤 CRUD 기능이 수행되는지는 HTTP 요청 메서드를 통해 표현해야 한다.

### 5. URI 컬렉션을 필터링하려면 쿼리 파라미터를 사용하라
정렬, 필터링, 제한(limiting)이 필요하면 새로운 API를 만들지 말고, 기존의 리소스 컬렉션 API에 쿼리 파라미터(query parameter)로 전달하면 된다.







### 출처
- https://restfulapi.net/

- https://johngrib.github.io/wiki/clipping/roy-fielding/rest-paper/#%EB%85%BC%EB%AC%B8-%EC%9A%94%EC%95%BD