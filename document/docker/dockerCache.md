# dockerCache.md

docker build시 layer에 캐싱을 처리하면 해당 부분은 새로 업로드하지않고 기존 caching된 값을 사용한다.

* node에 node_modules관련 캐싱 처리
  * packages.json을 COPY하고
  * npm install을 처리하면
  * packages.json이 변경되지 않으면 npm install도 하지 않는다.
  * 해당 작업 시, .dockerIgnore파일에 node_modules를 추가해야한다.

```docker
COPY packages.json ./
CMD npm install

# 모든 파일 복사
COPY . .
```

## 참고
* https://nodejs.org/ko/docs/guides/nodejs-docker-webapp/
* http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/
