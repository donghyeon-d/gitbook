# Transaction

## \[Database] Transaction

### 의미

* 여러개의 SQL문이 하나로 묶여있는 것
* 단일한 논리적인 작업 단위 (a single logical unit of work)
* 논리적인 이유/목적으로 여러 SQL문들을 단일 작업으로 묶어서 나눠질 수 없게 만든 것
* transaction의 SQL문들 중에 일부만 성공해서 DB에 반영되는 일은 없음

### 사용

* `START TRANSACTION;`으로 시작
* `COMMIT;` 으로 종료
  * 지금까지 작업한 내용을 DB에 영구적으로(permanently) 저장
  * transaction 종료
* `ROLLBACK;` 으로 tansaction 이전 상태로 돌아감

### 예제

*   Transaction : J는 100만원을 갖고 있고, H는 200만원을 갖고 있음. J가 H에게 20만원을 계좌이체함

    ```SQL
    START TRANSACTION;
    UPDATE account SET balance = balance - 200000 WHERE id = 'J';
    UPDATE account SET balance = balance + 200000 WHERE id = 'H';
    COMMIT;
    ```
*   Rollback : J는 100만원을 갖고 있고, H는 200만원을 갖고 있음. J가 H에게 30만원을 계좌이체하려 함. J에서 출금했는데 H가 입금받지 못하는 상태(에러)가 발생함

    ```SQL
    START TRANSACTION;
    UPDATE account SET balance = balance - 300000 WHERE id = 'J';
    ROLLBACK;
    ```

### AUTOCOMMIT

* 각각의 SQL문을 자동으로 transaction처리 해주는 개념
* SQL문이 성공적으로 실행하면 자동으로 commit 함
* 실행 중에 문제가 있었다면 알아서 rollback함
* MySQL에서는 default로 autocommit이 enabled 되어 있으며, 다른 DBMS에서도 대부분 같은 기능을 제공함
* autocommit의 설정을 세팅하여 사용할 수 있음
* `SELECT @@AUTOCOMMIT;`
  * 현재 autocommit이 사용중인지 확인하는 쿼리
  * 1이 반환되면 true(활성화), 0이 반환되면 false
  * `SET autocommit=0;` 을 통해 끌 수 있음
  * 단, `START TRANSACTION;`으로 트랜젝션을 시작하면, `AUTOCOMMIT`의 영향을 받지 않음. COMMIT / ROLLBACK과 함께 transaction이 종료되면 원래 autocommit상태로 돌아감

### 일반적인 transaction 사용 패턴

1. transaction을 시작(begin)함
2. 데이터를 읽거나 쓰는 등의 SQL문들을 포함해서 로직을 수행함
3. 일련의 과정들이 문제없이 동작했다면 transaction을 commit 함
4. 중간에 문제가 발생했다면 transaction을 rollback함

#### java 코드 예시

![java\_code](images/14\_javacode.png)

\
\


## ACID : 트랜젝션의 속성

> Atomicity\
> Consistency\
> Isolation\
> Durability

### Atomicity (원자성)

* 묶여있는 것들이 모두 성공하거나 아무것도 실행되지 않음 (All or NOTHING)
* transaction은 논리적으로 쪼개질 수 없는 작업 단위이기 때문에 내부의 SQL문들이 모두 성공해야 함
* 중간에 SQL문이 실패하면 지금까지의 작업을 모두 취소하여 아무 일도 없었던 것처럼 rollback함
* commit 실행 시 DB에 영구적으로 저장하거나 rollback 실행 시 이전 상태로 되돌리는 것도 DBMS가 담당함
* 개발자는 언제 commit 할지, 언제 rollback할지, transaction 단위를 어떻게 구성할지 잘 정해야 함

### Consistency (일관성)

* 데이터베이스의 일관성을 유지시켜주는 것
* 트랜잭션이 테이블에 변경 사항을 적용할 때 미리 정의된, 예측할 수 있는 방식만 취함 (테이블에 이미 정해져있는 constraints 등에 대해 일관성 있게 지킴)
* transaction은 DB 상태를 consistent 상태에서 또 다른 consistent 상태로 바꿈
* constraints, trigger 등을 통해 미리 DB에 정의된 rules을 transaction이 위반했다면 rollback함
* transaction이 DB에 정의된 rule을 위반했는지는 DBMS가 commit 전에 확인하고 알려줌(error, exception throw 등)
* 그 외에 application 관점에서 transaction이 consistent하게 동작하는지는 개발자가 챙겨야 함(DBMS는 DB에 정의된 consistence만 챙김)

### Isolation (격리, 분리)

* 트랜잭션과 트랜잭션은 서로 영향을 주지 않아야 함
* 여러 트랜잭션이 동시에 실행되면 문제가 생길 수 있음 (race condition 문제가 생길 수 있음)
* 여러 transaction들이 동시에 실행될 때도 혼자 실행되는 것처럼 동작하게 만듦
* 모든 트랜잭션이 엄격하게 동기적으로 실행되면 전체 속도에 영향을 줄 수 있음
* 그래서 DBMS는 여러 종류의 isolation level을 제공함 (엄격함의 정도가 다름)
  * 레벨이 높으면 엄격, 동시성 낮아짐, race condition확률 낮아짐, 하지만 속도는 느려짐
  * 레벨이 낮으면 덜 엄격, 동시성 높아짐, race condition확률 높아짐, 하지만 속도는 비교적 빠름
  * 상황에 따라 적절한 isolation level을 설정해야 함
* concurrency control의 주된 목표가 isolation임

### Durability (영존성)

* commit이 된 트랜잭션은 DB에 영구적으로 저장됨
* DB system에 문제가(power fail or DB crash 등으로 인한 DB서버 죽음) 생겨도 commit된 transaction은 DB에 남아있음
* `영구적`이라는 말은 일반적으로 `비휘발성 메모리(HDD, SSD)`에 저장함을 의미함
* 기본적으로 트랜잭션의 durability는 DBMS가 보장함

## 참고사항

1. transaction을 어떻게 정의해서 쓸지는 개발자가 정함. 구현하려는 기능과 ACID 속성을 잘 이해해야 트랜잭션을 잘 정의할 수 있음
2. transaction의 ACID는 DBMS가 알아서 다 해주는 것은 아님. 디폴트 설정돼있는 것도 있지만 개발자가 잘 챙겨야 함

\
\


## 참고

* [유튜브 쉬운코드](https://youtu.be/sLJ8ypeHGlM)
* [data brick](https://www.databricks.com/kr/glossary/acid-transactions)
