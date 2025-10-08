# Singleton Pattern with Python
이 글은 싱글턴 패턴을 파이썬 코드로 구현하면서 디자인 패턴과 파이썬 언어의 특징을 함께 살펴볼 목적을 갖고 있다.

## 싱글턴 패턴이란
싱글턴 패턴은 클래스의 인스턴스가 하나만 존재하도록 하면서 이 인스턴스에 대한 전역 접근 지점을 제공하는 생성 디자인 패턴(creational design pattern)이다.

고정된 메모리영역을 얻어 하나의 인스턴스만 생성하기 때문에 메모리 낭비를 방지할 수 있다.

## 객체 생성과 초기화
파이썬에서 객체를 선언할 때, 객체 생성과 속성의 초기화는 `__new__`와 `__init__` 이라는 메서드가 사용된다. 일반적으로 클래스를 정의할 때 `__init__`만 주로 사용했는데, `__new__`는 클래스에 정의되어 있지 않으면 알아서 부모 클래스인 `Object` 클래스의 `__new__`가 호출되어 객체가 생성된다. 객체 생성 후, 속성을 초기화할 때,  `__init__`이 호출되는 것이다.

사용자는 클래스 생성 과정에 관여하고 싶을 때, 직접 `__new__` 메서드를 클래스에 구현해서 `Object` 클래스의 `__new__` 함수가 아닌 오버라이딩된 커스텀 `__new__` 메서드를 호출한다.

사용자가 클래스의 `__new__` 함수를 재정의할 때는, 내부에서 직접 `Object` 클래스의 `__new__` 함수를 호출하고 리턴해줘야 한다. 아래 코드는 `Foo` 클래스는 `__new__` 함수를 직접 오버라이딩했다.

```
class Foo:
    
    def __new__(cls):
        print("__new__ called")
        instance = super().__new__(cls)
        print("instance created:", instance)
        return instance

    def __init__(self):
        print("__self__ called")

Foo()
-- print --
# __new__ called
# instance created: <__main__.Foo object at 0x102fc4bb0>
# __self__ called
```

## 파이썬 싱글턴 패턴 구현
자바에서는 싱글턴 패턴을 구현하면, 하나의 객체만 존재하기 위해 생성자는 private으로 막고 정적 함수를 통해 싱글턴 객체에 접근하게 제한한다. 하지만 파이썬에서는 언어적으로 private으로 생성자를 막을 수 없기 떄문에 **"사실상 하나의 인스턴스만 존재하게"** 구현한다.

따라서 아래와 같이 클래스 변수 `_instance` 속성을 선언하고 `__new__` 함수 안에서 객체가 이미 존재하는지 확인하게 해서 하나의 객체만 존재하게 하면 된다.

```
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            print("__new__ is called")
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        print("__init__ is called")
```

이 코드는 아직 완벽하지 않다. 객체는 유일하게 생성되도록 했지만 생성된 객체를 초기화하는 `__init__`은 여러번 호출하는 대로 속성값이 바뀔 수 있다.

그래서 초기화 함수 호출 여부를 표시하는 인스턴스 변수를 추가해서 이미 초기화가 호출된 경우에는 호출되지 않게 하면 된다.

```
class Singleton:
    _instance = None
    
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            print("__new__ is called")
            cls._instance = super().__new__(cls)
            cls._instance._initialized = False  
        return cls._instance
    
    def __init__(self, name):
        if self._initialized:
            return
        self._initialized = True
        self.name = name
        print("__init__ is called")
```
`cls._instance._initialized = False`는 객체가 생성되었을 때 동적으로 인스턴스 변수를 생성하고 초기화하는 부분이다. 이렇게 클래스 변수가 아닌 "인스턴스 변수"로 초기화 여부를 확인하는 이유는 `__init__ `함수에서 그 플래그 값을 확인해야 하는데 `__init__` 함수의 매개변수에는 `self` 변수가 있기 때문에 인스턴스 변수를 다루는게 더 자연스럽기 때문이다.

`cls = type(self)`로 클래스에 접근할 수 있기 때문에 클래스 변수를 플래그로 잡아도 상관없다.

## 파이썬 싱글턴 패턴 구현 with 데코레이터
여러 클래스를 싱글턴 패턴으로 구현한다고 할 때, 데코레이터를 사용한다면 책임을 분리하게 되어 확장성있는 코드가 된다.

```
def singleton(cls):
    _instances = {}

    def get_instance(*args, **kwargs):

        if cls not in _instances:
            _instances[cls] = cls(*args, **kwargs)
        return _instances[cls]

    return get_instance


@singleton
class Database:
    def __init__(self, url):
        print("Connecting to", url)
        self.url = url

db1 = Database("db1")
```
그래서 이 코드를 설명해보면,

`Database` 클래스에 `@singleton` 데코레이터가 적용되면서 아래와 같이 동작한다.
```
class Database:
    def __init__(self, url):
        print("Connecting to", url)
        self.url = url

Database = singleton(Database)  # 데코레이터가 클래스 정의를 감싼다.
```
즉, `singleton()` 함수의 `cls` 매개변수에는 클래스 객체 `Database`가 들어온다.

이후에, `Database` 객체 선언 시점에는 실제로 클래스가 아니라 함수가 호출되면서 객체가 하나만 존재하게 하는 로직이 적용된다.
```
db1 = Database("db1") => db1 = get_instance("db1")

def get_instance(*args, **kwargs):

        if Database not in _instances:
            _instances[Database] = Database(*args, **kwargs)
        return _instances[Database]

```

그래서 아래와 같이 두 개의 객체를 생성해서 비교하면 서로 같은 객체임을 보인다.
```
a = Database("db1")
b = Database("db2")

print(a.url, b.url)  # db1 db1
print(a is b)        # True
```


### 출처
- [싱글턴 패턴](https://wikidocs.net/69361)
- [Singleton](https://refactoring.guru/design-patterns/singleton)
