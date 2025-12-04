# equals() & hashCode() 함수

모든 Java 클래스의 상위 클래스인 Object 클래스에는 hashCode() 및 equals() 함수가 정의되어 있다. 따라서 모든 Java 객체는 equals() 와 hashCode() 함수를 상속받고 있다.

## equals() 함수

Object 클래스에서 **equals()의 기본 구현은 객체가 참조하는 것이 동일한지 확인**하는 것이며, 이는 **동일성(identity)**을 비교한다.

```
public boolean equals(Object obj) {
        return (this == obj);
    }
```

프로그래밍 상에서 객체가 갖고 있는 값에 대해 **동등성(equality)**을 비교하기 위해 **equals() 함수를 오버라이딩**한다.

코드로 확인하기 위해 id, name 필드를 갖고 있는 Student 클래스를 선언해보자.

```
public class Student{
    int id;
    String name;
}
```

그리고 아래와 같이 두 학생(s1,s2) 객체를 선언해서 같은 id를 설정했을 때를 보면, 서로 같은 id를 갖지만 결국 서로 다른 메모리를 참조하기 때문에 equals() 함수의 기본 구현에 의해 false를 반환한다.

```
public class EqualTest {

    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();
        s1.setId(1);
        s2.setId(1);
        System.out.println(s1.equals(s2));// false
    }
}
```

두 학생이 같은 id 값을 갖는 것에 대해 동등성(eqaulity)을 비교하기 위해서 equals() 함수를 오버라이딩해야 한다.

```
 @Override
    public boolean equals(Object obj) {
        if(obj == null){
            return false;
        }
        if (obj == this){
            return true;
        }
        if (getClass() != obj.getClass()){
            return false;
        }

        Student s = (Student) obj;
        return this.getId() == s.getId();
    }
```

equals() 함수를 오버라이딩하고 다시 같은 테스트를 했을 때 true를 반환하는 것을 알 수 있다.

```
public class EqualTest {

    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();
        s1.setId(1);
        s2.setId(1);
        System.out.println(s1.equals(s2));// now true
    }
}
```

## hashCode() 함수

hashCode() 함수는 **런타임에 객체에 대한 고유한 정수 값을 반환**한다. 객체가 HashSet과 같은 데이터 구조에 저장되어야 할 때 인덱스 위치를 결정하는 데 사용된다.

Student 클래스와 함께 hashCode() 함수의 역할을 테스트 해보자.

```
public class HashTest {

    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();
        s1.setId(1);
        s2.setId(1);

        HashSet<Student> set = new HashSet<>();
        set.add(s1);
        set.add(s2);
        System.out.println(set.size());// 2
    }
}
```

s1, s2 객체는 같은 id를 갖고 있지만 현재 hashCode() 함수는 **Object 클래스와 동일하게 객체에 대해 고유한 정수 값을 반환**한다. 따라서 HashSet의 사이즈를 조회하면 2가 나오는 걸 알 수 있다.

```
@Override
public int hashCode()
{
    final int PRIME = 31;
    int result = 1;
    result = PRIME * result + getId();
    return result;
}
```

**HashSet 내부에 같은 값을 갖는 인스턴스가 하나만 존재(여기서는 Student 객체)하게 하기 위해 hashCode() 함수를 오버라이딩해야 한다.** 오버라이딩할 때, 반영되고자 하는 값(id)를 포함하여 반환하도록 하면 의도한 결과를 얻을 수 있다.

```
public class HashTest {

    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();
        s1.setId(1);
        s2.setId(1);

        HashSet<Student> set = new HashSet<>();
        set.add(s1);
        set.add(s2);
        System.out.println(set.size());// now 1
    }
}
```

## equals() 함수와 hashCode() 함수 간의 contract

**동일한 객체는 동일한 해시 코드를 가져야 한다는 hashCode() 함수의 contract 때문에 equals() 함수를 오버라이딩 할 때, hashCode() 함수도 오버라이딩 해야 한다.**

- **equals() 함수에 따라 두 객체가 동일한 경우 두 객체 각각에 대해 hashCode 메서드를 호출하면 동일한 정수 결과가 나와야 한다.** (If two objects are equal according to the equals method, then calling the hashCode method on each of the two objects must produce the same integer result.)

## JPA 엔티티를 사용할 때 주의할 점

ORM을 사용할 때, hashCode()와 equals()를 오버라이딩 한다면 항상 **getter 메서드를 사용**하고, **필드 참조는 사용하면 안된다.** 이유는 ORM에서는 필드가 지연 로딩이 될 수 있으며, getter 메서드를 호출하기 전까지는 해당 값을 사용할 수 없기 때문에 동등성 판단이 깨질 수 있다.

예를 들어 Employee 클래스에서 e1.phone_number = e2.phone_number로 비교한다면, phone_number 필드가 지연 로딩 상태일 수 있다. 그렇다면 phone_number는 null일 수 있고, 버그가 발생할 수 있다.

반면에, e1.getPhoneNumber() == e2.getPhoneNumber()를 사용하면, 지연 로딩이라도 getter 호출시 ORM이 값을 채우기 때문에 올바르게 비교가 가능하다.

### 참고

- [Java hashCode() and equals() Methods](https://howtodoinjava.com/java/basics/java-hashcode-equals-methods/)
- [Java 17 API Docs](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())