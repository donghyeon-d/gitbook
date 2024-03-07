# 계산한 테이블에 이름 붙여 재사용하기(유사 테이블 사용하기)
## 의미
- DB에 table로 저장되어있지 않지만, 임시로 테이블처럼 만들어서 사용하고 싶은 데이터가 있을 거임. 서브쿼리로 만들기 보다는 임시로 테이블로 만들어서 활용하는 것이 좋은 경우에 사용하게 됨
- CTE : Common Table Expression의 약자 
- WITH 구문을 사용하여 임시테이블을 만들어서 사용함
## 구문
- `WITH <테이블 이름> AS (SELECT ~)`
- 예시
    ```sql
    WITH
    production_sale_ranking AS (
        SELECT
            category_name,
            product_id,
            sales,
            ROW_NUMBER() OVER(PARTITION BY category_name ORDER BY sales DESC) AS rank
        FROM
            product_sales
    ),
    mst_rank AS (
        SELECT DISTINCT rank
        FROM product_sale_ranking
    )
    SELECT *
    FROM mst_rank
    ```
    - WITH 구문으로 임시 테이블을 만듦
    - 본격적인 SELECT 구문보다 위에서 선언해야 함
    - 여러개의 임시테이블을 만들고 싶으면 컴마(,)로 구분하여 선언해줌
    - 앞에서 선언하는 테이블에서는 뒤에 올 테이블을 참조하지 못함. 여기서는 mst_rank가 production_sale_ranking를 참조함. production_sale_ranking가 mst_rank를 참조할 수 없음
