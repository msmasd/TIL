# docker

## 설치 방법(MAC)

1. https://hub.docker.com/editions/community/docker-ce-desktop-mac 사이트 접속
2. docker 회원가입 후 다운로드 진행
3. docker 설치 후 실행
4. docker 로그인 후 docker 실행 완료
5. 터미널에 `docker -v` 버전확인 명령어로 docker 실행여부 확인

## docker 기본 명령어

* `docker pull [이미지 명]`
  * 이미지 다운로드
* `docker images`
  * 로컬에 설치된 이미지들을 확인할 수 있다.
* `docker ps`
  * 실행중인 컨테이너 목록 확인
  * Option
    * `-a`: 중지된 컨테이너까지 포함된 목록 조회
* `docker create [옵션] [이미지 명]`
  * 컨테이너 생성
* `docker start [컨테이너 명 or container_id]`
  * 컨테이너 실행
* `docker run [옵션] [이미지 명]`
  * 컨테이너 생성 및 실행
* `docker stop [컨테이너 명 or container_id]`
  * 컨테이너 정지
* `docker rm [컨테이너 명 or container_id]`
  * 컨테이너 삭제
* `docker exec`
  * 실행중인 컨테이너에 접근할 수 있게 해준다.
  * 심지어, bash shell에 접근이 가능하다.
    * `docker exec -i -t [컨테이너 명 or container_id] /bin/bash`
    * -i: 표준입력(stdin) 활성
    * -t: TTY모드(pseudo-TTY) 사용
    * 접근한 뒤에 가장 기본의 bash이기 때문에, `apt-get update`를 진행한다 -> 로컬에서만 한다. 개발에서하면 위험하다.
      * ps 명령을 사용하기 위해서 `apt-get install procps` 설치

## 참고
* https://whitepaek.tistory.com/38
