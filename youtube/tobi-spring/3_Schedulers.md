# Reactive Streams - Schedulers

Publisher와 Subscriber는 같은 스레드에서 돌아가도록 만들지 않는다.
둘다 같은 스레드에서 동작하면 Publisher가 이벤트를 모두 publish할 때까지 blocking이 되기 때문.

주로 Publisher는 다른 백그라운드 스레드에서 일어나도록 만드는 경우가 많다.

## Scheduler - Flux's subscribeOn
```java
public final Flux<T> subscribeOn(Scheduler scheduler)
```

위의 함수는 주로 느린 publisher와 빠른 subcriber(= consumer)사이에 사용된다. 
이는 느린 publisher를 별도의 스레드로 분리하여, 이 느린 publisher에 시스템 전체가 block되지 않도록 만들기 위함이다.

```java
flux.subscribeOn(Schedulers.single()).subscribe();
```

이렇게 하면 예측할 수 없는 이벤트를 publish하는 Publisher에게 blocking되지 않도록 만들 수 있다.
위의 함수의 실제 작동 원리는 아래와 같다.

```java
// 기존의 pub

Publisher<Integer> subOnPub = sub -> {
  ExecutorService es = Executors.newSingleThreadExecutor(); // 스레드 풀의 스레드 수가 1개인 풀을 생성
  es.execute(() -> pub.subscribe(sub)); // 별도의 스레드에서 동작하면서 동시에 기존의 pub과 sub을 연결 = Scheduler의 역할
};

// 기존의 sub
subOnPub.subscribe(sub);
```

반대로, 느린 subscriber와 빠른 publisher에서는 subscriber를 다른 스레드로 분리하여 blocking되지 않도록 만들어야한다.
이때 사용하는 함수는 `publishOn`이라는 함수이다.

```java
public final Flux<T> publishOn(Scheduler scheduler)
```

위의 함수의 실제 동작원리는 아래와 같다.

```java
// 기존의 pub

Publisher<Integer> pubOnSub = sub -> {
  pub.subscribe(new Subscriber<Integer>() {
    ExecutorService es = Executors.newSingleThreadExecutor();
    
    @Override
    public void onSubscribe(Subscription s) {
        // 기존의 pub은 데이터를 빠르게 publish할 수 있으므로, 그 부분까지 main스레드가 처리하도록 하고, onNext, onError, onComplete은 별도의 스레드에서 동작하도록 변경
      sub.onSubscribe(s); 
    }
    
    @Override
    public void onNext(Integer i) {
      es.execute(() -> sub.onNext(i));
    }
    
    @Override
    public void onError(Throwable t) {
      es.execute(() -> sub.onError(t));
    }
    
    @Override
    public void onComplete() {
      es.execute(() -> sub.onComplete());
    }
  });
}

// 기존의 sub
pubOnSub.subscribe(sub);
```

## Reactor - Flux's Scheduler

```java
Flux.range(1, 10)
  .log()
  .subscribeOn(Schedulers.newSingle("sub"))
  .subscribe(System.out::println)
  
System.out.println("exit") // 이게 가장 먼저 출력되고, sub-1 thread가 1, 2, ...을 만들어냄
```

