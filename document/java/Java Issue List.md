# Java Issue List

## 1. redis에 있는 값들을 get,set하는 경우 조심해야 하는 부분

redis에는 string으로만 데이터를 저장할 수 있습니다.
`List<Object>`인 것들을 저장할 시, 단순시 `.toString()`을 통해서 저장하게 된다면 해당 값을 get을 할 때 에러가 발생합니다.

위 문제는 객체를 저장할 때 deserialize할 수 없는 문자열로 바꾸기 때문입니다.

* 객체를 toString을 하는 것이 serialize
* 값을 가져와서 객체로 매핑하는 것이 deserialize

해결방법

```java
@Autowired
private ObjectMapper mapper;
@Autowired
private StringRedisTemplate stringRedisTemplate;
..
public void setRedisObject(TestDto a) {
    ValueOperations<String, String> values = stringRedisTemplate.opsForValue();
    values.set("testKey", mapper.writeValueAsString(a));
}
public TestDto getRedisObject() {
    ValueOperations<String, String> values = stringRedisTemplate.opsForValue();
    TestDto testDto = mapper.readValue(values.get("testKey"), new TypeReference<TestDto>() {});
    return testDto;
}
```

위 코드처럼 ObjectMapper를 통해 `writeValueAsString`을 통해 객체를 serialize하고
값을 받아온뒤에 해당 문자열을 `mapper.readValue`를 통해서 deserialize를 처리한다.


## 참고
