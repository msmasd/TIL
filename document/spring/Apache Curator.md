# Apache Curator

Java Client library for Zookeeper.
Zookeeper를 사용하기 쉽고 안정적으로 쓰게 해준다.
recipes들이 있는데 참조하여 개발하기.

## zookeeper cli

`docker exec -i -t docker_zookeeper_1 /apache-zookeeper-3.5.6-bin/bin/zkCli.sh -server localhost:2181`
로 사용

* 값 확인
  * get path
    * e.g. get /codeManager/notification/version

## watcher

zookeeper는 자바에서 getData를 통해서 watcher 역활을 할 수 있을꺼라고 보여진다?
-> https://www.slideshare.net/madvirus/zookeeper-34888385 여기 피피티를 보고..
내일 작업 진행해보기

## 참고
* https://curator.apache.org/
* https://www.slideshare.net/madvirus/zookeeper-34888385
* https://d2.naver.com/helloworld/583580
