---
tags : [Study]
title: Locking
created: 2020-04-16 20:01:59
modified: 2020-04-16 20:01:59
---

# Locking

## Transaction이란?

데이터베이스의 데이터를 조작하는 작업의 단위(unit of work)이다.
가장 많이 드는 예시는 은행에서의 송금.
1. 보내는 사람의 계좌에서 돈을 빼고
2. 받는 사람의 계좌에 돈을 추가하는 두 가지 행위가 묶인 한 작업

transaction은 흔히 이론적으로 ACID원칙을 보장해야 한다고 한다.

### ACID

* Atomicity(원자성)
  * transaction의 작업이 부분적으로 성공하는 일이 없도록 보장하는 성질
  * 송금하는 사람의 계좌에서 돈은 빠져나갔는데 받는 사람의 계좌에 돈이 들어오지 않는 일은 없어야 한다.
* Consistency(일관성)
  * transaction이 끝날 때 DB의 여러 제약조건에 맞는 상태를 보장하는 성질
  * 송금하는 사람의 계좌 잔고가 0보다 작아지면 안된다
    * 제약 조건 중 하나로 보여진다.
* isolation(독립성)
  * transaction이 진행되는 중간 상태의 데이터를 다른 transaction이 볼 수 없도록 보장하는 성질
  * 송금하는 사람의 계좌에서 돈은 빠져나갔는데 받는 사람의 계좌에 돈이 아직 들어가지 않은 DB상황을 다른 transaction에서 읽으면 안된다.
* Durability(영구성)
  * transaction이 성공했을 경우 해당 결과가 영구적으로 적용됨을 보장받는 성질이다.
  * 한번 송금이 성공하면 은행 시스템에 장애가 발생하더라도 송금이 성공한 상태로 복구할 수 있어야 한다.

### ACID 원칙은 완벽히 지켜지지 않는다. - Transaction의 isolation level

실제로는 **ACID 원칙은 종종 지켜지지 않는다.**
-> **CID 원칙을 strict하게 지키려면 동시성이 매우 떨어진다.**
그렇기 때문에 DB 엔진은 ACID 원칙을 희생하여 동시성을 얻을 수 있는 방법을 제공한다.
**transaction의 isolaction**이다.
isolation 원칙을 덜 지키는 level을 사용할수록 문제가 발생할 가능성은 커지지만 동시에 더 높은 동시성을 얻을 수 있다.

DB엔진은 isolation level에 따라 서로 다른 locking전략을 취한다. 요컨대, **isolation level이 높아질수록 더 많이, 더 빡빡하게 lock을 거는 것**이다.
따라서 각각의 isolation level을 언제 사용해야 하는지, 혹은 isolation level의 위험성은 무엇인지 알기 위해서는 각 isolation level별 locking전략을 파악해야 한다.

### InnoDB의 lock

InnoDB는 transaction의 ACID 원칙과 동시성을 최대한 보장해주기 위해 다양한 종류의 lock을 사용한다.

#### row-level lock

가장 기본적인 lock은 **테이블의 row마다 걸리는 row-level lock**이다.
두가지 종류가 있다.

1. shared lock(S lock)
   1. **read에 대한 lock**
   2. 일반적인 `SELECT`쿼리는 lock을 사용하지 않고 DB를 읽어들인다.
   3. 하지만, 일부 `SELECT` 쿼리는 read 작업을 수행할 때 InnoDB가 각 row에 S lock을 건다.
2. exclusive lock(X lock)
   1. **write에 대한 lock**
   2. `SELECT ... FOR UPDATE`나 `UPDATE`, `DELETE` 등의 수정 쿼리를 날릴 때 각 row에 걸리는 lock이다.

##### S lock과 X lock을 거는 규칙
* 여러 transaction이 동시에 한 row에 S lock을 걸 수 있다.
  * 즉, 여러 transaction이 동시에 한 row를 읽을 수 있다.
* S lock이 걸려있는 row에 다른 transaction이 X lock을 걸 수 없다.
  * 즉, 다른 transaction이 읽고 있는 row를 수정하거나 삭제할 수 없다.
* X lock이 걸려있는 row에는 다른 transaction이 S lock과 X lock 둘 다 걸수 없다.
  * 즉, 다른 transaction이 수정하거나 삭제하고 있는 row는 읽기, 수정, 삭제가 전부 불가능하다.

요약하자면, **S lock을 사용하는 쿼리끼리는 같은 row에 접근 가능, 반면 X lock이 걸린 row는 다른 어떠한 쿼리도 접근 불가능**하다.

#### Record lock

Record lock은 row가 아니라 DB의 index record에 걸리는 lock이다. row-level lock과 마찬가지로 S lock과 X lock이 있다.

