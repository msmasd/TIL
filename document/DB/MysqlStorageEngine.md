---
tags : [Study]
title: MysqlStorageEngine
created: 2020-04-16 19:52:03
modified: 2020-04-16 19:52:03
---

# MysqlStorageEngine

Mysql은 크게 아래의 2가지 구조로 되어있다.
* 서버엔진: 클라이언트(또는 사용자)가 Query를 요청했을때, Query Parsing과 스토리지 엔진에 데이터를 요청하는 작업을 수행
* 스토리지 엔진: 물리적 저장장치에서 데이터를 읽어오는 역활을 담당.

스토리지 엔진은 데이터를 직접적으로 다루는 역활을 하므로 엔진 종류마다 동작원리가 다르기 때문에 트랜잭션, 성능과 같은 주요 이슈에도 밀접하게 연관되어 있다.

Mysql의 스토리지 엔진은 *Plug in* 방식이며, 기본적으로 8가지 스토리지 엔진이 탑제되어 있다.

* 'CREATE TABLE' 문을 사용하여 테이블을 생성할 때, 맨 마지막 구문에 스토리지 엔진의 이름을 추가함으로 아주 간단하게 설정 할 수 있다.

```SQL
- ENGINE=INNODB not needed unless you have set a different
-- default storage engine.
CREATE TABLE t1 (i INT) ENGINE = INNODB;
-- Simple table definitions can be switched from one to another.
CREATE TABLE t2 (i INT) ENGINE = CSV;
CREATE TABLE t3 (i INT) ENGINE = MEMORY;
```

가장 많이 쓰인다고 알려진 스토리지 엔진인 MyISAM, InnoDB의 엔진 정리

* InnoDB
  * 따로 스토리지 엔진을 명시하지 않으면 default로 설정되는 스토리지 엔진
  * transaction-safe하며, 커밋과 롤백, 그리고 데이터 복구 기능을 제공하므로 데이터를 효과적으로 보호할 수 있다.
  * row-level locking제공
  * 데이터를 clustered index에 저장하여 PK기반의 query의 I/O 비용을 줄인다.
  * FK제약을 제공하여 데이터 무결성을 보장
* MyISAM
  * 트랜잭션을 지원하지 않고 table-level locking을 제공한다.
  * multi-thread 환경에서 성능이 저하 될 수 있다.
    * 특정 세션이 테이블을 변경하는 동안 테이블 단위로 lock이 잡히기 때문

## InnoDB vs MyISAM 성능 비교
각 목적에 맞도록 잘 사용해야 한다.
10만건당, 테이블에 밀어넣는(Batch Insert) 작업이 InnoDB는 10초, MyISAM은 7초걸림

* 순수 데이터를 입력하는 속도는 MyISAM이 InnoDB보다 약 30% 빠르다
* 카운트 함수(집계)등을 사용할 때는 MyISAM이 InnoDB보다 빠르다
* Order by가 들어가면, InnoDB가 압도적으로 빠르다.

### 각 엔진마다 유리한 경우
* InnoDB가 유리한 경우
  * 대용량의 데이터를 컨트롤 하는 경우
  * 트랜잭션 관리가 필요한 경우
  * 복구가 필요한 경우
  * 정렬등의 구문이 들어가는 경우
  * IUD 등이 빈번하게 발생하는 경우(Row-Level locking)
* MyISAM이 유리한 경우
  * 읽기 위주의 작업만 필요한 경우
  * 전문 검색이 필요한 경우
  * 트랜잭션이나 복구등이 필요 없을 경우
  * 한번에 대량의 데이터를 입력하는 배치성 테이블

## 참고
* http://asuraiv.blogspot.com/2017/07/mysql-storage-engine.html
* https://needjarvis.tistory.com/45
