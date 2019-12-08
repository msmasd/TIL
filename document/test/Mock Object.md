# Mock Object

아래와 같은 서비스가 있다고 가정하자.
```java
public class CellphoneMmsSender {
    private CellphoneService cellphoneMmsSender;

    public CellphoneMmsSender(CellphoneService cellphoneMmsSender) {
        this.cellphoneMmsSender = cellphoneMmsSender;
    }

    // 해당 send의 함수동작을 테스트하고싶다!
    public void send(String msg){
        CellphoneService.sendMMS(msg);
    }
}
```

CellphoneMmsSender의 send() 메소드에 대한 테스트 코드를 작성하려면 어떻게 해야할까?
* 반환값 검증?
  * void형이라 반환값 검증 불가
* CellphoneMmsSender 테스트 관점에서 중요한것은 실제 문자 메세지를 보내는 것이 아니다.
* 실제 문자 메시지를 보내는 것은 CellphoneService의 책임
  * CellphoneMmsSerder send() 메소드에서 검증해야 하는 것은 전달 받은 메시지(msg)를 sendMMS()의 파라미터로 호출했는지의 여부!
  * 즉, 호출여부를 테스트한다.
  * 호출여부를 테스트하기위해서 mock object를 만들어서 확인한다.

..

## 참고
* https://medium.com/@SlackBeck/mock-object%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-85159754b2ac
