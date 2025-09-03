# CPython
CPython은 Python 프로그래밍 언어의 공식 구현체(Python 런타임)이다. Python은 언어 사양(specification)이고, 이를 실제로 실행 가능한 형태로 만든 "구현체(implementation)"가 여러개 있는데 CPython이 가장 널리 쓰이고 공식 표준으로 간주되는 구현체이다. 

CPython 외에도 PyPy, Cython, Jython 등 다른 구현체가 있으며 python.org에서 Python 배포판을 설치하면 CPython이 실행된다. CPython은 "공식 구현체"이기 때문에 다른 파이썬 구현체가 따라야 하는 언어 명세(language specification)까지 포함하고 있다.

언어 명세란, 언어의 문법과 의미를 규정한 공식 문서로, 모든 구현체가 따라야 하는 기준이다. 예컨대 JavaScript는 ECMAScript 사양이 있고, Java는 JLS(Java Language Specification)가 있다. Python은 별도의 국제 표준은 없지만, 공식 문서인 Python Language Reference와 함께 CPython의 동작이 사실상 언어 명세의 역할을 한다.

Python 언어 명세는 Python 언어에 대한 설명을 담은 문서이다. 예를 들어, assert는 예약어이고, []는 인덱싱, 슬라이싱, 빈 리스트 생성에 사용된다고 명시되어 있다.

> 언어 명세란 언어의 문법과 의미를 규정한 공식 문서로, 모든 구현체가 따라야 한다. Java에는 JLS(Java Language Specification)이 있고, JS는 ECMAScript 사양이 있다. Python은 별도의 국제 표준은 없지만, 공식 문서인 Python Language Reference와 함께 CPython의 동작이 사실상 언어 명세 역할을 한다.

## 특징
#### C언어로 구현
- CPython은 C 언어로 작성된 인터프리터이다. 덕분에 C/C++ 기반으로 만든 네이티브 확장 모듈과 쉽게 연결 가능하다.
  - 네이티브 확장 모듈: Python 코드로만 작성된 게 아니라 C/C++같은 네이티브 언어로 작성된 라이브러리
  
  - 네이티브 언어: CPU/OS가 직접 실행 가능한 기계어 코드로 컴파일 되는 언어(C, C++, Rust)

- Numpy, TensorFlow 같은 라이브러리는 CPython에 의존한다.

#### GIL (Global Interpreter Lock)


### 참고
- https://realpython.com/ref/glossary/cpython
- https://realpython.com/cpython-source-code-guide/
- https://en.wikipedia.org/wiki/CPython
- https://devocean.sk.com/blog/techBoardDetail.do?ID=164537
- chatGPT