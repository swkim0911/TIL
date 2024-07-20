# JVM(Java Virtual Machine) 이란?
가상 머신은 물리적 기계의 프로그램을 실행하기 위해 물리적 머신(컴퓨터)와 유사한 머신을 소프트웨어로 구현한 것을 말한다. 즉, JVM(Java Virtual Machine)은 Java 애플리케이션을 실행하는 런타임 엔진 역할을 한다. 자바는 WORA(Write Once Run Anywhere)를 구현하기 위해 물리적인 머신과 별개의 가상 머신을 기반으로 동작하도록 설계되었다.

.java 파일을 컴파일하면 .java 파일에 있는 동일한 클래스 이름을 가진 .class 파일(바이트 코드)이 생성된다. WORA를 구현하기 위해 JVM은 사용자 언어인 자바와 기계어 사이의 중간 언어인 자바 바이트코드를 사용한다.

- JRE는 자바 API와 JVM으로 구성되며, JVM의 역할은 자바 애플리케이션을 클래스 로더(Class Loader)를 통해 읽어 들여서 자바 API와 함께 실행하는 것이다.

# Class Loader
클래스 파일을 로드하는데 사용되는 JVM의 하위 시스템이다. 컴파일 타임이 아닌 런타임에 처음으로 클래스를 참조할 때 클래스 파일을 로딩(load), 링크(link), 초기화(initialization)한다.
## 로드(loading)
.class 파일의 다음과 같은 정보를 메소드 영역에 저장한다.
- 로드된 클래스를 비롯해 부모 클래스 정보


## 링크(linking)
## 초기화(Initalization)

# 

# 참고
- [JVM Internal](https://d2.naver.com/helloworld/1230)
- [How JVM Works – JVM Architecture?](https://www.geeksforgeeks.org/jvm-works-jvm-architecture/)
- [The JVM Architecture Explained](https://dzone.com/articles/jvm-architecture-explained)