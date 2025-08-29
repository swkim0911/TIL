# static 키워드
static 키워드는 주로 클래스 변수나 메소드에 사용되어 클래스 로드시에 메모리에 할당되며, 프로그램이 종료될 때 해제된다. 상수값이나 공유해야 할 자원을 관리할 때 유용하다. 

## static의 메모리 영역

## static 변수
static 변수는 메모리에 한번 할당되어 프로그램이 종료될 때 해제되는 변수이며, 메모리에 한번 할당되면 해당 클래스의 모든 인스턴스가 해당 메모리를 공유하게 된다.

Java 5,6에서 HotSpot JVM을 사용할 경우 PermGen(Permanent Generation) 메모리 영역에 저장되었다. PermGen은 JVM 스펙에서 정의한 Method Area를 HotSpot JVM이 구현한 방식이다. 이후, Java 8부터는 PermGen을 제거하고, Method Area를 Metaspace(네이티브 메모리 기반)으로 구현했다.

두 영역의 차이는 PermGem은 고정된 공간이고 Metaspace는 동적 할당이 가능하며 이를 통해 **PermGen 예측 문제 해결**했다. 저장 위치도 PermGem은 JVM의 힙 메모리 영역이고 Metaspace은 OS 네이티브 메모리 영역이다.
- 네이티브 메모리 영역은 운영체제가 프로세스에게 직접 할당한 메모리 공간을 뜻한다.

라이프 사이클은 JVM이 종료되거나, 해당 클래스를 로드한 클래스 로더가 GC로 수거될 때 같이 제거된다. 

> 클래스 로더는 자바에서 그냥 일반 클래스이며, 인스턴스가 Heap에 생성된다. 따라서 다른 객체와 마찬가지로, GC의 대상이 될 수 있다. 

### PermGem 예측 문제
PermGen 예측 문제는 자바 7 이하에서 고정된 공간인 PermGen 영역 크기를 미리 예측하기 어려워서 생기는 문제를 말한다. 예측이 필요했던 이유는 애플리케이션 서버(Tomcat 등)는 수많은 클래스로 로딩하며 실행 전에 정확히 몇 개를 로딩할지 예측하기 어렵다. 또한, 리플렉션, 동적 프록시, JSP 컴파일 등 런타임에 새로운 클래스를 계속 로딩하는 경우 PermGen 사용량이 급격히 늘어나면서 메모리 해제가 제때 일어나지 않으면 Out Of Memory Exception이 발생할 수 있었다.

그래서 Java 8 이후에는 PermGem을 없애고 **동적 할당이 가능한 네이티브 메모리 기반의 Metaspace를 도입하면서 "클래스를 몇 개 로드할지 미리 예측해야 한다"는 제약이 사라졌다.**

## static 메서드
static 메소드는 인스턴스 생성 없이 호출될 수 있으므로, 유틸리티 함수나 상태가 없는 메소드를 구현하는데 사용된다. 클래스 레벨에서 인스턴스 변수에 접근할 수 없으며, 오직 static 변수나 다른 static 메서드에만 접근할 수 있다.

static메서드 역시 Metaspace 내의 클래스 영역에 저장되며 메서드의 바이트코드와 관련 메타데이터가 포함된다.

또한, 정적 메서드가 호출되면, 다른 메서드와 마찬가지로 해당 스레드의 스택(Stack) 위에 실행 프레임이 생성된다.

# final 키워드
final 키워드는 변수, 메소드, 클래스에 대한 변경을 금지한다.
- final 변수: 상수가 되어 값의 변경이 불가능해진다.

- final 메서드: 하위 클래스에서 오버라이딩을 금지한다.

- final 클래스: 상속을 금지한다.
   
# static 과 final 키워드
static final로 선언된 변수는 클래스의 상수로서, 모든 인스턴스에서 공유되며 변경할 수 없는 값이 된다. 이는 프로그램 전체에서 일관된 값을 유지해야 할 때 유용하다.

# 참고
- [자바의 static과 final 키워드 깊게 이해하기](https://f-lab.kr/insight/understanding-java-static-final?gad_source=1&gclid=CjwKCAjw4_K0BhBsEiwAfVVZ__f5ihZl4xzrcs-_1u74xRCzwDF8M7ddwqFCZ_FRmT_7vudmEkHDHhoC2Y0QAvD_BwE)
- [static변수와 static 메서드](https://mangkyu.tistory.com/47)
- [Understanding storage of static methods and static variables in Java](https://www.geeksforgeeks.org/java/understanding-storage-of-static-methods-and-static-variables-in-java)
- chatGPT