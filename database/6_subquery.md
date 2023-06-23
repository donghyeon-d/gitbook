# [Database] subquery를 사용하여 데이터 조회하기 (IN, EXITSTS, ANY, ALL)


## 정의
- 한 쿼리의 결과를 다른 쿼리에서 사용하고 싶을 때 사용함
- 결과가 이용되는 쿼리가 subquery에 해당 됨
- 예시 : ID가 14인 임직원보다 생일이 빠른 임직원의 ID, 이름, 생일을 알고 싶다
	``` SQL
	SELECT id, name, birth_date FROM employee
	WHERE birth_date < (
				SELECT birth_date FROM employee WHERE id = 14
			);
	```
### subquery
- nested query or inner query 라고도 불림
- SELECT, INSERT, UPDATE, DELETE에 포함된 query
- subquery는 괄호 () 안에 기술됨
- 위의 예시에서 `(SELECT birth_date FROM employee WHERE id = 14);`가 subquery

### outer query
- main query 라고도 불림
- subquery를 포함하는 query
- 위의 예시에서 `SELECT id, name, birth_date FROM employee
	WHERE birth_date <` 가 outer query

## 여러개의 attribute를 반환하는 서브쿼리
- ID가 1인 임직원과 같은 부서 같은 성별인 임직원들의 ID와 이름과 직군을 알고 싶다
- 여러개의 attribute를 비교할 수 있음 `(dept_id, sex) = (subquery)`
	``` SQL
	SELECT id, name, position
	FROME employee
	WHERE (dept_id, sex) = (
		SELECT dept_id, sex
		FROM employee
		WHERE id = 1
	);
	```

## query가 참조하는 table
- 해당 attribute가 사용된 query를 포함하여 그 query의 바깥쪽으로 존재하는 모든 queries 중에 해당 attribute 이름을 가지는 가장 가까이에 있는 table을 참조함

## FROM절 안의 subquery
- WHERE 절에 있는 서브쿼리
	``` SQL
	SELECT id, name
	FROM employee
	WHERE id IN (
		SELECT DISTINCT empl_id
		FROM works_on
		WHERE empl_id != 5 AND proj_id IN (
			SELECT proj_id
			FROM works_on
			WHERE empl_id = 5
		)
	);
	```
- FROM 절에 있는 서브쿼리. 위 쿼리와 같은 동작함
	``` SQL
	SELECT id, name
	FROM employee, 
		(
			SELECT DISTINCT empl_id
			FROM works_on
			WHERE empl_id != 5 AND proj_id IN (
				SELECT proj_id
				FROM works_on
				WHERE empl_id = 5
			)
		) AS DSTNCT_E
	WHERE id = DSTNCT_E.empl_id;
	```
	- subquery의 결과로 가상의 table이 만들어지고, employee 테이블과 가상 테이블간 join condition을 통해 결과를 가져옴

<br><br>

# IN
## 기능
- v IN (v1, v2, v3, ... 값들의 집합) : v가 값들의 집합 중에 하나와 같이 같다면 TRUE를 return 함
- 값들의 집합은 명시적으로 적어주는 값들의 집합일 수도 있고, subquery의 결과(set or multiset)일 수도 있음
- v NOT IN (값들의 집합) : 값들에 집합 중 어느 것에도 해당되지 않으면 TURE를 return

## 사용 예시
- WHERE 조건을 설정할 때, 어떤 리스트 중 하나에 해당하는지에 대한 조건을 설정할 수 있음
- `WHERE proj_id = 2001 or proj_id = 2002` 에서 or로 쓰면 보기 불편하니까 `IN`을 사용해서 간단하게 표현해줄 수 있음
- `WHERE proj_id IN (2001, 2002)` : 위와 이 표현은 같은 결과
## subquery에서의 예시
- ID가 5인 임직원과 같은 프로젝트에 참여한 임직원들의 ID를 알고 싶다
	``` SQL
	SELECT DISTINCT empl_id FROM works_on
	WHERE empl_id != 5 AND proj_id IN (
			SELECT proj_id FROM works_on WHERE empl_id = 5
		);
	```

<br><br>

