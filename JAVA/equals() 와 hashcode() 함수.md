# equals() & hashCode() 함수
> 모든 Java 클래스의 상위 클래스인 Object 클래스에는 hashCode() 및 equals() 함수가 정의되어 있다. 따라서 모든 Java 객체는 equals() 와 hashCode() 함수를 상속받고 있다. 
## equals() 함수
Object 클래스에서 equals()의 기본 구현은 객체가 참조하는 것이 동일한지 확인하는 것이며, 이는 동일성(identity)을 비교한다.

```
public boolean equals(Object obj) {
        return (this == obj);
    }
```
프로그래밍 상에서 객체가 갖고 있는 값에 대해  동등성(equality)을 비교하기 위해 equals() 함수를 오버라이딩한다. 아래와 같이 두 학생(s1,s2) 객체는 같은 id를 갖지만 서로 다른 메모리를 참조하기 때문에 eqauls() 함수는 false를 반환한다.

```
public class Student{
    int id;
    String name;
}
```

```
public class EqualTest {

    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();
        s1.setId(1);
        s2.setId(1);
        System.out.println(s1.equals(s2)); // false
    }
}
```
 두 학생이 같은 id 값을 갖는 것에 대해 동등성(eqaulity)을 비교하기 위해서 equals() 함수를 오버라이딩해야 한다.

```
 @Override
    public boolean equals(Object obj) {
        if(obj == null)
        {
            return false;
        }
        if (obj == this)
        {
            return true;
        }
        if (getClass() != obj.getClass())
        {
            return false;
        }

        Student s = (Student) obj;
        return this.getId() == s.getId();
    }
```
```
public class EqualTest {

    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();
        s1.setId(1);
        s2.setId(1);
        System.out.println(s1.equals(s2)); // now true
    }
}
```
## hashCode() 함수
런타임에 객체에 대한 고유한 정수 값을 반환한다. 객체가 HashSet과 같은 데이터 구조에 저장되어야 할 때 인덱스 위치를 결정하는 데 사용된다.
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
        System.out.println(set.size()); // 2
    }
}
```
s1, s2 객체는 같은 id를 갖고 있지만 현재 hashCode() 함수는 Object 클래스와 동일하게 객체에 대해 고유한 정수 값을 반환한다. HashSet 내부에 같은 값(id)을 갖는 인스턴스가 하나만 존재하게 하기 위해 hashCode() 함수를 오버라이딩해야 한다.
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
        System.out.println(set.size()); // now 1
    }
}
```
## equals() 함수와 hashCode() 함수 간의 contract
동일한 객체는 동일한 해시 코드를 가져야 한다는 hashCode() 함수의 contract 때문에 equals() 함수를 오버라이딩 할 때, hashCode() 함수도 오버라이딩 해야 한다.
- equals() 함수에 따라 두 객체가 동일한 경우 두 객체 각각에 대해 hashCode 메서드를 호출하면 동일한 정수 결과가 나와야 한다. (If two objects are equal according to the equals method, then calling the hashCode method on each of the two objects must produce the same integer result.)

## JPA 엔티티 선언시 주의할 점
ORM을 사용하는 경우, equals() 함수와 hashCode() 함수를 오버라이딩할 때 필드 참조를 사용하지 않고 항상 getter을 사용해야 한다. ORM에서는 때때로 필드가 lazy load 되어 getter 메서드가 호출돼야 필드를 사용할 수 있기 때문이다.

## 참고
- [Java hashCode() and equals() Methods](https://howtodoinjava.com/java/basics/java-hashcode-equals-methods/)  
 
- [Java 17 API Docs](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#hashCode())