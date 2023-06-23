# cardinality 카디널리티
## 의미
- 어떤 attribute가 가질 수 있는 유니크한 값의 개수
- 예를 들어 성별 컬럼은 (남자), (여자) 라는 두개의 값을 가짐. 이때, 카디널리티는 2
- attribute가 PK이면 cardinality == N 이 되고, UNIQUE로 정의되어 있으면 cardinality == N - NULL 개수가 됨
    - N은 recode(tuple) 개수를 의미함
    - oracle에서는 NULL이 cardinality에 포함되지 않음 [(참고 : 구루비)](http://wiki.gurubee.net/pages/viewpage.action?pageId=4948754)

<br>

# selectivity 선택도
## 의미
```
selectivity = cardinality / number of records
```
- 데이터 집합에서 특정 값을 얼마나 잘 골라낼 수 있는지에 대한 지표
- 선택도가 1이라는 것은 모든 값이 유니크하다는 의미
- 선택도가 높다는 것은 중복되는 recode 수가 작다는 것을 의미함

<br>

# index에서의 선택도 활용 
## 사용
- 선택도가 높은 attribute에 인덱스를 걸면 인덱스가 특정 recode를 잘 골라낼 수 있음
- DBMS의 optimizer는 index의 선택도가 낮아서 효율이 떨어진다고 판단되면, 그냥 table을 풀스캔 함
- index를 잘 활용하기 위해서는 선택도가 높게 설계해야 함
- 반대로, 선택도가 낮다면 index안쓰고 그냥 full scan으로 하는게 더 좋음
## 선택도가 높은 index를 생성하기 위한 방법
- PK 또는 UNIQUE constraints인 attribute를 기준으로 index를 생성함
- 두 개 이상의 attribute를 조합해서 인덱스를 생성함 (단, 이 경우엔 공간 효율이 낮아짐)

<br>

# 참고
- [programmerinterview.com](https://www.programmerinterview.com/database-sql/selectivity-in-sql-databases/)
- [구루비](http://wiki.gurubee.net/pages/viewpage.action?pageId=4948754)
- [왕 달팽이 블로그](https://soft.plusblog.co.kr/87)