# JVM(Java Virtual Machine) 이란?
**JVM(Java Virtual Machine)은 Java 바이트 코드가 실행될 수 있는 런타임 환경을 제공한다.** 자바는 WORA(Write Once Run Anywhere)를 구현하기 위해 물리적인 머신과 별개의 가상 머신을 기반으로 동작하도록 설계되었다.

.java 파일을 컴파일하면 .java 파일에 있는 동일한 클래스 이름을 가진 바이트 코드가 포함된 .class 파일이 생성된다. **WORA를 구현하기 위해 JVM은 사용자 언어인 자바와 기계어 사이의 중간 언어인 자바 바이트코드를 사용한다.**

- JRE는 자바 API와 JVM으로 구성되며, JVM의 역할은 자바 애플리케이션을 클래스 로더(Class Loader)를 통해 읽어 들여서 자바 API와 함께 실행하는 것이다.

## 자바 프로그램 실행 과정
0. 컴파일 (실행 전 단계)
 - javac 컴파일러가 .java 소스코드를 읽고 .class 파일(바이트코드)을 생성한다.
1. JVM 시작 & 메모리 할당
 - 사용자가 java Main 실행 → OS가 JVM 프로세스에 메모리 공간 할당한다.
 - JVM은 이 메모리를 Runtime Data Areas (Method Area, Heap, Java Stack, PC Register, Native Stack)로 나눠 관리한다.
2. 클래스 로딩
 - Class Loader Subsystem이 .class 파일을 읽어 JVM으로 가져온다.
 - Loading -> Linking -> Initialization 
3. 실행 준비
 - 클래스 로딩/초기화가 끝나면 JVM은 public static void main(String[] args) 메서드를 찾아 실행 준비를 한다.
4. 실행 (Execution)
 - Execution Engine이 바이트코드를 실행한다.
   - Interpreter: 바이트코드를 한 줄씩 해석 후 실행
   - JIT Compiler: 자주 실행되는 바이트코드를 네이티브 코드로 컴파일해 성능 최적화

<p align="center"><img width="500" alt="image" src="https://github.com/user-attachments/assets/b3093aa0-6a7e-4319-aadd-da7945bce56d"></p>

# Class Loader
클래스 파일을 로드하는데 사용되는 JVM의 하위 시스템이다. **<mark>컴파일 타임이 아닌 런타임에 처음으로 클래스를 참조할 때</mark> 클래스 파일을 로딩(loading), 링크(linking), 초기화(initialization)한다.**

> 여기서 참조라는 것은 `new` 키워드로 객체 생성할 때, 정적 필드나 메서드를 사용할 때, 리플렉션으로 클래스를 불러올 때, 상속/인터페이스로 인해 클래스를 로딩할 때가 있다.

## 로딩(loading)
**.class 파일을 읽고 그 내용에 따라 적절한 바이너리 데이터(메타데이터)를 만들고 '메소드' 영역에 저장한다.** 이때 메소드 영역에 저장되는 데이터는 다음과 같다.
- FQCN(Full Qualified Class Name: 클래스가 속한 패키지명을 포함한 클래스 이름)
- 클래스, 인터페이스, enum
- 메소드, 변수
  
> 참고로 이 글에서 말하는 메서드 영역, 힙 영역은 JVM 스펙상 논리적 영역이다. 실제로는 JVM 구현체(Hotspot 등)마다 다르게 매핑됩니다.

로딩이 끝나면 힙 메모리 안에 이 파일(.class)을 표현(메타데이터)하는 `Class` 타입의 객체를 생성한다. 이 `Class` 객체는 메타데이터 캐시 역할을 해서, JVM이 런타임에 리플렉션(reflection)이나 동적 로딩 등에 쓸 수 있다.

