# Spring Flyway

DB이력을 형상관리하는 오픈소스 툴.

버전별 DB 스크립트 파일은 아마 다음과 같이 스크립트 파일을 쭉 열거하여 관리하고 일일이 파일을 DBMS에서 실행했을것이다.
* 스크립트 파일들이 쭉 있고, 해당 파일을 DBMS에 가서 스크립트를 실행하는 방식.

스크립트 파일을 VCS로 형상관리를 해도 되지만, 자칫 사람의 실수가 발생할 수 있다. Appication은 버전 상향이 되었는데, 변경된 DB 스크립트가 실행되지 않아, 장애로 이어질 수도 있다.

버전별 스키마 변경 관리는? Application 구동을 위한 Provisioning data관리는? 일일이 직접 기억하여 실행을 해줘야 하나?
-> Flyway 오픈소스 DB 형상 관리 도구가 있다.

로컬, 알파 등 개발 DB에서 변경한 schema, index, key등을 베타, 운영 DB에 누락되는 것을 flyway를 사용하여 방지할 수 있습니다.
또한 단위 테스트에서도 in-memory DB(H2, derby, Hsqldb 등)에 DB DDL 이력을 실행하여 원격과 같은 DB 형상을 유지한 채, 단위 테스트를 할 수 있습니다.

## Flyway 시작하기

위 파일을 V + 버전 + __ + 설명.sql 규칙으로 파일명을 작성한다 (ex: V1.0__INIT.sql)
그럼 Flyway plugin이 flyway_schema_history 내부 스키마를 이용하여 파일 버전을 관리합니다.

버전 2에서 2.1로 버전이 상향되면서 2개의 테이블이 삭제될 경우, V2.1__refactoring.sql파일에 DROP TABLE .. 을 기술한다.

Flyway Plugin은 내부 스키마의 등록된 정보를 기반으로 버전 순서대로 파일 내용을 실행합니다.
가령, version 2는 이미 등록된 정보이고 version 2.1은 신규로 등록된 row의 경우, 해당 스크립트 파일을 실행하여 버전별 이력을 관리하게 됩니다. **이미 row에 등록된 스크립트 버전은 중복으로 실행되지 않습니다.**

여기서 주의할 점은 Flyway로 관리될 SQL 파일은 ***src/main/resources/db/migration*** 위치에 저장되어야 Flyway plugin이 인식할 수 있고, Prefix V는 반드시 대문자로 작성되어야 합니다.

## 실행

설정은 참고 사이트 참조

Application main() 메소드 아래에 아래 코드를 추가

```java
Flyway flyway = Flyway.configure().dataSource("DB_URL", "User", 'Password').load();
flyway.migrate();
```

## 참고

* https://meetup.toast.com/posts/173
