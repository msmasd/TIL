# Hibernate ddl-auto

spring boot에 hibernate, jpa를 설정할 때, spring.hibernate.ddl-auto 라는 옵션이 있다.
java에서 @Entity를 선언한 entity들이, WebApplication이 올라갈 때(정확히는 hibernate의 sessionFactory가 올라갈 때)DataSource에 접근을 해서 DBMS의 스키마를 자동으로 alter 하거나 지우고 다시 만들거나 검증하거나 하는 역활을 한다.

* none: 아무것도 실행하지 않는다(대부분의 DB에서 기본값이다.)
* create: SessionFactory가 시작될 때 데이터베이스 drop을 실행하고 생성된 DDL을 실행한다.
* create-drop: SessionFactory가 시작될 때 데이터베이스 drop을 실행하고 생성된 DDL을 실행하고, SessionFactory가 종료될 때 drop을 실행한다.(in-memory DB의 경우 기본값이다)
* update: SessionFactory가 올라갈 때, Object를 검사하여 테이블을 alter 시킨다. 데이터는 유지
* validate: 변경된 스키마가 있으면 변경점을 출력하고 애플리케이션을 종료한다.

## 참고

* https://hakurei.tistory.com/110
* https://pravusid.kr/java/2018/10/10/spring-database-initialization.html
