# dockerImage.md

## 도커 이미지가 저장되는 방식

도커 이미지를 `pull`받게 되면 마치 여러개로 분리된 조각을 내려받는 것처럼 보인다.
```
$ docker pull nginx:latest

Using default tag: latest
latest: Pulling from library/nginx
c499e6d256d6: Already exists
74cda408e262: Pull complete
ffadbd415ab7: Pull complete
Digest: sha256:282530fcb7cd19f3848c7b611043f82ae4be3781cb00105a1d593d7e6286b596
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```
이렇게 분리된 데이터를 **레이어(Layer)**라고 한다.

레이어는 도커 이미지가 빌드될 때 `DockerFile`에 정의된 명령문을 순서대로 실행하면서 만들어진다. 이 레이어들은 각각 독립적으로 저장되며 읽기 전용이기 때문에 임의로 수정할 수 없다.

실제로 레이어가 어디에 어떻게 저장되는지 정확하게 확인하려면 `docker image inspect`명령어를 실행한 뒤 출력된 결과에서 아래와 같은 해시값들을 볼 수 있다.

```
"sha256:c3a984abe8a88059915bb6c7a1d249fd1ccc16d931334ac8816540b0eb686b45"
"sha256:99134ec7f247e5a211c7205fec587bf72a6d4aac339b21858b892e9c04f78920"
"sha256:d37eecb5b7691ec21bd19989e37f8bb4d20b340a775591d0f3db5897d606b0e4"
```

이 해시값으로 호스트 머신 내 어딘가 실제 레이어 내용이 저장된 것을 찾을 수 있다.
좀 더 자세한 설명은 (링크)[https://www.44bits.io/ko/post/how-docker-image-work]를 참고

그렇다면 **도커 컨테이너가 실행될 때 생성되는 결과물들은 어디에 저장될까?**
도커 컨테이너가 실행되면 모든 읽기 전용 레이어들은 순서대로 쌓은 다음 마지막에 쓰기 가능한 신규 레이어를 추가하게 된다. 그다음 컨테이너 안에서 발생하는 결과물들이 쓰기 가능 레이어를 기록하게 되는 것이다.

즉, 아무리 많은 도커 컨테이너를 실행하더라도 기존 읽기 전용 레이어는 변하지 않고, 컨테이너마다 생성된 쓰기 가능 레이어에 데이터가 쌓이기 떄문에 서로 겹치지 않으며 컨테이너가 종료되면 모두 사라지게 된다.

## 도커 이미지 레이어에 대해

도커 이미지 레이어가 중요한 이유는 이미지를 빌드할 때마다 이미 생성된 레이어가 캐시되어 재사용되기 때문에 빌드 시간을 단출할 수 있다.
하지만 `Dockerfile`에 정의된 모든 명령문은 레이어가 되는 것은 아니다.

`RUN`, `ADD`, `COPY` 이 3가지 단계만이 레이어로 저장되고, `CMD`, `LABEL`, `ENV`, `EXPOSE` 등과 같이 메타 정보를 다루는 부분은 임시 레이어로 생성되지만 저장되지 않아 도커 이미지 사이즈에 영향을 주지 않는다.

이 글의 가장 첫 부분에서 `pull` 받은 `nginx:latest` 도커 이미지의 `Dockerfile`을 보면 처음에 베이스 이미지를 가져오는 부분을 포함해서 2개 `RUN` 명령까지 총 3개의 레이어를 받아왔다.
만약 `Dockerfile`에 `RUN`, `ADD`, `COPY` 명령문이 수정되면 기존 캐시가 무효가 되어 새로운 레이어를 생성하게 될 것이다.

## 도커 이미지 잘 만드는 방법

### 1. 베이스 이미지는 사이즈가 작은 것을 선택

### 2. 이미지 레이어 개수를 줄이자

### 3. 애플리케이션 코드를 복사하는 부분은 아래로

### 4. 프로그래밍 언어마다 패키지 매니저가 제공하는 Lock 파일 시스템을 활용

### 5. 멀티-스테이지 빌드를 활용

## 참고
* https://jonnung.dev/docker/2020/04/08/optimizing-docker-images/