```
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class Test {
    
    public static void main(String[] args) {
        Student student = new Student();
        Class<? extends Student> c1 = student.getClass();

        System.out.println(c1.getName()); // 클래스 이름

        Method[] declaredMethods = c1.getDeclaredMethods(); // 선언된 모든 메서드
        for (Method method : declaredMethods)
            System.out.println(method.getName());

        Field[] declaredFields = c1.getDeclaredFields(); // 선언된 모든 필드
        for (Field field : declaredFields)
            System.out.println(field.getName());
    }
}

class Student{
    String name;
    int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

## 링크(linking)
- 검증(verification): .class 파일 형식이 유효한지, 유효한 컴파일러에 의해 생성되었는지 체크한다.
- 준비(preparation): 클래스 변수(static 변수)에 대해 메모리가 할당되고 기본값으로 초기화된다. 
- 분석(resolution): .class 파일 파일의 상수 풀 내 모든 심볼릭 참조를 메소드 영역에 있는 직접 참조로 교체한다.(optional)

## 초기화(Initalization)
이 단계에서 모든 `static` 변수는 코드에 정의된 값으로 초기화되고, `static { ... }` 블록이 있으면 실행된다.

# 런타임 데이터 영역(Runtime Data Area)
JVM이라는 프로그램이 운영체제 위에서 실행되면서 할당받는 메모리 영역이다. 5개의 주요 구성 요소로 구분된다.

## 메서드 영역 (Method Area)
static 변수를 포함하여 클래스 이름, 부모 클래스 이름, 메소드 및 변수 정보 등과 같은 모든 클래스 수준의 정보가 저장된다. **JVM당 하나의 메소드 영역만 있으며 공유 리소스이다.** 메서드 영역에는 **Runtime Constant Pool**이라는 별도의 관리 영역도 존재한다. 이는 상수 자료형을 저장하여 참조하고 중복을 막아주는 역할을 수행한다.

> .class 파일 안에는 Constant Pool Table이라는 영역이 있다. 여기에는 클래스가 참조하는 문자열, 상수 리터럴, 클래스/메서드/필드에 대한 심볼릭 참조 정보가 있는데, .class 파일이 JVM에 로딩될 때 그 파일의 Constant Pool이 Method Area안에 런타임 버전으로 올라간다.

### 심볼릭 참조와 직접 참조
심볼릭 참조란 .class 파일의 Constant Pool Table에 저장된 **이름 기반 정보**를 의미한다. 즉, 클래스/메서드/필드/인터페이스/상수 등을 직접 메모리 주소로 참조하는 게 아니라 문자열 이름, 타입 서명(Signature), 설명자 같은 심볼(symbol) 로만 가리키고 있는 상태이다. 런타임에 JVM이 이 심볼릭 참조를 해석(Resolution)해서 직접 참조(Direct Reference) 로 바꿔줘야 실제 실행이 가능하다. 

반면, 직접 참조는 런타임에 실제 메모리 주소와 같이 실행 가능한 참조를 말한다. 

이제 실제 .class 파일을 확인하면 다음과 같다.

```
public class Main {

    public static void main(String[] args){
        String s = "Hello";
        System.out.println(s);
    }
}
```

이 코드를 컴파일(.java -> .class)해서 상수 풀의 일부를 보면 다음과 같다.

```
#7 = String             #8             // Hello
#8 = Utf8               Hello
#9 = Fieldref           #10.#11        // java/lang/System.out:Ljava/io/PrintStream;
#10 = Class              #12            // java/lang/System
#11 = NameAndType        #13:#14        // out:Ljava/io/PrintStream;

