# 인터페이스 
인터페이스는 클래스가 구현해야 하는 동작을 선언(declare)하는데 사용되는 추상 타입이다. 즉, 클래스가 반드시 구현해야 하는 행동(메서드)의 규약(contract)을 정의하는 것이다.

Java의 핵심 개념 중 하나이며 추상화, 다형성 및 다중 상속을 달성하는데 사용된다.
## 인터페이스 구성
- constant variable: 선언된 모든 변수는 상수(`public static final`)이다.

- abstract 메서드: `abstract` 키워드가 없어도 모두 추상 메서드이다.
  
- default 메서드: Java 8부터 인터페이스에 `default` 메서드를 정의할 수 있게 되었다. 기본 구현이 제공되는 `default` 메서드는 구현 클래스에서 구현하지 않아도 컴파일이 가능하다.
  
- static 메서드: Java 8부터 인터페이스에 `static` 메서드를 정의할 수 있게 되었다. 인스턴스 생성과 상관없이 인터페이스 타입으로 호출하는 메서드이다.
    - Java 8 이전에는 인터페이스와 관련된 정적 메서드들을 동반 클래스(companion class)에서 제공했다. 대표적인 예로 `Collection` 인터페이스와 `Collections` 동반 클래스가 있다.
  
    - 이제는 인터페이스에 바로 정적 메서드를 추가할 수 있기 때문에 동반 클래스를 따로 정의하지 않아도 된다. Java 8에 추가된 `Stream` 인터페이스는 유용한 정적 메서드들을 제공한다.

- private 메서드: 인터페이스 내부에서만 사용되는 헬퍼 메서드(도우미 메서드)를 정의할 수 있게 해준다. 이러한 기능은 자바 9부터 도입되었다. `private` 메서드는 인터페이스의 디폴트 메서드나 정적 메서드에서 공통적으로 사용되는 코드를 캡슐화하고 재사용할 수 있도록 도와준다.
   
# 추상 클래스
클래스의 일부 또는 전체 메서드가 구현되지 않은 상태로 정의되는 클래스이다. 인스턴스화할 수 없으며, 다른 클래스가 이를 상속받아 구체적인 구현을 제공하도록 하는데 사용된다.
# 인터페이스 vs 추상 클래스
|  구분  |                               인터페이스                               |                 추상 클래스                  |
| :----: | :--------------------------------------------------------------------: | :------------------------------------------: |
|  변수  |               `public static final`(상수)만 가질 수 있다               | 제한 없음 (인스턴스 변수, static 변수, 상수) |
| 생성자 |                                   X                                    |                      O                       |
| 메서드 | `abstract` 메서드, `default` 메서드, `static` 메서드, `private` 메서드 |                  제한 없음                   |
|  상속  |                             다중 상속 가능                             |               단일 상속만 가능               |


### 참고
- [Java Interface](https://www.baeldung.com/java-interfaces)
- [[Java] 인터페이스 - 인터페이스의 요소들](https://velog.io/@foeverna/Java-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%9D%98-%EC%9A%94%EC%86%8C%EB%93%A4)
- [Interface (Java)](https://en.wikipedia.org/wiki/Interface_(Java))
- [Java 8 - Interface의 변화](http://happinessoncode.com/2017/04/19/java8-changes-in-interface/)
- [인터페이스 vs 추상클래스 용도 차이점 - 완벽 이해](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
