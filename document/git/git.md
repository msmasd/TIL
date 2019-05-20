# git

* [pro git](https://git-scm.com/book/ko/v2)을 참고하여 정리한 내용입니다.

## git의 정의

git은 DVCS(Distributed Version Control Systems)이다. 클라이언트는 단순히 파일의 마지막 스냅샷을 Checkout하지 않는다. 그냥 저장소를 히스토리와 더불어 전부 복제한다. *즉 로컬과 원격에 있는 데이터는 완전히 동일하다*

### git의 특징

* 빠른속도
* 단순한 구조
* 비선형적인 개발(수천 개의 동시다발적인 브랜치)
* 완벽한 분산
* Linux 커널 같은 대형 프로젝트에서도 유용할 것(속도나 데이터 크기 면에서)

### git의 기초

기존 VCS와 가장 큰 차이점은 데이터를 다루는 방법에 있다. VCS 시스템이 관리하는 것은 각 파일의 변화를 시간순으로 관리하면서 파일들의 집합을 관리한다(보통 *델타 기반* 버전 관리 시스템이라고 함)

![how_to_work_VCS](how_to_work_VCS.png)

git은 데이터를 **파일 시스템 스냅샷의 연속**으로 취급하고 크기가 아주 작다. Git은 커밋하거나 프로젝트의 상태를 저장할 때마다 파일이 존재하는 그 순간을 중요하게 여긴다. 파일이 달라지지 않았으면 Git은 성능을 위해서 파일을 새로 저장하지않는다. 단지 이전 상태의 파일에 대한 링크만 저장한다. Git은 데이터를 **스냅샷의 스트림**처럼 취급한다.

![how_to_work_Git](how_to_work_Git.png)

#### 거의 모든 명령을 로컬에서 실행

거의 모든 명령이 로컬 파일과 데이터만 사용하기 때문에 네트워크에 있는 다른 컴퓨터는 필요 없다. 그래서 Git만의 미칠듯한 속도를 구현할 수 있고, 프로젝트의 모든 히스토리가 로컬에 있기 때문에 모든 명령이 순식간에 실행된다.

#### Git의 무결성

Git은 데이터를 저장하기 전에 항상 체크섬을 구하고 그 체크섬으로 데이터를 관리한다. 체크섬 없이는 Git에 어떠한 파일이나 디렉토리도 변경할 수 없다. Git에서 사용하는 가장 기본적인(Atomic) 데이터 단위이자 Git의 기본 철학이다.
Git은 SHA-1 해시를 사용하여 체크섬을 만든다. 만든 체크섬은 40자 길이의 16진수 문자열이다. 파일의 내용이나 디렉토리 구조를 이용하여 체크섬을 만든다(그래서 중복이 되기 어렵다)

#### 세 가지 상태

Git은 파일을 Committed, Modified, Staged 이렇게 세 가지 상태로 관리한다.
* Committed: 데이터가 로컬 데이터베이스에 안전하게 저장됐다는 것을 의미한다.
* Modified: 수정한 파일을 아직 로컬 데이터베이스에 커밋하지 않은 것을 말한다.
* Staged: 현재 수정한 파일을 곧 커밋할 것이라고 표시한 상태를 의미한다.

![Git_Three_State](Git_Three_State.png)

#### 수정하고 저장소에 저장하기

Git의 파일 라이프 사이클은 아래의 사진과 같다.

![The_lifecycle_of_the_status_of_your_files](The_lifecycle_of_the_status_of_your_files.png)

워킹 디렉토리의 모든 파일은 아래의 상태로 나눠진다.
* Tracked
  * Unmodified
  * Modified
  * Staged
* Untracked

Tracked는 이미 스냅샷에 포함돼 있던 파일이다. 즉 Git이 알고 있는 파일이다.
Untracked파일은 워킹 드렉토리에 있는 파일 중 스냅샷에도 Stating Area에도 포함되지 않은 파일이다.

#### 파일의 상태 확인하기

git status 명령어를 통해 파일의 상태를 볼 수 있다.

```console
$ git status
1 On branch master
2 Your branch is up-to-date with 'origin/master'
3 nothing to commit, working directory clean
```
1번은 현재 어떠한 브랜치인지 알려주고
2번은 해당 브랜치가 원격저장소와 어떠한 상태인지 알려주고
3번은 현재 로컬 브랜치의 상태를 보여준다
* Untracked files: Untracked의 상태 파일을 보여준다.
* Changed to be committed: Staged 상태라는 것을 보여준다.
* Changes not staged for commit: 수정한 파일이 Tracked상태이지만 아직 Staged 상태는 아니라는 것이다.(Modified인 상태)

#### 변경사항 커밋하기

Staging Area에 있는 파일을 committed상태로 변경하기 위해서는 ```git commit```명령어를 실행하면 된다. 해당 명령어를 실행하면 commit 메시지를 입력하라고 한 뒤 입력뒤에 commit이 완료가 된다. *메시지를 인라인으로 첨부 할 수있다. -> ```git commit -m message```*


#### 파일 삭제하기

git에서는 파일 삭제하는것은 ```git rm```명령어로 되는것인데 이 명령어는 파일은 삭제되고 Git은 이 파일을 더는 추적하지 않는것이다.


#### 파일 이름 변경하기

git에서는 파일 이름의 변경이나 파일의 이동을 명시적으로 관리하지 않는다. 그냥 예전 파일을 삭제```git rm```하고 새로운 파일을 ```git add```하는 것으로 처리되어진다. 이 명령을 간단하게 ```git mv```명령어로 처리된다.