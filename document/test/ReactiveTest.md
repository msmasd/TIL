일반 테스트와는 다르게 Reactive로 되어있는 서비스는 test도 다르게 해야한다.

Publisher 테스트는 verifyComplete 함수를 호출해야 구독이 되고 그후에 결과를 얻을 수 있다.

Publisher(Mono, Flux) 테스트시에는 `StepVerifier` 를 사용하여 한다.

```java
// Count check
StepVerifier.create(Flux.just("안녕", "하세요"))
	.expectNextCount(2)
	.verifyComplete();

// Exception Check
StepVerifier.create(Flux.error(RuntimeException::new))
	.expectError(RuntimeException.class)
	.verify();

// Mock처리하여 Test 확인
@Slf4j
@RunWith(SpringRunner.class)
@WebFluxTest
@ContextConfiguration(classed = {ReactiveCassandraConfig.class})
public class WebfluxTestRepoTest {
	@Autowired
	private WebfluxTestRepo webfluxTestRepo;

	@Before
	publiv void setUp() {
		Flux<WebfluxTest> deleteAndInsert = webfluxTestRepo.deleteAll()
			.thenMany(webfluxTestRepo.saveAll(Flux.just(new WebfluxTest("1", 50),
				new WebfluxTest("2", 45),
				new WebfluxTest("3", 42),
				new WebfluxTest("4", 27)
			)));

		StepVerifier
			.create(deleteAndInsert.doOnNext(info -> log.info("info: {}", info)))
			.expectNextCount(4)
			.verifyComplete();
	}
}

```