---
tags : [Study]
title: IsolationLevel
created: 2020-04-16 19:25:47
modified: 2020-04-16 19:25:47
---

# IsolatioenLevel

트랜잭션 격리수준(isolation level)이란 동시에 여러 트랜잭션이 처리될 때, 트랜잭션끼리 얼마나 서로 고립되어 있는지를 나타내는 것이다.

level은 크게 아래의 4가지로 나뉜다.
* READ UNCOMMITTED
* READ COMMITTED
* REPEATABLE READ
* SERIALIZABLE

아래로 내려갈수록 트랜잭션간 고립 정도가 높아지며, 성능이 떨어지는 것이 일반적이다.
(oracle = READ COMMITTED, mysql = REPEATABLE READ)

## READ UNCOMMITTED

어떤 트랜잭션의 변경내용이 COMMMIT이나 ROLLBACK과 상관없이 다른 트랜잭션에서 보여진다.
이 격리 수준에서는 데이터 정합성에 문제가 많으므로, RDBMS 표준에서는 격리수준으로 인정하지 않는다.

## READ COMMITTED

어떤 트랜잭션의 변경 내용이 COMMIT 되어야만 다른 트랜잭션에서 조회할 수 있다.
오라클 RDBMS에서 기본으로 사용하고 있고, 온라인 서비스에서 가장 많이 선택되는 격리수준이다.

이 수준에서도 정합성 문제가 해결되는것으로 보이지만, `NON-REPETABLE READ` 부정합 문제가 발생할 수 있다.

1. B 트랜잭션에서 10번 사원의 나이를 조회
2. 27살이 조회
3. A 트랜잭션에서 10번 사원의 나이를 27살에서 28살로 바꾸고 `커밋`
4. B 트랜잭션에서 10번 사원의 나이를 다시 조회(변경되지 않은 이름이 조회됨)
5. 28살이 조회됨

이는 하나의 트랜잭션내에서 똑같은 SELECT를 수행했을 경우 항상 같은 결과를 반환해야 한다는 REPEATABLE READ 정합성에 어긋나는 것이다.
일반적인 웹 어플리케이션에서는 크게 문제가 되지 않지만, 작업이 금전적인 처리와 연결되어 있다면 문제가 발생할 수 있다.

## REPETABLE READ

트랜잭션이 시작되기 전에 커밋된 내용에 대해서만 조회할 수 있는 격리수준
mysql에서 기본으로 사용, 이 격리수준에서는 `NON-REPETABLE READ` 부정합이 발생하지 않는다.

자신의 트랜잭션 번호보다 낮은 트랜잭션 번호에서 변경된(커밋된) 것만 보게 되는 것이다.
(모든 InnoDB의 트랜잭션은 고유한 트랜잭션 번호(순차적으로 증가)를 가지고 있으며, 언두 영역에 백업된 모든 레코드는 변경을 발생시킨 트랜잭션의 번호가 포함되어 있다.)

>> REPETABLE READ 격리수준에서는 트랜잭션이 시작된 시점의 데이터를 일관되게 보여주는 것을 보장해야 하기 때문에 한 트랜잭션의 실행시간이 길어질수록 해당 시간만큼 계속 멀티 버전을 관리해야하는 단점이 있다.
하지만 실제로 영향을 미칠 정도로 오래 지속되는 경우는 없어서 READ COMMITTED와 REPETABLE READ의 성능차이는 거의 없다고 한다.

### REPETABLE READ에서 발생할 수 있는 데이터 부정합

1. UPDATE 부정합
2. Phantom READ

## 참고
* https://joont92.github.io/db/%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EA%B2%A9%EB%A6%AC-%EC%88%98%EC%A4%80-isolation-level/
* https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation
