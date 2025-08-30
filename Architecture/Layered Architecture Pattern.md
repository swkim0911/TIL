# Introduction

백엔드 프로젝트를 새롭게 시작할 때마다, 항상 계층형 아키텍처를 적용하지만 정작 정확히 이게 무엇인지, 왜 사용하는지 모르고 사용하는 것 같다. 그래서 계층형 아키텍처 패턴에 대해 이해하고자 정리하게 되었다.

해당 내용은 [**Software Architecture Patterns - Mark Richards**](https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/preface01.html?_gl=1*391lzo*_ga*MjI5MTY5MjI3LjE3NTI0ODMzODc.*_ga_092EL089CH*czE3NTI0ODMzODckbzEkZzEkdDE3NTI0ODUwNTAkajI3JGwwJGgw) 리포트를 참고했다.

# Layered Architecture Pattern 구조

계층형 아키텍처 패턴은 가장 일반적인 패턴으로, n 계층 아키텍처 패턴이라고도 한다. 이 패턴은 대부분의 Jakarta EE 애플리케이션에 대한 사실상 표준이다

계층형 아키텍처 패턴에서 컴포넌트들은 수평적인 계층으로 구성되며, 각 계층은 특정 역할을 수행한다. 이 패턴에서는 반드시 존재해야 하는 계층의 수나 종류를 명시하지 않지만, 대부분의 계층형 아키텍처는 보통 아래 그림과 같이 네 가지 표준 계층으로 구성된다.

경우에 따라, 퍼시스턴스 로직이 비즈니스 계층에 포함되어 하나로 통합되기도 한다. 그래서 작은 애플리케이션은 세 개의 계층만으로도 충분할 수 있으며, 반대로 크고 복잡한 애플리케이션은 다섯 개 이상의 계층으로 구성되기도 한다.

각 계층은 애플리케이션 내에서 고유한 **역할과 책임(responsibility**)을 갖고 있다. 또한, 특정 비즈니스 요청을 처리하는 데 필요한 작업에 대한 **추상화** 역할을 해서 다른 계층이 어떤 일을 하는지 알 필요가 없다.

이 패턴의 강력한 특징은 컴포넌트 간의 **관심사의 분리** (Separation of Concern)이다. **특정 계층에 속한 컨포넌트들은 오직 그 계층과 관련된 로직만 다룬다.** 이 특징 덕분에 개발, 테스트, 유지보수가 쉬워진다.

## 4-Tier Layered Architecture
<p align="center">
  <img style="width:50%; height:auto;" alt="Image" src="https://github.com/user-attachments/assets/eee96c14-a620-4393-b9f3-c5b54271acb4" />
</p>
<p align="center">
  <sub>https://www.oreilly.com<sub>
</p>

### Presentation Layer

클라이언트(사용자)와 직접 상호 작용하는 최상위 레이어이다. 주요 책임은 사용자 인터페이스를 처리하는 것이다. 사용자로부터 요청을 받아 검증한 다음, 다음 레이어에 전달한다.

### Business Layer

애플리케이션의 핵심 비즈니스 로직을 처리한 다음, 그 결과를 보통 퍼시스턴스 계층으로 전달한다.

### Persistence Layer

데이터베이스 또는 기타 데이터 저장 시스템과 같은 저장소에서 데이터 저장 및 검색을 처리한다. 애플리케이션이 종료와 무관하게 데이터는 유지되어야 하는 특성을 가진다.

### Database Layer

데이터베이스 자체를 의미한다.

## Layered Architecture Pattern의 핵심 개념
<p align="center">
<img style="width:50%; height:auto;" alt="Image" src="https://github.com/user-attachments/assets/5b1195af-e438-4462-ba23-a72cbd6b3f6d" />
</p>
<p align="center">
  <sub>https://www.oreilly.com<sub>
</p>


각 계층의 요청이 상위 계층에서 하위 계층으로 이동할 때 반드시 바로 아래 계층을 통해 이동하며 이것은 각 계층의 **닫힌(closed) 상태**를 의미한다.

예를 들어 프레젠테이션 계층에서 시작된 요청은 반드시 비즈니스 계층을 먼저 거친 후, 그 다음 퍼시스턴스 계층을 지나야 한다. 단순히 DB 조회나 저장을 위해 여러 계층을 거치는 것보다 직접 접근하는 것이 더 빠르다고 생각할 순 있지만, 이 아키텍처에는 **계층 간 격리(layers of isolation)라는 핵심 개념**이 있다.

**계층 간 격리란, 아키텍처의 한 계층에서 이루어진 변경은 내부에만 국한되며, 많아야 연관된 또 다른 계층에만 영향을 미치는 것을 말한다.** 만약 프레젠테이션 계층에서 퍼시스턴스 계층에 직접 접근하면, 퍼시스턴스 계층의 SQL 변경이 비즈니스 계층은 물론 프레젠테이션 계층에 영향을 주게 된다. 이는 서로 **강하게 결합된(tightly coupled) 구조**가 되어 컴포넌트 간 의존성이 높아지며 이런 아키텍처는 **변경하기 어렵고 비용도 많이 드는 구조**가 된다.

계층 간 격리 개념은 또한 각 계층이 서로 독립적이며, 아키텍처 내 다른 계층의 내부 동작에 대해 거의 알지 못하거나 전혀 알지 못하는 **추상화**를 의미한다.

<p align="center">
<img style="width:50%; height:auto;" alt="Image" src="https://github.com/user-attachments/assets/2f3ac663-1bb1-4d5a-a3b8-ddfe1cdc6b84" />
</p>
<p align="center">
  <sub>https://www.oreilly.com<sub>
