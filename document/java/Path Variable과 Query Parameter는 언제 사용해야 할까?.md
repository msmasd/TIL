# Path Variable과 Query Parameter는 언제 사용해야 할까?.md

## Query Parameter

### 가정
소셜 서비스를 만든다고 가정해보자. 사용자 목록을 관리해야 하고, 모든 사용자는 사용자 페이지가 있어야 할 것이다.

각각의 사용자를 위한 페이지를 만들려면 페이지 마다 식별된 파라미터 경로가 필요한데, 다음과 같은 get 파라미터를 사용할 수 있을 것 이다.

`GET host/users?id=123 # 아이디가 123인 사용자를 가져온다`

그럼 서버로부터 id변수를 얻을 수 있다.

## PathVariable

위와 같은 동작을 PathVariable로도 가능하다

`GET host/users/123 # 아이디가 123인 사용자를 가져온다.`

## 그럼 Path Variable과 Query Parameter를 각각 언제 사용하는가?

어떤 **resource를 식별**하고 싶으면 Path Variable을 사용하고,
**정렬이나 필터링**을 한다면 Query Paramter를 사용하는것이 Best Practice이다.

```
/users  # 사용자 목록을 가져온다.
/users?occupation=programer # 프로그래머인 사용자 목록을 가져온다.
/users/123  # 아이디가 123인 사용자를 가져온다.
```

또한, 기본적인 CRUD 기능을 위해서 또 다른 URL이나 query paramter를 정의할 필요는 없다.
대신 원하는 기능에 맞게 HTTP 메소드를 바꾸어야 한다.
```
/users  [GET]   # 사용자 목록을 가져온다.
/users  [POST]  # 새로운 사용자를 생성한다.
/users/123  [PUT]   # 사용자를 갱신한다.
/users/123  [DELETE]    # 사용자를 삭제한다.
```

## 참고
* https://ryan-han.com/post/translated/pathvariable_queryparam/
