# [Database] Partitioning, Sharding, Replication

# Partitioning

## 의미
- database table을 더 작은 table들로 나누는 것
### vertical partitioning
- column을 기준으로 table을 나누는 방식 (세로로 쪼갬). schema가 쪼개짐
- 정규화(normalizatioin)도 vertical partitioning 중 하나. 중복데이터를 없애주기 위해서 1개 이상의 attribute를 떼서 새로운 table을 만드는 것이기 때문
- 큰 데이터가 attribute에 포함되어 있을 때 vertical partitioning 사용할 수 있음. DBMS에서 SELECT를 수행할 때, HDD/SSD에서 튜플 전체를 메모리로 읽어오고 거기서 SELECT한 attribute만 걸러서 반환해줌. 게시판 목록을 불러오는 쿼리에서 게시판 내용은 포함되지 않아도 되는데, 같은 테이블에 있으면 일단 게시판 내용까지 저장장치에서 불러오기긴 함. 그래서 DB서버가 I/O에 부담을 느낄수도 있음.
- 이미 정규화가 되어 있어도 퍼포먼스(속도)를 위해서 vertical partitioning 가능
- 민감한 정보에는 제한을 더 걸어서 함부로 접근하지 못하게 하는 용도로도 함
- 자주 사용하는 attribute만 모아서 하나의 table로 모으고, 나머지를 하나의 table로 구성하는 경우도 있음

### horizontal partitioning
- row를 기준으로 table을 나누는 방식 (가로로 쪼갬)
- table의 schema는 그대로 유지됨
- table의 튜플수가 어어어엄청 많아질 것으로 예상될 때 사용함. (테이블이 커지면 인덱스의 크기도 커짐. 테이블에 읽기/쓰기가 있을 때마다 인덱스에서 처리되는 시간도 조금씩 늘어남)
- horizontal partitioning을 하는 방법이 여러가지가 있음. 아래는 그 중 hash-based로 이루어짐
	- hash function을 하나 마련함
	- 그 hash function의 input으로 user_id를 넣음
	- 결과 값의 개수는 쪼갤 테이블 개수만큼 있음
	- hash function의 결과 값에 따라 그에 해당되는 table에 저장함
	- 이렇게 되면 같은 user_id의 데이터는 같은 테이블에 저장됨
	- 이때, user_id를 partition key라고 함
- 주의사항
	- partition key가 아닌 값으로 조회하려면 여러 테이블을 다 접근해야함 -> 가장 많이 사용될 패턴에 따라 partition key를 정하는 것이 중요
	- 데이터가 균등하게 분배될 수 있도록 hash function을 잘 정의해야 함
	- 한번 partition이 나눠져서 사용하면, 이후에 partition을 추가하기 까다로움 (이미 서비스가 시작 됐다면) -> 처음 설계할 때 partitioning을 잘 해야 함

<br><br>

# Sharding
## 의미
- horizontal partitioning처럼 동작함
- row를 기준으로 table을 나눔
- 나누어진 각 table(partition)이 서로 다른 독립된 DB 서버에 저장됨
- horizontal partitioning하면, 테이블을 달라도 결국 그것을 처리하는 H/W는 동일함. 부하를 줄여주기 위해서 table을 쪼개고 쪼개진 table을 서로 다른 머신에 저장하고, 각 요청을 각 머신이 처리하게 하는 것이 sharding
- DB서버의 부하(load)를 분산시키는 목적을 가짐
- partition key를 shard key라고 부름
- 각 partition을 shard라고 부름

<br><br>

# Replication
## 의미
- DB서버에 문제가 생겨도 동작할 수 있게 해주는 방법 중 하나
- application server에서 read/write를 하는 DB와 똑같이 DB를 다른 머신에 복사하는 것. 그리고 계속해서 sync를 맞춤
- 주 DB서버가 있고, 계속 copy를 해서 보조 서버가 있는 방식. 보조 서버는 여러대가 있어도 됨
- 주 DB 서버 : master, primary, leader
- 보조 DB 서버 : slave, secondary, replica
## 장점
- High availability(HA. 고가용성) : 주 DB 서버에 문제가 생겨서 application의 요청을 처리 못할 때, 보조 서버가 그 요청을 받아줄 수 있음(fail over) -> 서비스 타격 최소화
- read 트래픽이 많을 때, 그 일부를 보조 DB서버에 분산 시켜줄 수 있음
