# 정의
- SELECT 할 때, 어떤 값들을 다른 값으로 매핑하고 싶을 때 사용함. 예를 들어, DB에는 enum의 숫자로 저장했는데 SELECT 할 때 문자열로 갖고 오고 싶을 때 사용함

# 사용 예시
```sql
SELECT user_id, 
    CASE
        WHEN register_device = 1 THEN '테스크톱'
        WHEN register_device = 2 THEN '스마트폰'
        WHEN register_device = 3 THEN '애플리케이션'
        ELES '없음'
    END AS device_name
FROM   tb_account
;
```
- tb_account TABLE 에서 register_device COLUMN의 값을 SELECT 하는데, 그 값이 1인거는 테스크톱으로 2인거는 스마트폰으로 3인ㅌ거는 애플리케이션으로 불러옴
- ELES : WHEN에서 지정되지 않은 값이 있는 것들에 대한 default 설정
- END : CASE 문을 마칠때는 적어줌
- AS device_name : 불러온 데이터의 칼럼명을 지정해줌
