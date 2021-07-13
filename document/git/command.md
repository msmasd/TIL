# git command

## about code

### 1. add

```console
git add <file_name>
```

### 5. commit

## about branch

### 1. fetch

### 2. push

### 3. pull

### 8. rebase

### reset

`git reset --hard HEAD`

### 1. merge

다른 브랜치와 합치는 명령어

* 옵션
  * --abort: 깃 머지 취소하기 (`git merge --abort`)
  * --ff: 현 브랜치와 병합할 브랜치가 Fast-forward 관계인 경우 병합할 브랜치(커밋)을 따라감. 그리고 merge 커밋 없음. ff관계가 아니면 병합할 브랜치와 병합됨. 그리고 merge 커밋 생성
  * --no-ff: 현재 브랜치와 병합 대상의 관계가 Fast-forward이든 아니든 무조건 merge 커밋과 같이 병합되는 옵션
  * --ff-only: 현재 브랜치와 병합 대상의 관계가 Fast-forward인 경우에만 병합 진행. merge 커밋 생성되지 않음
  * --squash: 현재 브랜치에 병합 대상과의 차이나는 commit을 하나로 합쳐서 커밋함.

### rebase

## about status

### 6. log

### 7. status

### 9. cherry pick

