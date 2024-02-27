# 문자열 추출
- db에 문자열로 정보를 저장하는 경우가 있음
- 해당 문자열들을 정규표현식, 문자열 관련 함수 등을 통해서 파싱하고 원하는 내용을 추출할 수 있음
## mysql 문자열 파싱 함수
### 왼쪽부터 짜르기
- left(컬럼명 또는 문자열, 문자열 길이)
- 사용 예시
    ```sql
    SELECT left("dongchoi", 4);
    -- 결과 : dong
    ```
### 오른쪽부터 짜르기
- right(컬럼명 또는 문자열, 문자열 길이)
- 사용 예시
    ```sql
    SELECT right("dongchoi", 4);
    -- 결과 : choi
    ```
### 원하는 위치부터 몇개만큼 짜르기
- SUBSTR(컬럼명 또는 문자열, 시작 위치, 문자열 길이)
- 사용 예시
    ```sql
    SELECT SUBSTR("dongchoi", 3, 4);
    -- 결과 : ngch
    ```
### 구분자(delimiter)를 기준으로 짜르기 (split)
- SUBSTRING_INDEX(str, delim, count)
- str를 짜르는데, count 번째 delim 전까지 출력함
- count가 양수이면 delimiter를 왼쪽에서 오른쪽으로 가면서 몇번째인지 세고, 음수이면 오른쪽에서 왼쪽으로 가면서 몇번쨰 delimiter인지 셈
- 사용 예시
    ```sql
    SELECT SUBSTRING_INDEX("www.dongchoi.com", '.', 1);
    -- 결과 : www

    SELECT SUBSTRING_INDEX("www.dongchoi.com", '.', 2);
    -- 결과 : www.dongchoi
    
    SELECT SUBSTRING_INDEX("www.dongchoi.com", '.', -1);
    -- 결과 : com

    SELECT SUBSTRING_INDEX("www.dongchoi.com", '.', -2);
    -- 결과 : dongchoi.com
    ```