# static 키워드
static 키워드는 주로 클래스 변수나 메소드에 사용되어 클래스 로드시에 메모리에 할당되며, 프로그램이 종료될 때 해제된다. 상수값이나 공유해야 할 자원을 관리할 때 유용하다. 
## static의 메모리 영역
일반적으로 우리가 만든 Class는 static 영역(Method 영역)에 생성되고, new 연산을 통해 생성된 객체는 heap 영역에 생성된다. 객체 생성시 할당된 heap 영역의 메모리는 GC를 통해 수시로 관리를 받는다. 하지만 static 키워드를 통해 static 영역에 할당된 메모리는 GC의 관리 영역 밖에 존재한다. 따라서 static을 자주 사용하게 되면 시스템의 퍼포먼스에 악영향을 준다.

## static 변수
static 변수는 메모리에 한번 할당되어 프로그램이 종료될 때 해제되는 변수이므로, 메모리에 한번 할당되면 여러 객체가 해당 메모리를 공유하게 된다.

## static 메서드
static 메소드는 인스턴스 생성 없이 호출될 수 있으므로, 유틸리티 함수나 상태가 없는 메소드를 구현하는데 사용된다. 클래스 레벨에서 인스턴스 변수에 접근할 수 없으며, 오직 static 변수나 다른 static 메서드에만 접근할 수 있다.

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