# EXISTS 
## 기능
- WHERE절에서 사용됨
- EXISTS 뒤에 나오는 subquery에 하나라도 해당되는 게 있으면 TRUE를 반환함
- NOT EXITST : subquery의 결과가 단 하나의 row도 없나면 TRUE를 반환함
- correlated query : subquery가 바깥쪽 query의 attribute를 참조할 때, correlated subquery라고 부름
## 사용 예시
- ID가 7 혹은 12인 임직원이 참여한 프로젝트의 ID와 이름을 알고 싶다.
	``` SQL
	SELECT P.id, P.name
	FROM project P
	WHERE EXISTS (
			SELECT *
			FROM works_on W
			WHERE W.proj_id = P.id AND W.empl_id IN (7, 12)
		);
	```
	- P의 row를 하나씩 subquery에 넣어보면서, 서브쿼리의 결과가 하나라도 있다면 그 P의 row는 전체 쿼리(outer query)의 SELECT 대상이 됨

## EXISTS를 IN으로 바꿀 수 있음
- 위 코드와 동일한 동작을 함
	``` SQL
	SELECT P.id, P.name
	FROM project P
	WHERE id IN (
		SELECT W.proj_id
		FROM works_on W
		WHERE W.empl_id IN (7, 12)
		);
	```

## NOT EXISTS
- 2000년대생이 없는 부서의 ID와 이름을 알고 싶다
	``` SQL
	SELECT D.id, D.name
	FROM department AS D
	WHERE NOT EXISTS (
			SELECT *
			FROM employee E
			WHERE E.dept_id = D.id AND E.birth_date >= '2000-01-01'
		);
	```
- NOT IN 으로 바꾸기
	``` SQL
	SELECT D.id, D.name
	FROM department AS D
	WHERE D.id NOT IN (
			SELECT E.dept_id
			FROM employee E
			WHERE E.birth_date >= '2000-01-01'
		);
	```

<br><br>

# ANY
## 기능
- `v 비교연산자 ANY (subquery)`
- subquery가 반환한 결과들 중에 하나라도 v의 비교연산이 TRUE라면, TRUE를 반환함
- `SOME`도 `ANY`와 같은 역할을 함

## 사용 예시
- 리더보다 높은 연봉을 받는 부서원을 가진 리더의ID와 이름과 연봉을 알고 싶다.
	``` SQL
	SELECT E.id, E.name, E.salary
	FROM depardment D, employee E
	WHERE D.leader_id = E.id AND E.salary < ANY (
		SELECT salary
		FROM employee
		WHERE id <> D.leader_id AND dept_id = E.dept_id
	);
	```

<br><br>

# SELECT 절에서의 subquery
- 위 ANY의 사용 예시에서, 해당 부서의 최고 연봉을 알고싶은 경우
- FROM절, WHERE 절은 그래도이고, SELECT절 뒤에 서브쿼리가 붙음
``` SQL
SELECT E.id, E.name, E.salary
	(
		SELECT man(salary)
		FROM employee
		WHERE dept_id = E.dept_id
	) AS dept_max_salary
FROM depardment D, employee E
WHERE D.leader_id = E.id AND E.salary < ANY (
		SELECT salary
		FROM employee
		WHERE id <> D.leader_id AND dept_id = E.dept_id
	);
```

<br><br>

# ALL
## 기능
- `v 비교연산 ALL (subquery)`
- subquery가 반환한 결과들과 v와의 비교 연산이 모두 TRUE라면 TRUE를 반환
## 사용 예시
- ID가 13인 직원과 한번도 같은 프로젝트에 참여하지 못한 임직원들의 ID, 이름, 직군을 알고 싶다.
	``` SQL
	SELECT DISTINCT E.id, E.name, E.position
	FROM employee E, works_on W
	WHERE E.id = W.empl_id AND W.proj_id <> ALL (
			SELECT proj_id
			FROM works_on
			WHERE empl_Id = 13
		);
	```

<br><br>

# 참고 사항 
## IN vs EXISTS 성능 비교
- RDBMS의 종류와 버전에 따라 다름
- 최근 버전은 많은 개선이 이루어져서 IN과 EXISTS의 성능 차이가 거의 없음
- 사용할 때 직접 확인해보기

<br><br>

# 참고
- [유튜브 쉬운코드](https://youtu.be/aL0XXc1yGPs)
