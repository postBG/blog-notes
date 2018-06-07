# Reactive Streams - Operator
사용라이브러리: reactive streams

## 복습
* Publisher - Data Stream을 생성하는 역할 (Observable)
  * `subscribe`를 구현하면 됨
* Subsriber - Observer 같은 존재. publisher.subscirbe(subscriber)를 통해 등록됨
  * `onSubcribe onNext* (onError | onComplete)?`
* Subscription - Publisher에서 subscribe가 불려지면 반드시 호출해야하는 onSubscribe의 인자로 사용됨
  * 이것으 back pressure를 위한 매커니즘을 제공
  
## Operators (Transformers)
Publisher가 Subscriber에게 데이터를 넘겨주기 전에 이를 가공하고 변환하는 역할을 지닌 연산들.
`Publisher -> [Data1] -> Operator1 -> [Data2] -> Operator2 -> [Data3] -> Subscriber`
즉, 저런 flow를 정의하고 Data를 흘려보낼 수 있다.

ex)
1. map (T -> K) => Publisher이면서 Subscriber여야 함.
  ```java
  Publisher<Integer> pub = intPub();
  Publisher<Integer> mapPub = createMapPub(pub, x -> x * 10)
  mapPub.subscribe(intSub())
  ```
  
  ```java
  private static Publisher<Integer> createMapPub(Publisher<Integer> upstreamPub, Function<Integer, Integer> f) {
    return new Publisher<Integer>() {
      @Override
      public void subscribe(Subscriber<? super Integer> sub) {
        upstreamPub.subscribe(new Subscriber<Integer>() {
          @Override
          public void onSubscribe(Subscription subsription) {
            // upstream Publisher에게 받은 Subscription을 전달만해주면 됨
            sub.onSubscribe(subsription);
          }
          
          @Override
          public void onNext(Integer i) {
            sub.onNext(f.apply(i));
          }
          
          @Override
          public void onError(Throwable t) {
            sub.onError(t);
          }
          
          @Override
          public void onComplete() {
            sub.onComplete();
          }
        });
      }
    }
  }
  ```
  
  2. reduce
   ```java
   private static Publisher<Integer> createReducePub(Publisher<Integer> upstreamPub, final int initValue, final BiFunction<Integer, Integer, Integer> bf) {
    return new Publisher<Integer>() {
      @Override
      public void subscribe(Subscriber<? super Integer> sub) {
        upstreamPub.subscribe(new Subscriber<Integer>() {
          int result = initValue;      
  
          @Override
          public void onSubscribe(Subscription subsription) {
            // upstream Publisher에게 받은 Subscription을 전달만해주면 됨
            sub.onSubscribe(subsription);
          }
          
          @Override
          public void onNext(Integer i) {
            result = bf.apply(result, i);
          }
          
          @Override
          public void onError(Throwable t) {
            sub.onError(t);
          }
          
          @Override
          public void onComplete() {
            sub.onNext(result);
            sub.onComplete();
          }
        });
      }
    }
  }
   ```
