# SQL 함수 정리
## 반올림 - ROUND 함수
> ROUND(값, 자리수)
- ROUND(123.345, 1) = 123.3 
  - 소수점 두번째 자리에서 반올림해서 첫번째 자리수까지 표시
- ROUND(123.345, -1) = 120
  - 정수 첫번째 자리에서 반올림하고 소수부분은 버린다.

## 평균값 - AVG 함수
> AVG(값)

## 날짜 형식 - DATE_FORMAT
> DATE_FORMAT(날짜, format)
- DATE_FORMAT(NOW(), '%Y-%m-%d %p %h:%i') -> 2024-04-23 PM 03:08

## 년, 월, 일 추출 - YEAR, MONTH, DAY
> YEAR(날짜)
- YEAR(NOW()) -> 2024

## 반환 값 문자열 합치기 - COMCAT
> CONCAT(값, 합칠 문자)
- CONCAT(YEAR(NOW()), '년') -> 2024년

## NULL 처리 - IFNULL
> IFNULL(column명, 대체 문자)
- IFNULL(NAME, 'NONE')

# 연산자
## UNION
- 여러 쿼리문들을 합쳐서 하나의 쿼리문으로 만들어주는 방법
- 중복된 값을 제거한다.
- 컬럼명이 동일해야 한다. (같지 않을 경우 as를 이용해서 동일하게 맞춰야 한다.)
### 프로그래머스 문제
- [오프라인/온라인 판매 데이터 통합하기](https://github.com/swkim0911/Algorithm/tree/main/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/4/131537.%E2%80%85%EC%98%A4%ED%94%84%EB%9D%BC%EC%9D%B8%EF%BC%8F%EC%98%A8%EB%9D%BC%EC%9D%B8%E2%80%85%ED%8C%90%EB%A7%A4%E2%80%85%EB%8D%B0%EC%9D%B4%ED%84%B0%E2%80%85%ED%86%B5%ED%95%A9%ED%95%98%EA%B8%B0)
## GROUP BY, HAVING
- HAVIMG -> GROUP BY로 묶인 DATA를 기준으로 필터
- 그룹화하기 위해 집계 함수 (COUNT, MAX, MIN, SUM, AVG) 함께 사용

### 프로그래머스 문제
- [재구매가 일어난 상품과 회원 리스트 구하기](https://github.com/swkim0911/Algorithm/tree/main/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/2/131536.%E2%80%85%EC%9E%AC%EA%B5%AC%EB%A7%A4%EA%B0%80%E2%80%85%EC%9D%BC%EC%96%B4%EB%82%9C%E2%80%85%EC%83%81%ED%92%88%EA%B3%BC%E2%80%85%ED%9A%8C%EC%9B%90%E2%80%85%EB%A6%AC%EC%8A%A4%ED%8A%B8%E2%80%85%EA%B5%AC%ED%95%98%EA%B8%B0)
- [서울에 위치한 식당 목록 출력하기](https://github.com/swkim0911/Algorithm/tree/main/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/4/131118.%E2%80%85%EC%84%9C%EC%9A%B8%EC%97%90%E2%80%85%EC%9C%84%EC%B9%98%ED%95%9C%E2%80%85%EC%8B%9D%EB%8B%B9%E2%80%85%EB%AA%A9%EB%A1%9D%E2%80%85%EC%B6%9C%EB%A0%A5%ED%95%98%EA%B8%B0)
- [저자 별 카테고리 별 매출액 집계하기](https://github.com/swkim0911/Algorithm/tree/main/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/4/144856.%E2%80%85%EC%A0%80%EC%9E%90%E2%80%85%EB%B3%84%E2%80%85%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%E2%80%85%EB%B3%84%E2%80%85%EB%A7%A4%EC%B6%9C%EC%95%A1%E2%80%85%EC%A7%91%EA%B3%84%ED%95%98%EA%B8%B0)

## LIKE
> 패턴에는 ( % )와 ( _ )가 사용되는데 ( % )는 '모든 문자를 의미하고 ( _ )는 한 글자를 의미

## DISTINCT
- DISTINCT는 null 값을 포함한다.
- COUNT(column명)은 null을 제외 -> COUNT(DISTINCT(column명)) null 제외

### 프로그래머스 문제
- [중복제거](https://github.com/swkim0911/Algorithm/tree/main/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4/2/59408.%E2%80%85%EC%A4%91%EB%B3%B5%E2%80%85%EC%A0%9C%EA%B1%B0%ED%95%98%EA%B8%B0)
