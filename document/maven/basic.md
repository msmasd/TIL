# maven 기본 정리

Maven은 자바 프로젝트의 빌드(build)를 자동화 해주는 빌드 툴(build tool)이다.
즉, 자바 소스를 compile하고 package해서 deploy하는 일을 자동화 해주는 것이다.

## Maven 기능

1. 빌드
2. 문서화
3. 리포팅
4. 의존관계 관리
5. 소스코드 관리
6. 릴리즈
7. 배포

## Maven 장점

1. 편리한 의존관계 라이브러리 관리
2. 모든 프로젝트가 일관된 디렉토리 구조와 빌드 프로세스를 유지
3. 메이븐이 제공하는 다양한 플러그인을 활용
   1. 플러그인을 이용하여 다양한 통합개발 환경, 프로젝트 자동변환, 데이터베이스 통합 등 가능
4. 전시적으로 사용할 프로젝트 템플릿을 만들어 배포

## Maven Patrern

1. Build: Source/Test Code Compile
2. Package: WAR/JAR ETC package
3. Test: Unit Test, Test Result Check
4. Report: Build, Package, Test 결과 정리 및 문서화(Report 생성)
5. Release: Build 후 생성된 결과물(Artifact)를 local / Remote 환경(저장소)에 배포

## Maven Key Concept / Keyword

1. Plugin: Goal의 집합(Goals) / 메이븐에서 실질적인 실행을 담당
   1. Goal은 ant의 target과 같은 개념
   2. Phase를 실행하면 해당 Phase와 연결된 플러그인 Goal이 실행된다.
2. LifeCycle: 기본 라이프사이클(빌드 결과물 생성) / Clean 라이프사이클(빌드된 결과물 제거) / Site 라이프사이클(java document 결과물 생성)
3. Dependency: 라이브러리 의존성 관리(의존성 전의/ 스코프 등)
4. Profile: 배포 환경 정의(환경에 따른 배포 내용 정의 가능)
5. POM(Project Object Model)
6. Archetype: Template Project를 지칭
7. Phase: 라이프사이클의 각 단계를 지칭(compile, test, package, install, deploy)

### Plugin Goal 기능

* compile: src/main/java 디렉토리에 위치한 코드 컴파일
* test-compile: compile goal에 의존관계 있음. compile goal먼저 실행 후 test 코드 컴파일
* test: target/test-classes에 위치한 junit단위 테스트 실행, 결과는 target/surefire-reports 디렉토리에 생성
* package: jar, war 압축
* install: 압축한 jar, war 파일을 local repository에 등록
* deploy: jar, war 파일을 remote repository에 등록
* clean: target 디렉토리의 결과물을 모두 제거


## Maven 설정파일

### 1.setting.xml

메이븐 빌드 툴과 관련한 설정파일, MAVEN_HOME/conf 디렉토리에 위치

### 2. pom.xml

* 메이븐 기반 프로젝트에서 사용하는 설정파일
* 기본 구성요소: 프로젝트 기본정보, 빌드 정보, 프로젝트 관계 설정, 빌드 환경
* 메이븐은 빌드와 관련된 정보를 POM로 정의
* groupId + artifactId의 값이 유일한 값이어야함
* 대부분의 프로젝트는 다른 라이브러리 또는 프로젝트와 의존 관계를 가지며, 각 프로젝트 사이에서 상속 관계를 가지는 경우도 있다.

#### 2.1 pom.xml

* project: pom.xml 파일의 최상위 엘리먼트
* modelVersion: POM 모델의 버전, 최근버전 4.0.0, 수정하지 않는다(버전이 변경되지않는다면)
* groupId: 프로젝트를 생성하는 조직의 고유 아이디, 일반적으로 도메인 이름
* artifactId: 프로젝트를 식별하는 유일한 아이디
* packaging: 프로젝트를 어떤 형태로 패키징 할지 결정(jar, war, ear, pom 등)
* version: 프로젝트의 현재 버전
* name: 프로젝트 이름
* url: 프로젝트 사이트가 있다면 사이트 url을 등록한다
* dependencies: 프로젝트와 의존관계에 있는 라이브러리를 관리함
  * 최소한 groupId, artifactId, version정보 필요
  * scope는 compile, runtime, provided, test등이 오는데, 해당 라이브러리가 언제 필요한지 언제 제외되는지를 나타내는 것이다.
* properties: pom.xml에서 중복해서 사용되는 설정(상수)값들을 지정해놓는 부분. 다른 위치에서 ${...}로 표기해서 사용할 수 있다.
* profiles: dev, prd 와 같이 개발, 릴리즈할 떄를 나눠야할 필요가 있는 설정 값은 profiles로 설정할 수 있다. (maven goal부분에 -P옵션으로 프로파일 선택 가능(ex: ``mvn compile -P prd``))
* parent: 상속 관계에 있는 부모를 명시한다
  * groupId, artifactId, version을 부모의 내용을 명시한다.
  * relativePath: parent가 상위폴더가 아닌, 다른폴더에 있는경우 해당 폴더의 상대경로 혹은 절대경로를 명시
* build
  * finalName: 빌드 결과물(ex *.jar) 이름 설정
  * resource: 리소스(각종 설정 파일)의 위치를 지정할 수 있다. (default: ``src/main/resources``)
  * Repositories: 빌드할 떄 접근할 저장소의 위치를 지정할 수 있다.(default: ``http://repo1.maven.org/maven2``)
  * outputDirectory: 컴파일한 결과물 위치 값 지정.(default: ``target/classes``)
  * plugin: 어떠한 액션 하나를 담당하는 것으로 가장 중요하지만 들어가는 옵션은 제각각이다. 다행인 것은 플러그인 형식에 대한 것은 안내가 나와있으니 그것을 참고해서 작성

parent의 pom인경우 packaging이 무조건 pom으로 되어야한다.


## 참고사이트

1. [메이븐파헤치기](https://www.slideshare.net/ssuser5445b7/ss-56566336?qid=927855f5-7c8a-4f88-a834-d31292324fd2&v=&b=&from_search=4%20%EC%B6%9C%EC%B2%98:%20https://jeong-pro.tistory.com/168%20[%EA%B8%B0%EB%B3%B8%EA%B8%B0%EB%A5%BC%20%EC%8C%93%EB%8A%94%20%EC%A0%95%EC%95%84%EB%A7%88%EC%B6%94%EC%96%B4%20%EC%BD%94%EB%94%A9%EB%B8%94%EB%A1%9C%EA%B7%B8)
2. [메이븐은 알고 스프링을 쓰는가?](https://jeong-pro.tistory.com/168)
3. [메이븐 주요 개념 정리](https://goddaehee.tistory.com/48)