```
여기서
 - "Hello" → 리터럴 값 (상수 자체)
 - java/lang/System, out, Ljava/io/PrintStream; → 심볼릭 참조 (단순 이름 정보)

즉, 클래스, 필드, 메서드 등은 전부 "문자 기반 이름표"로만 저장되어 있다. 

이후에, JVM이 클래스를 로딩하면 Constant Pool의 심볼릭 참조를 보고 실제 위치를 Method Area에서 찾아 연결한다.
- "java/lang/System" → Method Area의 System.class 메타데이터로 매핑
- out → System 클래스의 static 필드 out 주소로 매핑


## 힙 영역 (Heap Area)
문자열 상수가 저장되는 **String Constant Pool** 을 포함하여 객체, 배열 등이 저장된다. JVM당 하나만 생성이 되고 여러 스레드에 대해 메모리를 공유하므로 저장된 데이터는 스레드로부터 안전하지 않다. 힙 영역이 가득 차게 되면 `OutOfMemoryError`를 발생시킨다.

## 스택 영역 (Stack Area)
**각 스레드 별로 할당되는 영역이다. JVM 스레드가 생겨날 때, 해당 스레드를 위한 스택(JVM stack)도 같이 만들어진다.** 각 스레드들이 메소드를 호출할 때마다 스택 안에 `frame`이란 단위를 추가(push)한다. 메소드가 결과를 반환하고 종료되면 해당 frame 은 스택으로부터 제거(pop) 된다. 
- 스레드가 쓸 수 있는 스택의 사이즈를 넘기게 되면 `StackOverflowError` 가 발생한다.
  - ex) 무한 재귀 호출
- 스택 사이즈를 동적으로 확장할 수도 있는데 확장할 메모리가 부족하거나, 새로운 스래드를 만들 때 필요한 새로운 스택에 할당할 메모리가 부족하면 `OutOfMemoryError` 가 발생한다.

Frame 은 메소드에 대한 정보를 가지고 있는 Local Variable Array, Operand Stack 그리고 Constant Pool Reference 로 구성되어 있다.
- Local Variable Array: 메소드의 지역 변수들을 갖는다.
- Operand Stack: 메소드 내 계산을 위한 작업 공간이다.
- Constant Pool Reference: 메서드 코드의 동적 연결을 지원하기 위해 현재 메서드 타입에 대한 런타임 상수 풀에 대한 참조가 포함되어 있다.

각각의 Thread 별로 메모리를 따로 할당하기 때문에 동시성 문제에서 자유롭다.

## PC Register
**각 스레드에는 별도의 PC 레지스터가 있으며 스레드의 현재 실행 명령의 주소를 저장한다.** 어떤 시점에서든 각 JVM 스레드는 단일 메서드의 코드 즉, 해당 스레드에 대한 현재 메서드를 실행한다. 해당 메서드가 native 메서드가 아닌 경우, pc 레지스터에는 현재 실행 중인 JVM 명령어의 주소가 포함된다. native 메서드가 실행되고 있는 경우, pc 레지스터의 값은 정의되지 않는다. 

## Native Method Stacks
모든 스레드에 대해 별도의 네이티브 스택이 생성된다. 네이티브 메소드 정보를 저장한다. Stack 영역과 비슷하게 Native Method 가 실행될 경우 Stack 에 해당 메서드가 쌓이게 된다.
- 네이티브 메서드: Java 프로그래밍 언어 이외의 언어로 작성된 메서드

# 실행 엔진(Runtime Engine)
런타임 데이터 영역에 할당된 바이트코드는 실행 엔진에 의해 실행된다. 실행 엔진은 바이트코드를 한 줄씩 읽고 실행한다. 크게 세 부분으로 구분된다.
- 인터프리터: 바이트코드를 한 줄씩 해석한 다음 실행한다. 여기서 단점은 하나의 메서드를 여러 번 호출할 때마다 해석이 필요하다는 점이다.
  
- JIT 컴파일러: 인터프리터의 효율성을 높이기 위해 사용된다. 자바 바이트코드를 **네이티브 코드(기계어)**로 컴파일하여 실행속도를 향상시킨다. 프로그램의 실행 흐름을 분석하여 빈번하게 수행되는 코드 블록을 네이티브 코드로 변환하고 캐싱하여 재사용한다. 이를 통해 인터프리터보다 빠른 실행 속도를 보장한다.
  
- Garabage Collector: 프로그램이 동적으로 할당한 메모리 중에서 더 이상 사용하지 않는 메모리를 해제하는 기능을 한다. 

# Java Native Interface(JNI)
자바 애플리케이션에서 C, C++, 어셈블리어로 작성된 함수를 사용할 수 있는 방법을 제공한다. 
- native 키워드를 사용한 메소드

# 참고
- [How JVM Works – JVM Architecture?](https://www.geeksforgeeks.org/jvm-works-jvm-architecture/)
- [The JVM Architecture Explained](https://dzone.com/articles/jvm-architecture-explained)
- [JVM(Java Virtual Machine)이란 무엇인가?](https://asfirstalways.tistory.com/158)
- [JVM Internal](https://d2.naver.com/helloworld/1230)
- [JVM (Java Virtual machine)](https://velog.io/@ddangle/Java-JVMJava-Virtual-Machine)
- [JVM stack과 frame](https://johngrib.github.io/wiki/jvm-stack/)
- [The PC Register](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html)
