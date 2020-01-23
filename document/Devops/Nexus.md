# Nexus

## docker-compose를 통한 설치방법
* docker-compose.yml을 작성

```yaml
version: '3'
services:
  dev-nexus:
    image: "sonatype/nexus3:3.20.0"
    container_name: dev-nexus
    ports:
      - "8081:8081"
    environment:
      - NEXUS_CONTEXT=nexus
      - INSTALL4J_ADD_VM_PARAMS=-Xms512m -Xmx1g -XX:MaxDirectMemorySize=512m -Djava.util.prefs.userRoot=/nexus-data
# 아래 내용은 개발계에 올릴때 사용. 로컬에서는 에러 발생
#    volumes:
#      - /app/svc/data/nexus-data:/nexus-data
```

* `docker-compose up`을 통하여 docker 이미지 구동
* 구동 완료 되었으면 http://localhost:8081/nexus/index.html 로 접속하여 넥서스 구동 확인
* 관리자로 로그인 하기 위해서는
  * 아이디는 `admin`
  * `docker exec -i -t dev-nexus /bin/bash`를 통하여 nexus 서버 접속
  * `cat /nexus-data/admin.password` 기본 패스워드 확인 후 해당 값으로 로그인

## 참고
* https://www.lesstif.com/pages/viewpage.action?pageId=20775149
