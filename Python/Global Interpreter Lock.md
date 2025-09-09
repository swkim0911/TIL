# GIL (Global Interpreter Lock, 전역 인터프리터 락)
## GIL 이란
**GIL은 CPython 인터프리터의 제어권을 단 하나의 스레드만 가질 수 있도록 보장하는 일종의 뮤텍스(mutex)이다.** 즉, 어느 시점에서든 단 하나의 스레드만 **실행 상태**에 있을 수 있다.

> 파이썬 인터프리터는 파이썬 소스 코드를 읽어 바이트코드로 변환하고 이를 실행하는 역할을 한다. 동시에 메모리 관리(객체 생성·삭제, GC), 스레드 관리, 예외 처리등 프로그램 실행 환경 전반을 책임진다. 

> 실행 상태: CPU를 실제로 점유해서 명령어를 수행 중인 상태

GIL은 CPython 구현체에만 존재하는 특징이다. 그리고 GIL은 두 개 이상의 CPU 코어가 있는 멀티스레드(하드웨어 스레드) 아키텍처에서도 한 번에 하나의 스레드만 실행하도록 허용하기 때문에 Python의 "악명 높은" 기능이다.

## 파이썬에 GIL이 필요한 이유
파이썬에서는 모든 것이 객체이며, 객체들에 할당된 메모리를 관리하기 위해 참조 카운팅(reference counting)을 기반으로 가비지 컬렉션(GC)가 동작한다. 즉, 파이썬에서 모든 객체는 자신을 가리키는 참조 횟수를 추적하는 참조 횟수 변수(reference count variable)를 가지며, GC는 그 값이 0에 도달하면 해당 객체에 할당된 메모리를 회수하게 된다. 

참조 횟수에 대한 짧은 코드를 보면 아래와 같다.
```
import sys

a = []
b = a
print(sys.getrefcount(a)) # 3
```
이 코드는 빈 배열 객체가 a, b, 그리고 sys.getrefcount() 인자로 총 3번 참조되어 참조 횟수가 3인걸 출력한다.

다시 돌아가면,

문제는 이 참조 횟수 변수가 두 스레드가 동시에 접근해서 발생하는 경쟁 상태(race condition)로부터 보호되어야 한다는 것이다. 만약 이런 상황이 발생하면, 메모리가 해제되지 않고 계속 남아 메모리 누수가 발생하거나, 아직 참조가 존재하는 객체의 메모리를 잘못 해제할 수도 있다.

이 참조 카운트 변수를 안전하게 지키는 방법으로 락(lock)을 추가하는 방법으로 생각될 수 있다. 하지만 객체마다 락을 추가하면 다수의 락이 존재하고, 이는 또 다른 문제인 데드락을 발생시킬 수 있다. 또한, 락을 반복적으로 획득하고 해제하는 과정에서 성능이 저하될 수 있다.

그래서 GIL은 인터프리터 자체에 하나의 전역 락을 두는 방식으로 이에 대한 해결책을 제시한다. 즉, 어떤 파이썬 바이트코드를 실행하려거든 반드시 이 인터프리터 락을 획득해야 한다. 이렇게 하면 락이 하나뿐이므로 데드락은 방지되고, 락 획득/해제에 따른 성능 저하도 크지 않다. 하지만 그 대신에 CPU-bound 성격의 파이썬 프로그램은 사실상 단일 스레드로 동작하게 된다.

## 멀티 스레드 환경에서 GIL
CPU-bound, I/O-bound 작업 각각을 멀티 스레드 환경에서 처리하는 경우 차이가 있다.

### CPU-bound
CPU-bound 작업은 I/O 작업을 거의 수행하지 않고, CPU 버스트를 길게 갖는 작업이다.

> CPU 버스트: CPU를 실제로 사용해 연산을 수행하는 구간

CPython에서 CPU-bound 작업의 실행 시간에 이점을 얻기 위해 멀티 스레드 환경에서 실행해도 큰 효과를 얻지 못한다. 이유는 앞서 말한 것처럼 GIL 때문에 한 시점에 하나의 스레드만 인터프리터가 실행하기 때문에 코어가 여러 개 있어, 병렬성을 기대해도 GIL은 바이트 코드 실행을 직렬화한다.

여기서 주의할 점은 GIL이 하나의 스레드만 실행된다는 것이 하나의 스레드가 CPU를 계속 점유한다는 뜻이 아닌 "한 시점에" 하나의 스레드가 실행된다는 뜻이다. 즉, 인터프리터는 주기적으로 "check"(타임슬라이스 단위의 GIL 해제 시점)를 실행해서 동시성은 확보한다.

<p align="center"><img style="width:50%; height:auto;" alt="Image" src="https://github.com/user-attachments/assets/f04e95a1-d35f-4f71-9c86-eca113ebf6e1" /></p>



### I/O-bound
I/O-bound 작업은 I/O 작업이 길거나 빈번하여 CPU 버스트가 짧게 나타나는 작업이다.

I/O-bound 작업을 멀티 스레드 환경에서 실행한다면 GIL의 제약을 크게 받지 않는다. 이유는, 실행 상태의 스레드가 I/O 작업을 하기 위해 blocking 상태가 되면서 GIL를 릴리즈 하기 때문이다. 그렇게 되면 다른 스레드는 GIL를 획득하여 곧바로 자신의 작업을 수행하므로, 멀티 스레드 환경에서 동시성을 확보해 효율적으로 동작될 수 있다.

<p align="center"><img style="width:50%; height:auto;" alt="Image" src="https://github.com/user-attachments/assets/ae797a74-dcd1-44a4-9765-afe94ca8ed82" /></p>




### 참고
- https://seungriyou.github.io/posts/python-global-interpreter-lock/

- https://wiki.python.org/moin/GlobalInterpreterLock

- https://realpython.com/python-gil/

- https://www.dabeaz.com/python/GIL.pdf
  
- chatGPT