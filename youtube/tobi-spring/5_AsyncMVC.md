# 비동기 MVC

## ListenableFuture<T>
스프링 ListenableFuture는 Callback을 등록할 수 있는 Future타입.
스프링은 4.0부터 `AsyncRestTemplate`이라는 것을 제공하고 있고, 이 `AsyncRestTemplate`은 Header, Body, 응답코드 등을 모두 포함한 `ResponseEntity`를
`ListenableFuture`로 감싼 `ListenableFuture<ResponseEntity<T>>`를 반환해주는 `.getForEntity`를 제공

또한 스프링은 아래와 같이 이것을 그냥 반환하면, 
알아서 서블릿 스레드를 이용해 Request를 보내고, 응답을 기다리는 동안 이 스레드를 반환하고, 다시 Request의 응답이 오면 이것을 http 응답으로 반환해줌
```java
@RestController
class MyController {
  AsyncRestTemplate rt = new AsyncRestTemplate(new Netty4ClientHttpRequestFactory(new NioEventLoopGroup(1)));
  
  @GetMapping("/rest")
  public Listenable<ResponseEntity<String>> rest(@RequestParam int idx) {
    return rt.getForEntity("http://somewhere.com/service?idx={idx}", String.class, idx);
  }
}
```
