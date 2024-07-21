# JVM(Java Virtual Machine) 이란?
가상 머신은 물리적 기계의 프로그램을 실행하기 위해 물리적 기계(컴퓨터)와 유사한 머신을 소프트웨어로 구현한 것을 말한다. 즉, JVM(Java Virtual Machine)은 Java 애플리케이션을 실행하는 런타임 엔진 역할을 한다. 자바는 WORA(Write Once Run Anywhere)를 구현하기 위해 물리적인 머신과 별개의 가상 머신을 기반으로 동작하도록 설계되었다.

.java 파일을 컴파일하면 .java 파일에 있는 동일한 클래스 이름을 가진 바이트 코드가 포함된 .class 파일이 생성된다. WORA를 구현하기 위해 JVM은 사용자 언어인 자바와 기계어 사이의 중간 언어인 자바 바이트코드를 사용한다.

- JRE는 자바 API와 JVM으로 구성되며, JVM의 역할은 자바 애플리케이션을 클래스 로더(Class Loader)를 통해 읽어 들여서 자바 API와 함께 실행하는 것이다.

## 자바 프로그램 실행과정
1. 프로그램이 실행되면 JVM은 OS로부터 이 프로그램이 필요로 하는 메모리를 할당받는다. JVM은 이 메모리를 용도에 따라 여러 영역을 나누어 관리한다.
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 읽어들여 자바 바이트코드(.class)로 변환시킨다.
3. 클래스 로더를 통해 .class 파일들을 JVM으로 로딩한다.
4. 로딩된 .class 파일들은 Execution Engine을 통해 해석된다.
5. 해석된 바이트코드는 Runtime Data Areas 에 배치되어 실질적인 수행이 이루어진다. 이러한 실행과정 속에서 JVM은 필요에 따라 Thread Synchronizaion과 GC같은 관리작업을 수행한다.
   
<img width="863" alt="image" src="https://github.com/user-attachments/assets/b3093aa0-6a7e-4319-aadd-da7945bce56d">

# Class Loader
클래스 파일을 로드하는데 사용되는 JVM의 하위 시스템이다. 컴파일 타임이 아닌 런타임에 처음으로 클래스를 참조할 때 클래스 파일을 로딩(loading), 링크(linking), 초기화(initialization)한다.
## 로딩(loading)
.class 파일을 읽고 그 내용에 따라 적절한 바이너리 데이터를 만들고 '메소드' 영역에 저장한다. 이때 메소드 영역에 저장되는 데이터는 다음과 같다.
- FQCN(Full Qualified Class Name: 클래스가 속한 패키지명을 포함한 클래스 이름)
- 클래스, 인터페이스, enum
- 메소드, 변수

로딩이 끝나면 해당 클래스 타입의 Class 객체를 생성하여 heap 영역에 저장한다.

## 링크(linking)
- 검증(verification): .class 파일 형식이 유효한지 체크한다.
- 준비(preparation): 클래스 변수(static 변수)에 대해 메모리가 할당되고 기본값으로 초기화된다. 
- 분석(resolution): 클래스의 상수 풀 내 모든 심볼릭 메모리 레퍼런스를 메소드 영역에 있는 실제 레퍼런스로 교체한다.(optional)

## 초기화(Initalization)
static 변수의 값을 할당한다. (static 블럭이 있다면 이때 실행된다.) 

# 런타임 데이터 영역(Runtime Data Area)
JVM이라는 프로그램이 운영체제 위에서 실행되면서 할당받는 메모리 영역이다. 5개의 주요 구성 요소로 구분된다.

## 메서드 영역 (Method Area)
static 변수를 포함하여 클래스 이름, 부모 클래스 이름, 메소드 및 변수 정보 등과 같은 모든 클래스 수준의 정보가 저장된다. JVM당 하나의 메소드 영역만 있으며 공유 리소스이다. 메서드 영역에는 **Runtime Constant Pool**이라는 별도의 관리 영역도 존재한다. 이는 상수 자료형을 저장하여 참조하고 중복을 막아주는 역할을 수행한다.

