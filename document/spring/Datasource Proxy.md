# Datasource Proxy

## Hibernate의 문제점

Hibernate를 사용하다보면 몇가지 불편사항이 있다.

* Native Query를 사용하지 않으면 (JPA)쿼리 실행 계획이 어떻게 되는지 알 수 없다.
* Hibernate debug log를 이용하면 Bulk insert 여부를 알 수 없다.
* Hibernate debug log를 이용하면 Binding variable 파악을 위한 불편이 생긴다.
* Query의 execute 결과를 계획하기 어렵다.

위 내용은 단편적 혹은 극단적으로 작성한 사항일 수 있지만, 비즈니스 로직을 작성하다보면 위 같은 사항들이 엄청 불편하다.

Binding variable의 문제는
```SQL
INSERT INTO TBL_TEST VALUES (?, ?, ?)
```

```
logging:
  level:
    org:
      hibernate:
        type:
          descriptor:
            sql: trace
```
위 옵션으로 해결하거나..

properties나 log4j configuration class를 바꾸거나 하는 방법이 있지만, 위 단점들을 해결할 수 있는 방법이 있다.

## Datasource Proxy란

Java에서 DB에 접근하는 과정은 다음과 같다.

Application -> Datasource -> JDBC Driver -> Physical DB

풀이하면,
Application에서는
DataSourc로부터 Connection 정보를 설정하고
DB Connectivity Driver를 이용해
Physical DB에 연결한다.

이 DataSource와 Application 사이에 Proxy를 두는것이다.

연결을 가로채어 Persistence와 Gathering 등 Database 와의 일련의 모든 로직들을 까볼 수 있다.

Application -> **DataSourceProxy** -> Datasource -> JDBC Driver -> Physical DB

## DataSourceProxy 설정방법

DataSourceProxy는 Spring내에 기본적으로 탑재돼있지 않기 때문에 별도의 의존성이 필요하다.
여기서는 ttddyy님의 [datasource-proxy](https://github.com/ttddyy/datasource-proxy)를  사용합니다.

의존성은 README.md를 참고하여 설정하면 됩니다.

이후 Datasource를 Bean을 설정하는 부분을 DataSourceProxy 객체를 반환하도록 작성해야 합니다.

```java
// proxy 사용전
@Bean
public DataSource dataSource() {
    DataSource dataSource = DataSourceBuilder.create()
    .url(...)
    .username(...)
    .password(...)
    .build();

    return dataSource;
}

// proxy 사용
@Bean
public DataSource dataSource() {
    DataSource dataSource = DataSourceBuilder.create()
    .url(...)
    .username(...)
    .password(...)
    .build();

    DataSourceProxy dataSourceProxy = ProxyDataSourceBuilder
    .create(dataSource)
    .name("dataSource")
    .logQueryToSysOut() // System.out으로 수행된 query를 출력
    .asJson() // 위 출력될 query 데이터의 형식을 json형식으로 지정
    .countQuery() // 실행된 query의 count를 보여준다.
    .afterQuery((execInfo, queryInfoList) -> {
        System.out.println("Elapsed time : " + execInfo.getElapsedTime() + " ms\n");
    }) // query가 실행된 이후의 동작을 지정한다. java8의 Consumer를 받는다. Consume을 위해 제공되는 객체는 ExecInfo와 QueryInfoList 2개이다. 보통 query elasped time을 찍는다.
    .build()

    return dataSourceProxy;
}
```

## 주의사항

DataSourceProxy을 이용한 콘셉트는 DataSource위에 추상계층이 있는 것이다.
따라서 ORM를 구현한 라이브러리를 이용할때는 해당 로그를 꺼야, 이중으로 로그가 나오는 것을 막는다.
예를들어, Hibernate라면 application properties에서
```
show_sql: true
use_sql_comments: true
format_sql: true
```
위 3줄을 주석처리하거나 지워주자.

## 참고

* https://myoa-engineering.com/techlog/archives/757
