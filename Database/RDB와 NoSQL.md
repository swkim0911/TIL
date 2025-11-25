# RDB & NoSQL
## RDB
관계형 데이터베이스는 정보를 테이블, 행, 열로 구조화하는 방법이다. 데이터를 미리 정의된 관계로 구성하는 정보의 집합으로, 데이터는 열과 행으로 구성된 하나 이상의 테이블에 저장되어 서로 다른 데이터 구조가 어떻게 연관되어 있는지 쉽게 파악하고 이해할 수 있도록 한다.

### pros & cons
장점
- ACID 특징으로 데이터 무결성 보장
- 정규화로 데이터 중복 최소화
- SQL 표준 지원 -> 배우기 쉽고, 복잡한 질의 가능

단점
- 수평 확장(scale-out)이 어렵고 비용이 큼
- 비정형/반정형 데이터(JSON, log) 처리에 부적합
- 스키마 변경 비용이 큼

> 데이터 무결성(data integrity)이란 데이터가 라이프사이클의 모든 지점에서 정확하고 완전하며 일관성을 유지한다는 것을 보장하는 걸 말한다.(accurate, complete and consistent)
  

## NoSQL
NoSQL이란 "Not only SQL"의 줄임말로, 관계형 데이터베이스처럼 규칙 기반의 관계형 테이블 형식이 아닌 비관계형 데이터베이스를 의미한다. NoSQL 데이터베이스 시스템은 문서, key-value, graph등 다양한 비정형 데이터를 지원하는 유연한 스키마 모델을 사용한다.

### How does NoSQL works
다양한 NoSQL 데이터베이스가 있지만, 공통되는 특징이 있다.
- 데이터에 대한 고정된 스키마를 선언할 필요가 없는 유연한 스키마를 따르므로 반정형, 비정형 데이터에 이상적이다.
  - 반정형 데이터: JSON, XML 등
  - 비정형 데이터: 텍스트 문서, 이미지 등
  
- 수직 확장으로 용량을 추가하는 대신 수평적으로 확장한다.
- 특정 데이터 모델 및 작업 패턴에 최적화 되어 있다.
- 엄격한 ACID 속성을 따르기 보다는 Eventual Consistency Model과 같이 지연된 일관성(consistency) 제공한다.

### pros & cons 
장점
- 스키마 유연성 -> JSON, key-value, Document, Graph 등 다양한 데이터 저장 가능
- 수평 확장 용이 -> 클러스터링, 샤딩으로 대규모 데이터 처리
- 고성능 -> 특정 사용 사례(캐싱, 세션 관리, 로그 저장 등)에서 매우 빠름
- 더 빠른 쿼리 -> 데이터 중복을 줄이기 위해 정규화된 RDB와 달리, NoSQL은 빠른 쿼리에 최적화되어 있다.

단점
- 전통적 의미의 무결성(참조 무결성, 트랜잭션 보장)은 약함
- 표준 쿼리 언어 부재(DB마다 쿼리 방식 다름)
- 복잡한 JOIN 연산에 비효율





### 참고
- [What is a Relational Database](https://cloud.google.com/learn/what-is-a-relational-database?hl=en)
- [What is NoSQL](https://cloud.google.com/discover/what-is-nosql?hl=en)
- [What is Data Integrity](https://www.ibm.com/think/topics/data-integrity)
- ChatGPT