## 힙 영역 (Heap Area)
문자열 상수가 저장되는 **String Constant Pool** 을 포함하여 객체, 배열 등이 저장된다. JVM당 하나만 생성이 되고 여러 스레드에 대해 메모리를 공유하므로 저장된 데이터는 스레드로부터 안전하지 않다. 힙 영역이 가득 차게 되면 `OutOfMemoryError`를 발생시킨다.

## 스택 영역 (Stack Area)
각 스레드 별로 할당되는 영역이다. JVM 스레드가 생겨날 때, 해당 스레드를 위한 스택(JVM stack)도 같이 만들어진다. 각 스레드들이 메소드를 호출할 때마다 스택 안에 `frame`이란 단위를 추가(push)한다. 메소드가 결과를 반환하고 종료되면 해당 frame 은 스택으로부터 제거(pop) 된다. 
- 스레드가 쓸 수 있는 스택의 사이즈를 넘기게 되면 `StackOverflowError` 가 발생한다.
  - ex) 무한 재귀 호출
- 스택 사이즈를 동적으로 확장할 수도 있는데 확장할 메모리가 부족하거나, 새로운 스래드를 만들 때 필요한 새로운 스택에 할당할 메모리가 부족하면 `OutOfMemoryError` 가 발생한다.

Frame 은 메소드에 대한 정보를 가지고 있는 Local Variable Array, Operand Stack 그리고 Constant Pool Reference 로 구성되어 있다.
- Local Variable Array: 메소드의 지역 변수들을 갖는다.
- Operand Stack: 메소드 내 계산을 위한 작업 공간이다.
- Constant Pool Reference: 메서드 코드의 동적 연결을 지원하기 위해 현재 메서드 타입에 대한 런타임 상수 풀에 대한 참조가 포함되어 있다.

각각의 Thread 별로 메모리를 따로 할당하기 때문에 동시성 문제에서 자유롭다.

## PC Register
각 스레드에는 별도의 PC 레지스터가 있으며 스레드의 현재 실행 명령의 주소를 저장한다. 어떤 시점에서든 각 JVM 스레드는 단일 메서드의 코드, 즉 해당 스레드에 대한 현재 메서드를 실행한다. 해당 메서드가 native 메서드가 아닌 경우, pc 레지스터에는 현재 실행 중인 JVM 명령어의 주소가 포함된다. native 메서드가 실행되고 있는 경우, pc 레지스터의 값은 정의되지 않는다. 

## Native Method Stacks
모든 스레드에 대해 별도의 네이티브 스택이 생성된다. 네이티브 메소드 정보를 저장한다. Stack 영역과 비슷하게 Native Method 가 실행될 경우 Stack 에 해당 메서드가 쌓이게 된다.
- 네이티브 메서드: Java 프로그래밍 언어 이외의 언어로 작성된 메서드

# 실행 엔진(Runtime Engine)
런타임 데이터 영역에 할당된 바이트코드는 실행 엔진에 의해 실행된다. 실행 엔진은 바이트코드를 한 줄씩 읽고 실행한다. 크게 세 부분으로 구분된다.
- 인터프리터: 바이트코드를 한 줄씩 해석한 다음 실행한다. 여기서 단점은 하나의 메서드를 여러 번 호출할 때마다 해석이 필요하다는 점이다.
- JIT 컴파일러: 인터프리터의 효율성을 높이기 위해 사용된다. 자바 바이트코드를 네이티브 코드(기계어)로 컴파일하여 실행속도를 향상시킵니다. 프로그램의 실행 흐름을 분석하여 빈번하게 수행되는 코드 블록을 네이티브 코드로 변환하고 캐싱하여 재사용한다. 이를 통해 인터프리터보다 빠른 실행 속도를 보장한다.
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