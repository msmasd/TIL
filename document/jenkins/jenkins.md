# Jenkins

지속적 통합(CI)와 지속적 배포(CD) 를 위한 대표적인 도구입니다. 빌드, 테스트, 배포 프로세스를 자동화하여 소프트웨어 품질과 개발 생산성을 높일 수 있습니다.

## 1. Jenkins?

젠킨스는 다른 일상적인 개발 작업을 자동화할 뿐 아니라 파이프라인(Pipeline)을 사용해 거의 모든 언어의 조합과 소스코드 리포지토리에 대한 지속적인 통합과 지속적인 전달 환경을 구축하기 위한 간단한 방법을 제공한다.

젠킨스는 **build**, **Test**, **Publish**, **deploy**의 단계로 구성할 수 있다.

젠킨스는 1400가지의 플러그인을 가지고 있어서 거의 모든 언어를 커버할 수 있다.

젠킨스는 WAR를 단독으로 또는 톰캣 같은 자바 애플리케이션 서버에서 서버렛(serverlet)으로 실행할 수 있다. 두개다 UI를 생성하며 REST Api에 대한 호출을 받아드린디ㅏ.



## 2. 특징

### 파이프라인

```markdown
pipeline {
  agent {
    docker {
      image 'node:6-alpine' // 1
      args '-p 3000:3000' // 2
    }
  }
  stages {
    stage('Build') { // 3
      steps {
        sh 'npm install' // 4
      }
    }
  }
}
```

1. `image` parameter는 `node:6-alpine Docker image`를 다운로드 하고 각 분리된 컨테이너(separate container)에 이미지를 실행(run)하는것이다.
2. ..?
3. state를 정의한다.(state는 `Build`, `Test`, `Publish`, `Deploy` 등)
4. 각 스텝에서 해야할 명령어를 입력한다.(이 프로젝트는 node.js에서 react를 빌드하는 프로젝트여서 `npm install`을 실행함)



### 2. Job

젠킨스는 잡이라는 것을 통해 CI작업을 한다. Job에서 어떤 작업을 할지 정의하면 된다.


### 2. 

## 3. 구성

agent: 는 node가 되어서 서버에서 돌아가던지 아니면 cloud에서 돌이가든지 한다!


## 4. 예시

#### SCM

SCM(Source Control Management)

Pipeline script from SCM은 소스코드에서 Pipeline Script를 얻도록 하는 옵션이다.
