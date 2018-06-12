# Webflux

### Webflux에서 `AsyncRestTemplate`은 deprecated되고, `WebClient`로 대체됨
`AsyncRestTemplate`는 `ListenableFuture`를 반환하는데, 이것은 꼭 callback 패턴을 써야하므로 코드 구조를 망치기 때문.

### Webflux는 Controller에서 Mono를 반환하도록 작성하는 것이 더 좋다.
```java
@RestController
public class MyController {
  @GetMapping("/rest")
  public Mono<String> rest() {
    return Mono.just("Result"); // Mono에서 값을 꺼내고, 그것을 ResponseBody에 쓰는 것은 Spring이 하는 일. 즉, subscribe를 호출해서 값을 꺼낸다.
  }
}
```

### `WebClient`
기본적으로 `RestTemplate`과 비슷하고, 더 나아가 `AsyncRestTemplate`과 같은 역할을 한다.
하지만 스프링 웹프럭스답게 Mono<ClientResponse>를 반환한다.

```java
@RestController
public class MyController {
  private static final String URL1 = "http://localhost:8081/service1?req={req}";
  private static final String URL2 = "http://localhost:8081/service2?req={req}";
  
  @Autowired
  private MyService myService;
  
  WebClient client = WebClient.create();

  @GetMapping("/rest") // 이 코드는 AsyncRestTemplate + DeferredResult처럼 비동기 논블로킹으로 동작한다.
  public Mono<String> rest(@RequestParam int idx) {
    // Spring webflux는 대부분 Mono를 반환. 여기까지가 결과를 받아오는 연산. 하지만 정의하는 순간 실행되지는 않는다.
    // 왜냐하면 Mono는 Publisher인데, Subscriber가 값을 request하지 않으면, 값을 생산하지 않는다.
    Mono<ClientResponse> res = client.get().uri(URL1, idx).exchange();
    Mono<String> body = res.flatMap(clientResponse -> clientResponse.bodyToMono(String.class)); // flapMap(f)의 f: x -> Mono<y>
    return body
            .flatMap(body -> client.get().uri(URL2, body).exchange()) // 앞에서 받은 값을 이용해서 다음 request를 보내보자
            .flatMap(clientResponse -> clientResponse.bodyToMono(String.class)); // Mono<String>
            .flatMap(body -> Mono.fromCompletionStage(myService.work(body))); // Mono<String>
  }
}

@Service
class MyService {
  @Async // 시간이 오래걸리는 작업이라면, 위의 컨트롤러의 Netty worker thread를 묶고 있을 수 있다. 따라서 별개의 스레드에서 동작하도록 수정.
  public CompletableFuture<String> work(String req){ 
    return CompletableFuture.completedFuture(req + "/work");
  }
}
```

#### 참고
원래 Spring Web application을 배포할 때는 tomcat을 띄우고, 거기에 war같은 것을 경로 설정하여 배포를 하였는데,
스프링부트는 위의 두 가지 행위를 동시에 해주는 것.
그런데 Http를 받고 처리하는 부분은 Netty / Undertow 등으로 변경해도 상관 없으며, Spring 5 부터는 톰캣에 의존성을 명시적으로 낮추거나 제거한 것. 
