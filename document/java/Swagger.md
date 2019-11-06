# Swagger

API로부터 API Document 자동생성하거나 Server/Client code를 생성하는 기능을 제공하는 API Framework를 이야기한다.

아래와 같이 이미 작성된 API 코드에서 Swagger Definition(YAML, JSON 등)을 생성하고, 이를 기반으로 Code/API Document를 생성 할 수 있다.

API -> Swagger Definition -> Server/Client code
API -> Swagger Definition -> API Document


Swagger Editor -> Swagger Definition -> Swagger Codegen, Swagger UI -> API
API  Swagger Editor, Swagger-Core IAX-RS, Automatic generation -> Swagger Definition ->  Swagger Codegen, Swagger UI -> API

## Swagger에서 공식적으로 지원하는 툴 리스트

1. Swagger Core
    * Swagger Definition을 생성하는 library
2. Swagger CodeGen
    * Swagger Definition을 이용하여 Server/Client code를 생성하는 Command-line tool
3. Swagger UI
    * REST API Document web 제공
4. Swagger Editor
    * YAML/JSON 형태의 Swagger Definition을 편집하는 editor

## Swagger 기능

* 작성된 API를 기반으로 Swagger Definition Generation
* 작성된 API를 기반으로 API Document Web Service -> Swagger-UI
* Swagger Definition을 기반으로 Test Code Generation -> Swagger CodeGen
* Swagger Definition을 기반으로 API Document Web Service -> Swagger-UI

## Swagger 환경 설정

Springfox를 이용한 Swagger 연동 방법은 아래와 같은 순서로 진행하면 된다.

1. maven dependency에 springfox 라이브러리 추가
2. Swagger Configuration 파일에 추가
    * Swagger Configuration은 어떤 Package에 추가하든 상관없지만 가급적이면 Config 관련 Package에 추가하는걸 권장한다.
3. Swagger UI를 resource에 추가
4. Swagger JSON 생성 확인

## Springfox-swagger 주요 annotation

### 1. ApiOperation

swagger를 사용할 url path에 사용하여 swagger-ui에 리스트를 만드는 annotation

* value: 메서드에 설명 명시

### 2. ApiModel

스웨거 모델에 추가적인 정보를 제공해줄 수 있다.
이 어노테이션으로 모델의 클래스명이 동일함으로써 발생하는 이슈들을 해결할 수 있다.

* value: 해당 모델에 대한 대체 이름(alternative name)을 제공한다
    * 이 값을 통해서 동일한 모델명에 대한 중복을 피한다.

### 3. ApiModelProperty

모델안에 멤버변수에 대해서 설명과 샘플데이터를 제공할 수 있다.

    * value: 값에 대한 설명
    * required: 필수값인지에 대한 boolean
    * example: 샘플데이터값

