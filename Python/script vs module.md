# script vs module
스크립트는 일반적으로 단독으로 직접 실행 가능한 코드가 있는 파일이다. 모듈은 일반적으로 다른 프로그램에 필요한 변수나 함수를 정의해 놓고 사용하는 용도의 파일이다.

```
# area.py
PI = 3.14

def circle(radius):
	return PI * redius * radius

```
area.py 파일에는 프로그램에 필요한 함수들만 정의하고 함수들을 직접 사용하지 않으며 이를 모듈이라 볼 수 있다.

```
# run.py
import area

x = 5
print('반지름의 길이가 {}인, 원의 면적은 {}이다.'.format(x, area.circle(x)))
```
run.py 파일에는 실제로 함수들을 사용하는 코드가 있고, 이 파일을 실행시키면 함수가 실행되므로 스크립트라 볼 수 있다.


하지만 스크립트와 모듈은 그 안에 어떤 내용을 담을지 개발자가 정할 뿐 파일 자체에 특별한 차이가 있는 건 아니다. 그래서 어떤 파이썬 파일이든 직접 실행할 수도 있고 다른 곳에서 불러올 수 있다. 즉, 어떤 파일이든 상황에 따라 모듈이 될 수 있고 스크립트가 될 수 있다. 예를 들어 아래와 같이 area.py 파일에 실행 코드를 포함하면

```
# area.py
PI = 3.14

def circle(radius):
	return PI * redius * radius

x = 6

print('반지름의 길이가 {}일 때, circle 함수는 {}을 반환한다.'.format(x, circle(x)))

```
area.py 파일을 함수를 테스트하는 스크립트로 사용할 수 있다. 하지만 이렇게 구현했을 때, area 모듈을 import한 app.py에서도 해당 테스트 코드가 실행되는 문제가 생길 수 있다. 그 이유는 모듈을 import하면 모듈 안에 있는 코드가 처음부터 끝까지 모두 실행되기 떄문이다.

이 문제를 해결하기 위해서 `__name__`이라는 내장 변수를 사용해야 한다. `__name__`은 "이 코드가 파이썬에게 어떤 '이름'으로 인식되고 있는가"를 나타낸다. 

파이썬 입장에서 모든 `.py`파일은 두 가지로만 구분한다. 파일이 직접 실행되면 `__name__`값은 "__main__"이 되고, 다른 코드에서 import하면, 그 모듈의 **경로 이름**이 된다.

 예를 들어 area.py 파일에 `__name__`을 출력하면 "_main__"이라고 출력되고, app.py 파일에서 area 모듈을 import하면 `__name__` 출력 값은 "area"라고 다온다.

그래서 `__name__` 내장 변수를 사용해서 파일이 직접 실행되는지, 아니면 import 되는지에 따라 코드 흐름을 제어할 수 있다. 파일을 직접 실행할 때만 실행하고 싶은 코드는 `if __name__== "__main__"` 조건문 안에 두면된다.








### 참고
- https://doyyy.tistory.com/26
- https://stackoverflow.com/questions/2996110/what-is-the-difference-between-a-module-and-a-script-in-python