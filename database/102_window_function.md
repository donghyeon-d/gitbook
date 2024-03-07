# 윈도우 함수
# 의미
- 행과 행간을 비교, 연산, 정의하기위한 함수
- 분석함수 또는 순위함수라고도 함
- group by 또는 order by를 통해 얻어지는 값들을 사용하고 싶을 때, group by 하기 전 칼럼 값들이랑 같이 한번에 select 하고 싶을 때 사용할 수 있음
- 테이블 내부에 윈도라고 부르는 범위를 정의하고, 해당 범위 내부에 포함된 값을 특정 레코드에서 자유롭게 사용하려고 도입함
- OVER(...) 에서 윈도 범위를 정의함
- 서브쿼리를 사용하지 않을 수 있음. 서브쿼리의 역할이 필요한데 좀 간단하게 만든 느낌..?

# 사용
- SELECT WINDOW_FUNCTION(ARGUMENTS) OVER([PARTITION BY 컬럼] [ORDER BY 컬럼] [WINDOWING 절])
FROM 테이블명;
## WINDOW_FUNCTION
### 집계 함수
- SUM : 합계
- MAX : 최댓값
- MIN : 최솟값
- AVG : 평균
- COUNT : 개수
### 순위 함수
- RANK() : 등수. 동점자가 있으면 그만큼 숫자를 건너 뜀. 1등,1등,1등,4등,5등, ... 이런식
- DENSE_RANK() : 등수인데, 동점자가 있어도 숫자는 이어감. 1등,1등,1등,2등,3등, ... 이런식
- ROW_NUMBER() : 1부터 순서대로 Auto Increment 값을 부여함
### 순서 함수
- FIRST_VALUE(column_name) : 가장 처음에 나온 값을 가져옴
- LAST_VALUE(column_name) : 가장 뒤에 나온 값을 가져옴
- LAG(column_name, number) : 현재 행을 기준으로 number만큼 앞의 행을 추출함
- LEAD(column_name, number) : 현재 행을 기준으로 number만큼 뒤의 행을 추출함

## 윈도우 지정
- 윈도우 함수를 실행할 범위를 정의하는 것
- OVER() 구문으로 지정함
- OVER 구문의 괄호 안에 들어가는 것들은 아래와 같이 있으며, 세개를 조합해서 사용할수도 있음
### PARTITION BY
- OVER(PARTITION BY(column_name))
- column_name을 기준으로 group by 하는 것과 비슷하게 묶임
### ORDER BY
- OVER(ORDER BY column_name [DESC])
- DESC가 있으면 내림차순, 없으면 오름차순
### 윈도우 프레임 지정 ROWS
- 어떤 기준으로 순차적인 열들의 집합을 만듦
- ROWS BETWEEN start AND end
- start부터 end 까지에 속하는 열들의 집합
- start, end에 들어가는 것(매크로?상수?)
    - CURRENT ROW : 현재의 행
    - n PRECEDING : n행 앞
    - n FOLLOWING : n행 뒤
    - UNBOUNDED PRECEDING : 이전 행 전부
    - UNBOUNDED FOLLOWING : 이후 행 전부


# 참고
- https://for-my-wealthy-life.tistory.com/48
- 데이터 분석을 위한 SQL 레시피 p.97~105
