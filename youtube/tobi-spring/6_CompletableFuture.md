# CompletableFuture

이름과 같이 실제로 프로그래머가 완료와 관련된 작업을 간단히 시행할 수 있다.

## APIs

### `CompletableFuture.completedFuture`: 직접 결과값을 써주고 싶을 때, 사용할 수 있다.
이미 완료가 된 상태로 CompletableFuture를 만들어준다.
```java
CompletableFuture<Integer> f = CompletableFuture.completedFuture(1);
System.out.println(f.get()); // 이미 완료된 것이므로 결과가 즉시 반환됨.
```

### `CompletableFuture.complete`, `CompletableFuture.completeExceptionally`: 직접 결과값을 써주고 싶을 때, 사용할 수 있다.
```java
CompletableFuture<Integer> f = new CompletableFuture<>();
f.completeExceptionally(new RuntimeExeption());
System.out.println(f.get()); // 이 시점에서 예외가 던져진다.
```

자바8에서는 Spring4의 `AsyncRestTemplate`의 `getForEntity(return값 = ListenableFuture<ResponseEntity>)`와 사용할 경우, 실제로 다음과 같은 코드를 작성해서 사용할 수 있다.
이렇게 하면, CompletableFuture의 장점들(chaining을 통한 비동기 작업)을 사용할 수 있는 타입으로 변환 가능.

```java
 <T> CompletabbleFuture<T> toCompletableFuture(ListenableFuture<T> lf) {
    CompletableFuture<T> cf = new CompletableFuture<>();
    lf.addCallback(res -> CompletableFuture.complete(res), e -> CompletableFuture.completeExceptionally(e));
    return cf;
 }
```

### `CompletableFuture.runAsync`
```java
CompletableFuture.runAsync(() -> {
  log.info("runAsync");
});
log.info("exit");

ForkJoinPool.commonPool().shutdown(); // 아무런 설정을 하지 않으면, Java 7부터는 이 CommonPool의 worker를 사용
ForkJoinPool.commonPool().awaitTermination(2, TimeUnit.SECONDS);
```

결과는 'exit'는 main thread에서 찍고, 그 다음 'runAsync'를 common pool의 worker thread가 출력

### `CompletableFuture`의 `CompletionStage`
`CompletableFuture`는 `CompletionStage`을 구현하고 있는데, 이것은 하나의 비동기 작업을 처리한 후 이어서 다음 비동기 작업을 할 수 있는
방법을 제공하고 있다.
또한 `CompletionStage`는 core의 성능을 덜 잡아먹는 장점이 존재하기도 한다.
```java
CompletableFuture
  .runAsync(() -> log.info("runAsync"))
  .thenRun(() -> log.info("thenRunAsync")); // 이 구문은 위의 비동기 작업을 한 스레드에서 하나의 비동기 작업을 추가로 하게 만든다.
```

### `CompletableFuture.supplyAsync`, `CompletableFuture.thenApply`, `CompletableFuture.thenCompose`
`supplyAsync`는 인자로 Supplier를 입력받는데, `Supplier`는 인자는 받지 않지만 리턴값은 존재하는 인터페이스이다.
```java
 CompletableFuture
  .supplyAsync(() -> {
    log.info("runAsync");
    retrun 1;
  })
  .thenApply(num -> { // 이전에서 완료된 비동기 작업의 결과를 받을 수 있다.
    log.info("thenRun {}", num);
    return num + 1; // 이렇게 값을 반환하면, 내부적으로 CompletableFuture로 값을 감싸게 된다. 즉, 이것은 map과 비슷
  })
  .thenCompose(num -> { 
    // thenCompose는 값이 아니라 CompletableFuture 타입을 반환하는 경우, CompletableFuture로 중복해서 감싸지지 않도록 해주는 API.
    // 즉, 이것은 모나드의 flapMap과 비슷
    log.info("thenRun {}", num);
    return CompletableFuture.completedFuture(1);
  })
  .thenAccept(num -> {
    log.info("num {}", num);
  }); // thenAccept의 인자는 Consumer이므로 인자를 받고, 리턴값은 없는 인터페이스
```

`CompletableFuture.thenApplyAsync`는 `CompletableFuture.thenApply`와 비슷하지만, 다른 스레드에서 실행되도록 하고 싶을 경우 사용.
마찬가지로 `CompletableFuture.thenAcceptAsync`은 `CompletableFuture.thenApply`의 다른 스레드에서 동작하는 버전이다.

```java
ExecutorService es = Executors.newFixedThreadPool(10);

 CompletableFuture
  .supplyAsync(() -> {
    log.info("runAsync");
    retrun 1;
  })
  .thenApplyAsync(num -> { // 이전에서 완료된 비동기 작업의 결과를 받을 수 있다.
    log.info("thenRun {}", num);
    return num + 1; // 이렇게 값을 반환하면, 내부적으로 CompletableFuture로 값을 감싸게 된다. 즉, 이것은 map과 비슷
  }, es)
  .thenAcceptAsync(num -> {
    log.info("num {}", num);
  }, es);
```