</p>

닫힌 계층은 계층 간 격리를 통해 아키텍처 내의 변경을 격리하는데 도움이 되지만, **특정 계층을 열어 두는 것이 타당한 경우도 있다.**

예를 들어, 비즈니스 계층 내의 여러 컴포넌트들이 접근할 수 있는 공통 서비스 요소들(로깅 클레스, 문자열 처리 클래스)과 같은 **공용 서비스 계층**을 아키텍처에 추가하는 경우이다.

이런 경우, 별도의 서비스 계층을 만들어야 접근을 비즈니스 계층으로 제한할 수 있다. 계층이 별도로 존재하지 않으면 다른 계층의 접근 제어를 관리하기 어렵기 때문이다.

하지만 이렇게 구성하면 비즈니스 계층이 퍼시스턴스 계층에 접근하기 위해 서비스 계층을 꼭 거쳐야 하는 문제가 생기므로, 이를 해결하기 위해 **서비스 계층을 '열린 계층(open layer)'으로** 설정해 비즈니스 계층이 우회하여 퍼시스턴스 계층에 직접 접근할 수 있도록 설계하는 것이 일반적이다.

## Layered Architecture Pattern 흐름

<p align="center">
<img style="width:50%; height:auto;" alt="Image" src="https://github.com/user-attachments/assets/1de32254-c40d-4d5f-87cb-3b6cdaa0b7b1" />
</p>
<p align="center">
  <sub>https://www.oreilly.com<sub>
</p>

계층형 아키텍처의 동작을 확인하기 위해, 사용자의 고객 정보를 조회하는 비즈니스 요청을 생각해보자.

1. 고객 화면(custom screen)에서 사용자 요청을 받는다.
2. 이 요청은 custom delegate module로 전달되며, 이 모듈은 요청을 적절한 비즈니스 계층의 컴포넌트로 전달하는 역할을 한다. custom delegate module은 어떤 비즈니스 컴포넌트가 요청을 처리해야 하는지, 어떻게 요청을 전달해야 하는지 알고 있다.
    - controller와 service 사이 어딘가 존재하는 모듈이라 생각하면 된다.
3. 비즈니스 계층의 Customer Object가 해당 요청을 받는다. 이 컴포넌트는 고객 정보를 조회하기 위한 모든 책임을 갖는다. 정보를 얻기 위해 퍼시스턴스 계층의 Customer dao와 Order dao를 호출해 데이터를 가져온다.
4. 퍼시스턴스 계층의 DAO 모듈들은 각각 SQL을 실행해 데이터를 조회한다.
5. 위 요청의 결과를 다시 반대로 Persistence Layer → Business Layer → Presentation Layer 로 전달되고 최종적으로 사용자에게 전달된다.

# 싱크홀 안티 패턴

계층형 아키텍처 패턴은 범용적이고 안정적인 패턴으로, 대부분의 애플리케이션에 적합한 출발점이 될 수 있다.

하지만 이 패턴을 선택할 때 **싱크홀 안티 패턴**을 조심해야 한다. 이 안티 패턴은 요청이 아키텍처의 여러 계층을 통과하면서 **각 계층에서 별다른 처리 없이 단순히 전달만 되는 상황**을 말한다. 이렇게 되면 계층의 존재 의미가 사라지며 의미 없는 boilerplate 코드가 쌓이니 리소스가 낭비된다.

그래도 모든 계층형 아키택처 패턴은 어느 정도 이런 상황이 존재할 수 밖에 없으며, 전체 흐름 중에서 약 20% 정도는 단순한 전달 처리만 이뤄져도 정상적인 구조로 본다.

# Layered Architecture Pattern 특징
| **항목**                              | **평가** | **설명**                                                                                             |
| ------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------- |
| **Overall Agility (유연성)**          |  낮음   | 모놀리식 구조로 인해 변화 대응이 느리고 계층 간 영향이 큼                                            |
| **Easy of Deployment (배포 편의성)**  |  낮음   | 작은 변경에도 전체 재배포가 필요해 CD 파이프라인 구축에 어려움                                       |
| **Testability (테스트 용이성)**       |  높음   | 계층 간 의존 분리로 다른 계층을 모킹(mock)·스텁(stub)으로 대체하기 쉬워 단위 테스트 용이             |
| **Performance (성능)**                |  낮음   | 계층을 여러 번 거치면서 요청 처리에 오버헤드 발생                                                    |
| **Scalability (확장성)**              |  낮음   | 강한 결합력과 모놀리식 형태로 인해 확장 어려움. 각 계층을 분리 배포할 수 있으나 단위가 커서 비효율적 |
| **Easy of Development (개발 편의성)** |  높음   | 구조가 단순하고 역할 분리가 명확해 초기 개발 속도가 빠름                                             |


### 참고

- [Software Architecture Patterns - Mark Richards](https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/ch01.html)

- [How to define the responsibilities of each layer in the layered architecture pattern?](https://www.tencentcloud.com/techpedia/105428)

- [테이블링 오피스의 Layered Architecture](https://techblog.tabling.co.kr/%ED%85%8C%EC%9D%B4%EB%B8%94%EB%A7%81-%EC%98%A4%ED%94%BC%EC%8A%A4%EC%9D%98-layered-architecture-72674e8ed1c6)

- [계층화 아키텍처 (Layered Architecture)](https://hudi.blog/layered-architecture/)
