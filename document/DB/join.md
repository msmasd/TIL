# join

둘 이상의 테이블을 연결하여 데이터를 검색하는 방법.

## 1. 종류

### 1.1 INNER JOIN

* 둘 이상의 테이블 간의 논리적 관계를 기준으로 데이터를 검색하여 결과 집합을 만든다.
* JOIN이라는 키워드만 사용하면 INNER JOIN으로 인식되어 쿼리가 동작한다.
* 결합된 테이블에 조건의 내용이 공통으로 들어가 있는 값을 결과 집합으로 만들어준다.
* ON을 통해 참조된 값을 찾아올 때 쓰인다. FK키로 직접 참조되지 않아도, 같은 값을 갖고 있고, 의미하는게 같다면 조인되어 가져올 수 있다.

### 1.2 OUTER JOIN(LEFT, RIGHT)

* INNER JOIN 문을 포함하고 한쪽에만 내용이 있더라도 지정한 기준 테이블에 있는 모든 데이터를 가져오는 조인방식

### 1.3 CROSS JOIN

* 결과값이 한쪽 테이블의 모든행들과 다른쪽 테이블의 모든 행을 조인시킨다.
* 결과 집합은 두 테이블의 개수를 곱한 값만큼 생성되며, 조인되는 테이블에 공통되는 행이 없어도 되며 조건절인 ON 키워드가 사용되지 않는다.
* ON을 입력하지 않은 JOIN과 같은 결과가 나오지만, CROSS JOIN의 개념은 두개의 테이블의 행을 모두 연결하는 것과 같다.
* 즉, CROSS JOIN은 첫 번째 테이블의 각 행과 두 번째 테이블의 각 행을 결합하는 행을 생성하는 것이다.
* 반대로, JOIN의 결과는 테이블의 모든 레코드의 카디전곱 후 테이블 A의 모든 레코드와 테이블 B의 모든 레코드를 결합한 결과로 정의 할 수 있습니다.

### 1.4 SELF JOIN

* 하나의 테이블에 같은 데이터가 존재하는데, 그 의미가 다르게 존재하는 경우. 즉, 같은 데이터이지만 다른 열에 있는 경우에는 두 테이블을 서로 SELF JOIN문으로 확인 가능
* 같은 테이블의 같은 값을 가져왔지만, AS로서 명칭을 다르게 가져와 사용할 수 있다.

### 1.5 UNION

* 한쪽의 테이블과 다른 쪽의 테이블의 결과값을 1개의 결과값으로 가져오는 JOIN을 말한다.

## 2. DBMS 조인 처리 방법

### 2.0 용어정리

### 2.0.1 Driving Table

### 2.0.2 Cadinality


### 2.1 Hash Join

* 해시조인(hashjoin)은 두 테이블 중 하나를 기준으로 비트맵 해시 테이블을 메모리에 올린 후 나머지 테이블을 스캔 하면서 해싱 테이블을 적용하여 메모리에 로딩된 테이블과 비교하여 매칭되는 데이터를 추출하는 방식의 조인이다.
* RDBMS에서 비용이 가장 많이 들어가는 JOIN 방법으로 주로 작은 Table과 큰 Table의 Join시 사용되어 지며, Driving 조건과 상관없이 좋은 성능을 발휘할 수 있다.

## 참조

1. [JOIN 종류](https://postitforhooney.tistory.com/entry/DBMARIADB-SQL-%EC%98%88%EC%A0%9C%EB%A5%BC-%ED%86%B5%ED%95%9C-JOIN%EC%9D%98-%EC%A2%85%EB%A5%98-%ED%8C%8C%EC%95%85)
2. [Driving Table](https://brightestbulb.tistory.com/147)
3. [cardinality](https://itholic.github.io/database-cardinality/)
4. [hash join, nested join..](http://ojc.asia/bbs/board.php?bo_table=LecHINT&wr_id=127)
