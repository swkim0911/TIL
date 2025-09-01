## Record 클래스

레코드 클래스는 Java 14에서 처음 소개되고, 16에서 정식으로 릴리즈된 새로운 클래스 타입으로 불변 데이터를 보관하기 위한 Java의 특수한 클래스이다.

**레코드 클래스는 주로 데이터 전송 클래스를 생성할 때 사용된다**. 이전에는 기본 데이터 클래스를 만들려면 생성자, `getter`, `equals()`, `hashCode()`, 와 같이 상단한 양의 코드를 작성해야 했다. 

```java
public record Person(String name, int age){}
```

이제는 이 한줄로 일반적인 POJO(Plain Old Java Object)에 대해 작성하는 **보일러플레이트 코드를 대체**한다.

## 컴파일시 제공되는 기능

- 생성자
- 각 필드에 대한 `getter` 메서드
- `equals()`, `hashCode()`, 그리고 `toString()`
- `final` 키워드

[Oracle 문서](https://docs.oracle.com/en/java/javase/17/language/records.html)에 따르면, 레코드 클래스는 데이터를 데이터로 모델링하는 간결한 방법이라고 한다.

## Record 클래스 사용 방식

레코드 클래스를 사용하기 위해서 다음과 같이 선언하면 된다.

```java
public record Rectangle(double length, double){}
```

이렇게 하면 컴파일러가 final 필드를 가진 클래스로 변환하고 자동으로 생성자, getter 등을 생성한다.

### 생성자

레코드 클래스의 정식 생성자(canonical constructor)를 아래와 같이 명시적으로 선언할 수 있다.

```java
public record Rectangle(double length, double width){
	public Rectangle(double length, double width){
		if(length <= 0 || width <= 0) throw new Exception();
	}
	this.length = length;
	this.width = width;
}
```

이렇게 되면 클래스의 필드들이 반복되는데 이를 생략하려면 컴팩트 생성자(compact constructor)로 선언할 수 있다.

```java
public record Rectangle(double length, double width){
	public Rectangle {
		if(length <= 0 || width <= 0) throw new Exception();
	}
}
```

레코드 클래스에서는 생성자의 매개변수뿐만 아니라 변수 할당도 생략할 수 있다.

### static

레코드 클래스에서는 일반 클래스와 마찬가지로 정적 필드, 정적 초기화 및 정적 메서드를 선언할 수 있다. 하지만, 인스턴스 변수나 인스턴스 초기화는 할 수 없다.

```java
public record Rectangle(double length, double width) {
    
    // Static field
    static double goldenRatio;

    // Static initializer
    static {
        goldenRatio = (1 + Math.sqrt(5)) / 2;
    }

    // Static method
    public static Rectangle createGoldenRectangle(double width) {
        return new Rectangle(width, width * goldenRatio);
    }
    
    // Field declarations must be static:
    BiFunction<Double, Double, Double> diagonal;

    // Instance initializers are not allowed in records:
    {
        diagonal = (x, y) -> Math.sqrt(x*x + y*y);
    }
}
```

## Record 클래스 사용의 모범 사례

### 1. 불변 데이터 모델에 레코드 사용

레코드 클래스는 변경 불가능하기 때문에 다음과 같은 경우에 적합하다.

- DTOs (Data Transfer Objects)
- API responses
- Event payloads
    - 카프카를 사용하는 event-drvien architecture의 payload

### 2. 레코드 내부에서는 비즈니스 로직을 피해야 한다.

레코드는 동작이 아닌 데이터를 모델링하는데 사용된다. 메서드를 추가할 수는 있지만 복잡한 로직은 피하는게 좋다.

### 3. 유효성 검사를 위해 컴팩트 생성자 사용

컴팩트 생성자는 간결성을 유지하면서 불변성을 적용하는데 이상적이다.

### 4. setter와 같은 가변 동작은 피해야 한다.

레코드 클래스의 목적은 불변성이다. 필드를 강제로 변경하는 리플렉션이나 바이트코드 조작은 피해야 한다.

### 5. ORM 프레임워크와 함께 사용할 때 주의해야 한다.

하이버네이트와 같은 ORM은 인자 없는 생성자와 변경 가능한 필드에 의존한다. 레코드에서는 이를 허용하지 않으므로 JPA 엔티티와 함께 직접 사용하는 것은 권장되지 않는다.

## 주의할 점

레코드 클래스를 DTO에 사용한다면, 직렬화/역직렬화 과정에 주의할 점이 있다.

Spring Framework 기반 웹 프로젝트에서는 보통 **Jackson 라이브러리**가 기본적으로 포함되어 있으며, 이를 통해 객체의 직렬화/역직렬화가 처리된다.

<p align="center">
<img width="537" height="139" alt="Image" src="https://github.com/user-attachments/assets/07e92f7b-84d5-4d51-b74b-27e4ce4c6fb6" /></p>

Jackson은 객체를 만들 때 사용 가능한 생성자를 우선적으로 탐색한다. 우선순위는 @JsonCreator가 붙은 생성자, 매개변수 이름이 JSON 키와 일치하는 생성자, 마지막으로 기본 생성자이다. 이후에는 남은 필드를 setter나 리플렉션을 통해 채운다.

문제는 record의 경우 모든 필드가 final이고 setter가 없다는 점이다. 구버전 Jackson(2.12 미만)은 record를 일반 클래스처럼 취급했기 때문에, canonical constructor(모든 필드를 받는 생성자)를 자동으로 인식하지 못해 역직렬화가 실패할 수 있었다.

이 문제를 해결하기 위해 [Jackson 2.12](https://cowtowncoder.medium.com/jackson-2-12-features-eee9456fec75) 버전부터는 record 타입을 공식 지원하며, 역직렬화 시 canonical constructor를 호출해 객체를 생성한다. 

**따라서 프로젝트에 레코드를 도입하기 전에, 사용하는 직렬화/역직렬화 라이브러리(Jackson, Gson 등)가 레코드를 지원하는 버전인지 반드시 확인해야 한다.**

### 참고

- https://docs.oracle.com/en/java/javase/17/language/records.html

- https://www.gaurgaurav.com/2024_advancedJava_exploringRecordClasses

- https://www.javacodegeeks.com/2025/07/java-record-classes-best-practices-and-real-world-use-cases.html

- https://jangjjolkit.tistory.com/69

- chatGPT