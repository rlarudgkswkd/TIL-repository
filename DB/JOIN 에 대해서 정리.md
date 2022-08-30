# Join 사용하기

### Join 개념
- 테이블 두개를 합치거나 정보조합시 사용
- join 자체가 테이블의 전체 데이터를 사용하는 연산이기에 메모리와 연산 부담 큼. 그래서 전략에 대한 이해가 필요함.

* 잘 이해하고 쓰면 좋은점
- out of memory 방지
- 데이터 처리 시간 단축
- 사용하는 자원 최소화

* JOIN을 위해 정의 되어야하느 내용

- 2개의 테이블 LEFT, RIGHT
- 1개 또는 그 이상의 컬럼 ( 두개 테이블에 공통 존재)
- 원하는 결과를 도출하기 위한 JOIN Expression
- JOIN TYPE

* Join 결과 새로운 테이블 생성

- 두개 테이블에서 1개 이상의 지정한 기준 컬럼으로 하나의 테이블에 모두 표시됌.
- Join 기법에 따라 한쪽에만 있으면 없는 레코드는 null 로 표시

### Join 기법
![image](https://user-images.githubusercontent.com/48428850/187322217-e6c79ac5-34a8-4a9e-9065-b0fd2da016df.png)
