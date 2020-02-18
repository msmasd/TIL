# kubernetes cli

## 쿠버네티스 내부 서비스끼리 연결

쿠버네티스 내부에 다른 pod으로 연결이 가능한것은 serviceName.nameSpace로 쿠버내 자체 dns가 있어서 그런듯 하다.

http://{service_name}.{namespace} 로 연결하면 된다.
Ex. http://dev-account.dev-service/v1/accounts

* service_name은 helm에서 쿠버를 올릴때 만들어지는데, 현재 설정은 `{profile}-{name}`이다.

## kubernetes cli

* `kubectl get pods`
  * 기본적으로 context와 namespace가 선택되어진 상태에서 pod의 리스트를 볼 수 있다
  * 선택된 namespace에서의 pods 리스트
* `kubectl logs -f {pod-name}`
  * pod의 `stdout`의 로그를 볼 수 있다.

### kubectx, kubens

current-context, namespace를 편하게 전환해줌
* kubectx: current-context 전환
  * kubectx
    * 선택할 수 있는 context 리스트 출력 `kubectx`
  * kubectx {context 이름} `kubectx dev-cluster`
    * context 선택
* kubens: name
  * kubens
    * 선택할 수 있는 namespace 리스트 출력 `kubens`
  * kubecns {namespace 이름} `kubens dev-service`
    * namespace 선택

## 참고
