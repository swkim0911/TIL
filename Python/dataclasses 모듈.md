# 파이썬 dataclasses 모듈
## dataclass란?
파이썬 3.7 버전 부터 dataclasses라는 모듈이 추가되었다. 그리고 `@dataclass`는 데이터를 담는 클래스에 선언하는 데코레이터로 개발자가 데이터 클래스를 선언할 때, 직접 작성하기 번거러운(보일러 플레이트) 코드들을 자동으로 생성해준다.

## dataclass를 사용하지 않고 클래스 작성하기
"직원"을 클래스로 만든다고 가정할 때, 아래와 같이 필요한 필드를 매개변수에 넣고, 또 초기화 과정을 거쳐야 한다. 이러한 코드를 흔히 보일러 플레이트라 하며 만약 필드가 늘어난다면, 비슷한 코드를 개발자가 직접 작성해야 한다.

```
from datetime import date

class Employee:
    def __init__(self, id: int, name: str, hire_date: date, admin: bool = False, employees: list = None) -> None:
        self.id = id
        self.name = name
        self.hire_date = hire_date
        self.admin = admin 
        self.employees = employees if employees is not None else []
```

디버깅이나 로깅을 한다고 이 클래스를 출력하려 한다면, 개발자는 아래와 같이 `__repr__()` 메서드를 추가해서 출력할 내용을 작성해야 한다.

```
from datetime import date

class Employee:
    
    .
    .
    .

    def __repr__(self):
        return (f"Employee(id={self.id!r}, name={self.name!r}, "
                f"hire_date={self.hire_date!r}, admin={self.admin!r}, "
                f"employees={self.employees!r})")
```

두 객체간의 동등성(equality)를 비교하려면 `__eq__()` 메서드 역시 직접 추가해야 한다.

```
from datetime import date

class Employee:
   
    .
    .
    .

    def __eq__(self, other):
        if not isinstance(other, Employee):
            return NotImplemented
        return (self.id == other.id and
                self.name == other.name and
                self.hire_date == other.hire_date and
                self.admin == other.admin and
                self.employees == other.employees)
```

이렇게 이상적으로 동작하는 클래스를 작성하려면 생각보다 많은 보일러 플레이트 코드를 작성해야 한다. 이때 dataclass를 사용한다면 더 빠르고 쉽게 구현할 수 있다.

## dataclass를 사용하고 클래스 작성하기
dataclasses 모듈은 위와 같은 데이터를 담아두기 위한 클래스를 매우 적은 양의 코드를 작성하게 해준다. dataclasses 모듈안에 있는 dataclass 데코레이터를 사용하면 아래 코드와 같다.

```
from dataclasses import dataclass, field
from datetime import date

@dataclass
class Employee:
    id: int
    name: str
    hire_date: date
    admin: bool = False
    employees: list = field(default_factory=list)
```

이렇게 `@dataclass` 데코레이터를 클래스에 선언하면 해당 클래스는 '데이터 클래스'가 된다. 데이터 클래스는 `__init__()`, `__repr__()`, `__eq__()` 와 같은 매서드를 자동으로 생성해줘서 보일러 플레이트 코드를 작성하지 않아도 된다.

데이터 클래스에서 주의할 점은 배열을 선언할 때 `field` 함수를 사용해야 한다는 것이다. 만약 배열 같은 가변 데이터 타입에 기본값을 직접 할당하면, 그 기본값이 클래스 선언 시점에 한 번만 생성되어 모든 인스턴스가 공유하게 된다. 그래서 `employees: list[int] = []`처럼 바로 빈 리스트를 기본값으로 두는 것은 허용되지 않으며(실행시 에러 발생), field(default_factory=list)를 사용해 인스턴스마다 독립적인 리스트를 생성하도록 해야 한다.

## dataclasses 모듈
### 불변 데이터 만들기
데이터 클래스의 필드를 불변성(immutability)이 보장되어야 한다면, `frozen` 옵션을 사용하면 된다. 이 옵션은 자동으로 `__hash__`도 생성하기 때문에 dict/set의 키로 사용 가능하게 해준다.

```
@dataclass(frozen=True)
class Employee:
    id: int
    name: str
    hire_date: date
    admin: bool = False
```
### post_init 메서드 사용해서 필드 생성하기
데이터 클래스를 초기화 시점 이후에 초기화가 필요한 경우 `post_init`을 사용하면 된다. 예를 들어 `id` 필드와 `name` 필드가 필요한 `name_with_id` 필드를 초기화하려면, `id`, `name` 필드는 인스턴스가 생성될 때 생성되기 때문에 동시에 값을 넣어줄 수 없다.

그래서 `post_init`, 즉 초기화(init) 이후라는 메서드를 사용하면 된다.

```
@dataclass(frozen=True)
class Employee:
    id: int
    name: str
    hire_date: date
    admin: bool = False

    def __post_init__(self) -> None:
        self.name_with_id = f"{self.name} - {self.id}"
```

### 출처
- [This is Why Python Data Classes Are Awesome](https://www.youtube.com/watch?v=CvQ7e6yUtnw)
  
- [dataclasses - dataclass](https://docs.python.org/ko/3.7/library/dataclasses.html)

- [파이썬 dataclass란?](https://cocojen.tistory.com/13)

- [파이썬의 데이터 클래스 사용법](https://www.daleseo.com/python-dataclasses/)