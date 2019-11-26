# how to read spring properties

## 1. PropertyPlaceholderConfigurer

1. xml
```xml
<!-- 한개 -->
<beans:bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <beans:property name="location" value="/WEB-INF/config/config.properties"/>
    <beans:property name="fileEncoding" value="UTF-8" />
</beans:bean>
<!-- 여러개 -->
<beans:bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <beans:property name="locations">
        <beans:list>
            <beans:value>classpath:/config/config.properties</beans:value>
            <beans:value>classpath:/config/config2.properties</beans:value>
        </beans:list>
    </beans:property>
</beans:bean>
...
<beans:bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <beans:property name="driverClassName" value="${db.driver}"/>
    <beans:property name="url" value="${db.url}">
    <beans:property name="username" value="${db.username}"/>
    <beans:property name="password" value="${db.password}"/>
</beans:bean>
```

2. java Source

@value 어노테이션 선언으로 가져올 수 있습니다.

```java
@Controller
public class HomeController {
    @Value("${file.path}")
    private String file_path;

    @Value("${db.username}")
    private String dbUser;
}
```

## 2. ResourceLoader

Spring에서 classpath에 있는 여러가지 resource 처리를 위해 Resource라는 인터페이스 구현체인 ClassPathResource를 제공합니다.


### ResourceLoader에서 File로 위임되었을때 생기는 이슈

해당 기능으로 파일객체로 반환을 하여 여러 처리를 하는데, boot로 넘어와서 war로 빌드하고 tomcat배포가 되면 부담없이 File객체로 처리 할 수가 있습니다.
하지만, executable jar로 실행 할때 아래 코드는 문제가 생깁니다.

```java
ClassPathResource classPathResource = new ClassPathResource("static/something.txt");
System.out.println("url = " + classPathResource.getURL());
boolean exists = classPathResource.exists();
System.out.println("exists = " + exists);
File file = classPathResource.getFile();
System.out.println("file = " + file); // 에러 발생
```

.getURL()이나 exists() 기능은 classLoader가 처리하기 때문에 문제가 없지만, getFile()은 문제가 다릅니다.
아래는 getFile 구현 코드입니다.

```java
public abstract class AbstractFileResolvingResource extends AbstractResource {
...
    @Override
    public File getFile() throws IOException {
        URL url = getURL();
        if (url.getProtocol().startsWith(ResourceUtils.URL_PROTOCOL_VFS)) {
            return VfsResourceDelegate.getResource(url).getFile();
        }
        return ResourceUtils.getFile(url, getDescription());
    }
}
```

War파일이나 IDE로 application run as로 실행하였다면 실제 resource 파일인 file:// 프로토콜을 쓰기 때문에 File객체를 생성해 줄 수 있지만,
executable jar로 실행 했다면 FileNotFoundException이 발생하게 됩니다.

```java
public abstract class ResourceUtils {
....
    public static File getFile(URL resourceUrl, String description) throws FileNotFoundException {
        Assert.notNull(resourceUrl, "Resource URL must not be null");
        if (!URL_PROTOCOL_FILE.equals(resourceUrl.getProtocol())) {
            throw new FileNotFoundException(
                description + " cannot be resolved to absolute file path " +
                    "because it does not reside in the file system: " + resourceUrl);
        }
        try {
            return new File(toURI(resourceUrl).getSchemeSpecificPart());
        }
        catch (URISyntaxException ex) {
            // Fallback for URLs that are not valid URIs (should hardly ever happen).
            return new File(resourceUrl.getFile());
        }
    }
}
```

excutable jar는 jar파일 하나에 컴파일된 class file, resource와 nested jar들로 구성되어 있습니다.
여기서 resource의 URL은 jar://로 시작하는 주소를 가지게 됩니다. 해당 프로토콜로 classloader가 원만한 작업을 할 수 있습니다.
하지만 getFile을 실제 fileSystem에서 지원하는 프로토콜로 처리를 해야 합니다. 그래서 jar 실행 환경에서는 사용할 수 없습니다.
해결방법은 getInputStream으로 classloader에 위임처리를 해야합니다.
그리고 실제 File객체가 필요하다면 복사를 해서 처리하는 방법도 있습니다.

```java
File somethingFile = File.createTempFile("test", ".txt");
try (InputStream inputStream = classPathResource.getInputStream()) {
    FileUtils.copyInputStreamToFile(inputStream, somethingFile);
}
```


## 참고

* https://ktko.tistory.com/entry/Spring-properties-%EC%9D%BD%EC%96%B4%EC%98%A4%EA%B8%B0
* https://sonegy.wordpress.com/2015/07/23/spring-boot-executable-jar%EC%97%90%EC%84%9C-file-resource-%EC%B2%98%EB%A6%AC/
