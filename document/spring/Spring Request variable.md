# Spring Request variable

스프링에서 여러가지 방법으로 인자값을 받을 수 있습니다.

* RequestParam
  * parsing
    * Spring MVC
      * query parameters, form data, parts in multipart request
    * Spring WebFlux
      * only query parameter
  * requestBody에서 특정값을 String으로 받음
  * List
    * Object List는 불가능하고, 단순 값들은 가능
* RequestPart
  * MultiPart에 특화된 방식
  * 주로 MultiPartFile을 처리
* RequestBody
  * parsing
    * RequestBody를 JSON을 통하여 Model로 파싱
    * 부분적으로 RequestBody를 선택하여 할 수는 없음
  * 모든 List들도 가능하다
  * 단지, Json으로 파싱할 수 없는 File은 사용할 수 없다.
* ModelAttribute
  * parsing
    * 값을 하나하나 찍어서 해당 값에 맞도록 파싱한다.
  * 모든 List와 파일도 가능하다.
  * 단지, List를 표현하는 방식에 대해서는 JSON방식([{..}, {...}])은 불가능하다
    * 아래 방식으로 List를 입력받을 수 있다.
      * items[0].name=item1Name
      * items[0].value=item1Value
      * items[1].name=item2Name
      * items[1].value=item2Value
