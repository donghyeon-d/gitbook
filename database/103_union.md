# UNION 여러 개의 테이블을 세로로 결합하기
## UNION ALL
- 비슷한 구조를 가지는 테이블을 하나로 합친 결과를 구할 수 있음
- 존재하는 컬럼이 모두 같거나, SELECT 구문을 통해서 컬럼을 동일하게 맞춰줘서 UNION 할 수 있음
- 테이블의 일부만 일치하는 경우 일치 하지 않는 칼럼을 제외하거나, 디폴트 값을 넣어줘야 함
- 사용 예시
    ```sql
    SELECT 'app1', AS app_name, user_id, name, email FROM app1_mst_users
    UNION ALL
    SELECT 'app2', AS app_name, user_id, name, NULL AS email FROM app2_mst_users
    ```
    - 결과물에서는 어떤 열이 어떤 테이블에서 온지 않 수 없으니, app_name으로 구분해줌
    - app2_mst_users는 email 컬럼이 없어서 NULL로 채워줌

## UNION DISTINCT (또는 UNION) 
- 데이터의 중복을 제외한 결과를 얻을 수 있음
- 계산 비용이 많이 들어가기 떄문에 일반적으로 UNION ALL을 더 많이 사용함
