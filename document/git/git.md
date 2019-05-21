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

![how_to_work_VCS](https://github.com/msmasd/TIL/blob/master/document/git/image/how_to_work_VCS.PNG)

git은 데이터를 **파일 시스템 스냅샷의 연속**으로 취급하고 크기가 아주 작다. Git은 커밋하거나 프로젝트의 상태를 저장할 때마다 파일이 존재하는 그 순간을 중요하게 여긴다. 파일이 달라지지 않았으면 Git은 성능을 위해서 파일을 새로 저장하지않는다. 단지 이전 상태의 파일에 대한 링크만 저장한다. Git은 데이터를 **스냅샷의 스트림**처럼 취급한다.

![how_to_work_Git](https://github.com/msmasd/TIL/blob/master/document/git/image/how_to_work_Git.PNG)

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

![Git_Three_State](https://github.com/msmasd/TIL/blob/master/document/git/image/Git_Three_State.PNG)

#### 수정하고 저장소에 저장하기

Git의 파일 라이프 사이클은 아래의 사진과 같다.

![The_lifecycle_of_the_status_of_your_files](https://github.com/msmasd/TIL/blob/master/document/git/image/The_lifecycle_of_the_status_of_your_files.PNG)

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

#### 커밋 히스토리 조회하기

```console
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

    first commit
```
특별한 아규먼트 없이 ```git log```명령을 실행하면 저장소의 커밋 히스토리를 시간순으로 보여준다. 가장 최근의 커밋이 가장 먼저 나온다. 여기서 각 커밋의 SHA-1 체크섬, 저자에 대한 정보가 나온다.


##### 옵션

* ```-p```, ```--patch```는 각 커밋의 diff 결과를 보여준다. 추가 아규먼트는 -2로 하면 최근 두 개의 결과만 보여주는 옵션이다.

```console
 git log -p -2
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number

diff --git a/Rakefile b/Rakefile
index a874b73..8f94139 100644
--- a/Rakefile
+++ b/Rakefile
@@ -5,7 +5,7 @@ require 'rake/gempackagetask'
 spec = Gem::Specification.new do |s|
     s.platform  =   Gem::Platform::RUBY
     s.name      =   "simplegit"
-    s.version   =   "0.1.0"
+    s.version   =   "0.1.1"
     s.author    =   "Scott Chacon"
     s.email     =   "schacon@gee-mail.com"
     s.summary   =   "A simple gem for using Git in Ruby code."

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test

diff --git a/lib/simplegit.rb b/lib/simplegit.rb
index a0a60ae..47c6340 100644
--- a/lib/simplegit.rb
+++ b/lib/simplegit.rb
@@ -18,8 +18,3 @@ class SimpleGit
     end

 end
-
-if $0 == __FILE__
-  git = SimpleGit.new
-  puts git.show
-end
```

* ```--stat```은 각 커밋의 통계 정보를 조회할 수 있다.
```console
  $ git log --stat
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number

 Rakefile | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700

    first commit

 README           |  6 ++++++
 Rakefile         | 23 +++++++++++++++++++++++
 lib/simplegit.rb | 25 +++++++++++++++++++++++++
 3 files changed, 54 insertions(+)
  ```
```--stat```옵션은 어떤 파일이 수정됐는지, 얼마나 많은 파일이 변경됐는지, 또 얼마나 많은 라인을 추가하거나 삭제했는지 보여준다.

* ```--pretty``` 옵션은 히스토리 내용을 보여줄 때 기본 형식 이외에 여러 가지 중에 하나를 선택할 수 있다.
    * ```oneline```옵션은 각 커밋을 한 라인으로 보여준다.
    * ```short```, ```full```, ```fuller```옵션은 정보를 조금씩 가감해서 보여준다.
    * ```format```은 나만의 포맷으로 결과를 출력하고 싶을 때 사용한다.
    * ```--graph```은 브랜치와 머지 히스토리를 보여주는 아스키 그래프를 출력
    * ```--since=```, ```--until```은 같은 시간으로 한정하여 조회한다.
    ```console
    git log --since=2.weeks
    ```

```console
$ git log --pretty=oneline
ca82a6dff817ec66f44342007202690a93763949 changed the version number
085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7 removed unnecessary test
a11bef06a3f659402fe7563abf99ad00de2209e6 first commit
```

| 옵션                  | 설명                 |
| :-------------------  | -------------------: |
| %H                    | 커밋 해시             |
| %h                    | 짧은 길이 커밋 해시    |
| %T                    | 트리 해시             |
| $t                    | 짧은 길이 트리 해시    |
| $P                    | 부모 해시             |
| $p                    | 짧은 길이 부모 해시    |
| $an                   | 저자 이름              |
| $ae                   | 저자 메일              |
| $ad                   | 저자 시각 (형식은 --date=옵션참고) |
| $ar                   | 저자 상대적 시각        |
| $cn                   | 커미터 이름             |
| $ce                   | 커미터 메일             |
| $s                    | 요약                   |

#### 되돌리기

커밋 메시지나 어떤 파일을 빼먹었을떄 다시 커밋하고 싶으면 파일 수정 작업을 하고 staging Area에 추가한 다음 `--amend`옵션을 사용하여 커밋을 재작성 할 수 있다.
```console
git commit --amend
```
이 명령은 Staging Area를 사용하여 커밋한다. 이 명령으로 파일을 빼먹거나 커밋메시지를 고칠때 사용하면 유용하다.

##### 파일 상태를 Unstage로 변경하기

```git reset HEAD <file>```을 통해 Changed to be committed에 올라간 파일을 빼올 수 있다.

##### Modified 파일 되돌리기

```git checkout -- <file>```을 통해 Modified상태인 파일을 다시 Unmodified상태로 변경한다 -> 즉 변경한 내용이 초기화되는 명령어이다.

#### 리모트 저장소

리모트 저장소는 원격 저장소이지만 로컬 시스템에도 위치할 수 있다. 인터넷이라는 의미보다는 로컬 저장소에 떨어져있는 저장소라고 생각하면 편하다.

##### 리모트 저장소 확인하기

`git remote`명령으로 현재 프로젝트에 등록된 리모트 저장소를 확인할 수 있다.
리모트 저장소는 기본 origin으로 등록이 되고, 추가로 등록이 가능하다.(`git remote add <단축이름> <url>`)

##### 리모트 저장소를 Pull 하거나 Fetch하기

`git fetch <remote>`, `git pull <remote>` 명령를 통해 한다.
pull은 fetch를 해서 가져온 뒤에 merge까지 해주는 명령어이다.

##### 리모트 저장소에 Push하기

`git push <remote> <branch>`명령어를 통해 처리한다.

##### 리모트 저장소 살펴보기

`git remote show <remote>`명령을 통해 리모트 저장소의 구체적인 정보를 확인할 수 있다.

#### 태그

커밋에 태그를 붙여 검색을 편하게 해주는 도구인듯하다.

##### 태그 조회

`git tag` 명령으로 태그 전체 리스트 확인
`git tag -l "pattern"` 명령을 통해 pattern을 가진 태그리스트를 검색 할 수 있다.

##### 태그 종류

* Lightweight: 브랜치와 비슷한데 브랜치처럼 가리키는 지점을 최신 커밋으로 이동시키지 않는다. 단순히 특정 커밋에 대한 포인터
* Annotated: Git 데이터베이스에 태그를 만든 사람의 이름, 이메일, 태그 생성날짜, 태그 메시지도 저장. 일반적으로 Annotated태그를 만들어 이 모든 정보를 사용할 수 있도록 하는것이 좋음.

##### 태그 추가

1. Annotated 태그: `git tag -a <tagname> -m <tagMassage> <SHA-1체크섬>` -a를 통해 추가하고 -m은 태그 메시지 추가, 특정 체크섬을 넣으면 특정 체크섬에 대한 태그생성(없으면 현재 커밋에 태그 추가)/ `git show`명령을 통해 태그 정보와 커밋 정보를 모두 확인할 수 있다.
2. Lightweight 태그: `git tag <tagname>`으로 저장


##### 태그 공유하기

`git push`명령을 통해 자동으로 리모트 서버에 태그를 전송하지 않는다. `git push origin <tagname>`을 통해 리모트 서버로 보내야 태그정보가 넘어간다. 여러개를 한번에 보내고 싶으면 `--tags`옵션을 추가하여 실행한다.

##### 태그를 checkout하기

`git checkout <tagname>`을 통해 체크아웃이 가능하지만 이렇게 하면 "detached HEAD"상태가 되며 이 상태에서 커밋을 만들면 태그는 가만히 있으나 새로운 커밋으로 갈 수가 없다. 그래서 `git checkout -b <branch> <tagname>`을 통해 태그명으로 브랜치를 생성하여 사용하면 된다.