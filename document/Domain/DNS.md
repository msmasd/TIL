# DNS

DNS(Domain Name System)은 기본적으로 웹에서 도메인을 정리하고 확인하는 전화번호부와 같은 역활을 합니다. "www.google.com"와 같은 웹 주소를 DNS를 통해 "74.125.19.147"과 같이 사이트를 호스팅하는 컴퓨터의 실제 IP 주소로 변환해줍니다.

## 1. DNS 요소

### 1.1 네임서버

네임서버는 도메인 이름을 DNS 설정을 제어하는 회사로 **연결**합니다. 일반적으로 이는 사용자가 도메인 이름을 등록한 회사가 됩니다.

하지만 사용자의 웹 사이트가 다른 회사를 통해 호스팅되는 경우, 해당 회사가 대신 연결하는 데 필요한 네임서버를 제공하기도 합니다.

### 1.2 영역 파일

영역 파일은 단순히 도메인의 모든 DNS 설정이 저장되는 파일입니다.

도메인 이름의 영역 파일은 회사의 네임서버에 저장됩니다.

## 2. 레코드 종류

### 2.1 A 레코드

A 레코드는 IP 주소를 사용하여 도메인 이름을 개별 서버에 연결합니다.
EX) "www.google.com"의 IP주소는 "74.125.19.147"입니다.

모든 도메인 이름에는 "@"이라는 기본 A 레코드가 있어서 누군가가 직접 방문할 때 사용자의 도메인 이름이 무엇인지 제어합니다.

A 레코드를 사용하여 서브도메인이 서버의 IP주소를 가리키도록 할 수 있다.
(서브도메인은 http://서브도메인.도메인.상위도메인 이렇게 나눠진다고 보면 된다)

### 2.2 CNAME

CNAME은 server1.godaddy.com과 같은 서버 이름을 사용하여 다른 서버로 서브도메인을 가리킵니다. 즉 별칭(Canonical Name)으로 매핑해주는 역활

대부분의 도메인 이름에는 많은 CNAME이 있습니다.

A레코드와 달리 CNAME은 IP주소를 사용할 수 없습니다.

### 2.3 MX 레코드

MX(Mail Exchange)레코드는 도메인 이름의 이메일을 이메일 제공업체와 연결합니다.

### 2.4 기타 레코드

* NS레코드: 도메인의 네임서버 정보입니다.
* TXT(Text strings)레코드: 호스트에 대한 텍스트 정보
* AAAA 레코드: 16진법 형식의 IPv6 주소
* TTL(Time To Live): 다음 레코드 변경사항이 적용될 때까지 걸리는 시간(초)을 결정하는 DNS 레코드 값(EX: TTL값이 86400초인 레코드는 변경사항이 적용될 때까지 24시간이 소요됩니다.)