## URL

URL은 Uniform Resource Locator
단어의 뜻을 그대로 해석하면, 예전에는 URL이 가리키는게 자원(파일) 위치였는데, 요즘에는 Rewrite 등의 Apache, IIS, Tomcat 핸들러 때문에 자원 식별자 이라고 부른다.

즉 요즘에는 웹 사이트 주소가 (http://test.com/company/location) 라고 했을 때, 요청하는 주소가 파일이라기 보다는 구분자로 보는 것이다. -> 실제로 해당 웹사이트의 company/location 이라는 파일은 없다.

요약을 하면 URL는 http://test.com/work/sample.pdf인경우, test.com 서버에서 work 폴더안의 sample.pdf를 요청하는 URL

## URI

URL은 Uniform Resource Identifier

1. rewrite 기술을 사용하여 만든 의미있는 식별자. (http://test.com/company/location)
2. REST 서비스(url로 실행되는 서비스) (http://service.com/tv/turn/on)

## 차이점?

URI는 인터넷상의 자원을 식별하기 위한 문자열 정도로 생각하면되고,
URL은 네트워크 상에서 자원이 어디 있는지를 알려주기 위한 규약이다. 이는 인터넷 상의 자원 위치라고 생각하면 된다.

URI가 가장 큰 상위개념이고, 이것의 하위 개념으로 URL과 URN이 있다.

URL은 URI의 한 형태로, URI는 URL을 포함하는 개념이다.