##### 예시

`c1`이라는 column을 가진 테이블 `t`가 있다고 하자.
이때 한 transaction에서

```sql
(Query 1 in transaction A)
SELECT c1 from t where c1 = 10 FOR UPDATE;
```
라는 쿼리를 실행했다. 그러면 `t.c1`의 값이 10인 index에 X lock이 걸린다.
이때, 다른 transaction에서

```sql
(Query 2 in transaction B)
DELETE FROM t where c1 = 10;
```
이라는 쿼리를 실행하려고 하면, 이 query 2는 우선 `t.c1 = 10`인 index record에 X lock을 걸려고 시도한다.
하지만 해당 index record에는 이미 transaction A가 query1을 실행할 때 X lock을 건 상태이다.
따라서 query2는 transaction A가 commit 되거나 rollback 되기 전까지 `t.c1 = 10`인 row를 삭제할 수 없다.
이는 `DELETE` 뿐만 아니라 `INSERT`나 `UPDATE` 쿼리도 마찬가지이다.

#### Gap lock

DB index record의 gap에 걸리는 lock이다.
여기서 gap이란 index 중 DB에 실제 record가 없는 부분이다.

##### 예시

`id` column만 있는 테이블이 있고, `id` column에 index가 걸려있다고 하자. 현재 테이블에는 `id = 3`인 row와 `id = 7`인 row가 있다. 그러면 DB와 index table은 아래 그림과 같다.

그림은 블로그글 참조하자.

그러면 현재 `id <= 2`, `4 <= id < 6`, `8 <= id`에 해당하는 부분에는 index record가 없다. 이 부분이 바로 index record의 gap이다.

그리고 gap lock은 이러한 gap에 걸리는 lock이다. 즉, gap lock은 해당 gap에 접근하려는 다른 쿼리의 접근을 막는다. record lock이 해당 index를 타려는 다른 쿼리의 접근을 막는 것과 동일하다.
**둘의 차이점이라면 record lock이 이미 존재하는 row가 변경되지 않도록 보호**하는 반면, **gap lock은 조건에 해당하는 새로운 row가 추가되는 것을 방지하기 위함**이다.

### Lock이 해제되는 타이밍

Transaction이 진행되는 동안, innoDB 엔진은 위에서 언급한것처럼 실행되는 쿼리에 맞는 수많은 lock을 DB에 걸게 된다. 이러한 **lock은 모두 transaction이 commit되거나 rollback 될 때 함께 unlock**된다.

### Transaction Isolation Level

#### Consistent Read

**read(=`SELECT`) operation을 수행할 때 현재 DB의 값이 아닌 특정 시점의 DB snapshot을 읽어오는 것**이다. 물론 이 snapshot은 commit된 변화만이 적용된 상태.

Consistent read는 어떤 방법을 통해 이루어질까? 가장 단순한 방법은 읽어온 row에 lock을 걸어 다른 transaction이 할 수 없도록 하는 방법일 것이다. 하지만 innoDB엔진은 consistent read를 하기 위해 lock을 사용하지 않는다. 왜냐하면 동시성이 매우 떨어지기 때문이다.

innoDB 엔진은 실행했던 쿼리의 log를 통해 consistent read를 지원한다. **innoDB 엔진은 각 쿼리를 실행할 때마다 실행한 쿼리의 log를 차곡차곡 저장한다. 그리고 나중에 consistent read를 할 때 이 log를 통해 특정 시점의 DB snapshot을 복구하여 가져온다.** 이 방식은 비록 복구하는 비용이 발생하지만, lock을 활용하는 방식보다는 높은 동시성을 얻을 수 있다.

#### REPEATABLE READ

반복해서 read operation을 수행하더라도 읽어 들이는 값이 변화하지 않는 정도의 isolation을 보장하는 level이다.

`REPEATABLE READ` transaction은 처음으로 read operation을 수행한 시간을 기록한다. 그리고 그 이후에 모든 read operation마다 해당 시점을 기준으로 consistent read를 수행한다.
그러므로 transaction 도중 다른 transaction이 commit이 되더라도 새로이 commit된 데이터는 보이지 않는다. 첫 read시의 snapshot을 보기 때문이다.

일반적인 non-locking `SELECT` 외에 lock을 사용하는 `SELECT`나 `UPDATE`, `DELETE` 쿼리를 실행할 때, `REPEATABLE READ` transaction은 gap lock을 활용한다. 즉, 내가 조작을 가하려고 하는 row의 *후보군*을 다른 transaction이 건들지 못하도록 한다. 여기에 대해서는 아래의 `REPEATABLE READ` vs `READ COMMITTED` 항목에서 보자.






## 참고
* https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/
