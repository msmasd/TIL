# Spring cache

## Spring Cache

Spring caching abstraction을 지원합니다.
이 Spring caching abstraction은 다른 캐시 솔루션을 Spring CacheManager를 통해서 쉽게 사용할 수 있도록 해줍니다.
Spring CacheManager는 다양한 캐시 솔루션들을 코드에 최소 영향으로 다양한 캐싱 솔루션(라이브러리)를 도입할 수 있게 해준다.
Spring caching abstraction은 자바 메소드에 캐싱을 적용한다.
메소드가 실행될 때마다 메소드의 넘어온 파라미터에 따라 캐쉬를 적용하게 된다.

## Spring Cache 다양한 캐시 솔루션 도입방법

Spring 3.1부터 간단하게 캐시를 도입할 수 있게 되었다. Spring에서 제공해주는 CacheManager 인터페이스만 구현체에 따라 구현해주면 된다.
다른 캐시 라이브러리들을 추가해서 구현체에 사용할 수 있다.
Spring Boot에서 캐시를 사용하기위해서는 spring-boot-starter-cache를 추가하는데, 기본적으로 spring-boot-starter-cache 이외에 아무 서드파티 라이브러리를 추가하지 않는다면 기본 설정인 SimpleCacheConfiguration이 동작하며 CacheManager로는 ConcurrentMapCacheManager가 빈으로 등록된다.
ConcurrentMapCacheManager는 가벼워서 많이들 사용하지만 Product용으로는 Ehcache를 사용한다.
Ehcache를 사용하면 좀 더 확장적이고 분산시스템등 커스터마이징을 할 수 있기 때문이다.

## Spring Cache 사용방법

Spring Cache를 사용하기 위해서는 라이브러리를 추가한다.

```gradle
implementation 'org.springframework.boot:spring-boot-starter-cache'
```

위와 같이 gradle에 내용을 추가하고 @EnableCaching을 Application에 선언하게 되면 Spring container에 빈이 등록된다.
설정이 없다면 기본빈은 ConcurrentMapCacheManager가 등록된다.
그후, Cache를 적용하기를 원하는 메소드에 `@Cacheable`를 붙이면 된다.
이 어노테이션 이외에도 몇개의 어노테이션이 있다.

* @Cacheable: 메소드에 캐시 트리거 설정
* @CachePut: 메소드 실행과 방해없이 캐시 갱신
* @CacheEvict: 캐시되있는 데이터 지우기
* @CacheConfig: 캐시 관련 설정
* @EnableCaching: 스프링 캐시 활성화

캐시 어노테이션들의 속성

* value: 캐시의 이름
* key: 캐시할 키를 설정(기본설정은 파라미터로 설정)
* condition: 특정 조건에 따라 캐시를 할지 않할지 결정
* cacheManager 등: 해당 캐시 매니저 설정가능

## 참고

* https://minwan1.github.io/2018/03/18/2018-03-18-Spring-Cache/
