# 왜 DB index로 B tree 계열이 사용되는가?

<br>

# B tree 계열이란?
- B tree를 기반으로 확장된 버전의 자료구조
- B+ tree, B* tree

<br>

# 시간 복잡도
## B tree의 시간복잡도
- 조회, 삽입, 삭제에 대해서 O(log)N
## Binary Search Tree(BST)와 비교
- Binary Search Tree는 편향되는 구조를 가질수도 있음
- 스스로 균형을 맞추는 self-balancing BST가 있음
    - 예) AVL tree, Red-Black tree
- 조회, 삽입, 삭제에 대해서 O(log)N
> 시간복잡도가 같은데, B tree가 과연 더 좋은 것일까?

<br>

# Secondary storage
## 의미
- 프로그램과 데이터가 영구적으로 저장됨(컴퓨터가 꺼져도 데이터가 남아있음)
- 실행 중인 프로그램의 데이터 일부가 임시 저장되기도 함(swap)
- SSD, HDD 가 이에 해당됨
- database는 여기에 저장됨
## 특징
- 데이터를 처리하는 속도가 가장 느림
    - SSD는 RAM에 비해 10배 느리고, HDD는 100배 느림
- 데이터를 저장하는 용량이 가장 큼 (금액당 용량이 큼)
- block 단위로 데이터를 읽고 씀
    - block : file system이 데이터를 읽고 쓰는 논리적인 단위. 2의 승수로 표현되며, 4KB, 8KB, 16KB 등으로 가져옴
    - 단점 : 불필요한 데이터까지 읽어올 가능성이 있음

<br>

# Secondary storage에 있는 DB에 대한 성능적인 고민
- DB에서 데이터를 조회할 때, secondary storage에 최대한 적게 접근하는 것이 성능 면에서 좋음
- block 단위로 읽고 쓰기 때문에 연관된 데이터를 모아서 저장하면 더 효율적으로 읽고 쓸 수 있음

## B tree index의 장점
- 노드마다 여러개를 갖고 있을 수 있기에 scondary storage에 접근하는 횟수가 더 적음
    - 자녀 노드수가 더 많아서 탐색 범위를 더 빠르게 좁혀나갈 수 있음 (root에서 leaf로 가는 횟수가 더 적음)
- 각 노드의 묶음이 block 단위로 묶여있어서 scondary storage에서 불러올 때 유리할 수 있음 (사용될 데이터가 묶을 뿐 아니라, 불필요한 데이터가 함께 불려질 확률이 더 적음)
- root노드에서 적은 레벨만으로도 더 넓은 범위의 값에 접근할 수 있음 -> DB 조회를 위해서 secondary storage에 접근하는 횟수가 더 적음

<br>

# 참고
[유튜브 : 쉬운코드](https://youtu.be/liPSnc6Wzfk)