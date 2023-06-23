# [Database] SQL 데이터 조회하기(SELECT)

## SELECT 문법
- `SELECT attribute(s) FROM table_name(s) WHERE condition(s);`
- attribute(s) : projection attributes
- condition(s) : selection_condition
- id가 9인 종사자의 이름과 부서를 알고 싶음
	: `SELECT name, position FROM employee WHERE id = 9;`
- WHERE 절을 통해 찾을 조건을 설정할 수 있으며, WHERE가 없다면 모든 tuple을 결과로 가져옴

## 두개의 테이블을 참조하는 SELECT
``` SQL
SELECT employee.id, employee.name position
FROM project, employee
WHERE project.id = 2002 and project.leader_id = employee.id;
```
- `project.leader_id = employee.id` 를 join condition이라 함
- `project.id = 2002` 를 selection condition이라고 함
- `employee.id, employee.name position`를 projection attributes라고 함

<br><br>

# AS 사용하기
## 기능
- 테이블이나 attribute에 별칭(alias)을 붙일 때 사용
- 생략 가능함
## 사용
- a AB b -> a를 b라는 별칭으로 사용하겠다는 의미
- table과 attribute 모두 AS로 별칭 지정 가능
	``` SQL
	SELECT E.id AS leader_id, E.name AS leader_name, position
	FROM project AS P, employee AS E
	WHERE P.id = 2002 and P.leader_id = E.id;
	```
- table : `FROM employee AS E`라고 쓰면, 뒤의 `WHERE` 절에서 `E.id` 로 사용할 수 있음
- attribute : `SELECT E.id AS leader_id`라고 쓰면, 결과물어 `id` 대신 `leader_id`라고 표기됨
- AS를 생략할 수 있음. 위 SQL 문을 아래처럼 써도 동일한 결과가 나옴
	``` SQL
	SELECT E.id leader_id, E.name leader_name, position
	FROM project P, employee E
	WHERE P.id = 2002 and P.leader_id = E.id;
	```

<br><br>

# DISTINCT 사용하기
## 기능
- SELECT 결과에서 중복되는 tuples은 제외하고 싶을 때 사용함
- SELECT 키워드 뒤에 써줌
- 예시)
	``` SQL
	SELECT DISTINCT P.id, P.name
	FROM employee AS E, works_on AS W, project AS P
	WHERE E.position = 'DSGN' and E.id = W.empl_id ans W.proj_id = P.id;
	```

<br><br>


# LIKE 사용하기
## 기능
- WHERE 조건절에서 문자열 pattern matchin에 사용됨
- reserved character
	- % : 0개 이상의 개수를 갖는 문자열
	- _ : 하나의 문자를 의미 글자 자리. 한 글자당 한 글자를 의미함
- escape character
	- \ : reserved character를 본면의 문자로 사용하고 싶을 때 사용
## 사용 예시
- 이름이 N으로 시작하거나 N으로 끝나는 사람을 찾음
	``` SQL
	SELECT name
	FROM employee
	WHERE name LIKE 'N%' or name LIKE '%N';
	```
- 이름이 NG가 포함된 사람을 찾음
	``` SQL
	SELECT name
	FROM employee
	WHERE name LIKE '%NG%';
	```
- 이름이 J로 시작하고 총 4글자의 이름을 가지는 사람을 찾음
	``` SQL
	SELECT name FROM employee WHERE name LIKE 'J_ _ _';
	```
## escape 문자와 함께 LIKE 사용하기
- %로 시작하거나 _로 끝나는 프로젝트 이름을 찾고 싶다면?
	- %와 _ 가 특별 기호로 쓰이고 있는데, 단순 문자로 사용하고 싶다면? 
	- 문자 앞에 \를 붙임
	``` SQL
	SELECT name FROM porject WHERE name LIKE '\%%' or name LIKE '%\_';
	```

<br><br>

# asterisk (*) 사용하기
## 기능
- 선택된 tuples의 모든 attributes를 보여주고 싶을 떄 사용함
- FROM에 table을 여러개 설정했을 땐, 해당 테이블의 모든 attributes가 순차적으로 출력됨
## 사용 예시
- ID가 9인 임직원의 모든 attributes를 알고 싶다
	``` SQL
	SELECT * FROM employee WHERE id = 9;
	```

<br><br>

# 주의사항
1. index
	- SELECT로 조회할 때 조건들을 포함해서 조회한다면, 조건과 관련된 attribtes에 index가 걸려있어야함. 그렇지 않으면 데이터가 많아질수록 조회 속도가 느려짐
	- index에 대해서는 뒤에서 기술
2. 위 내용은 MySQL 기준으로, 다른 RDBMS의 SQL문법과는 다름
3. SELECT와 관련해서는 이 외에도 다양한 조회 기능들과 세부 사항이 있음
<br><br>

# 참고
- [유튜브 쉬운코드](https://youtu.be/aL0XXc1yGPs)
