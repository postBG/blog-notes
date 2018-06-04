# Reactive Streams에 대한 필기

## Duality (쌍대성)
궁극적인 기능은 똑같은데, 표현이 반대인 것. 

### Iterable
* Iterable interface를 구현하며 for-each문을 사용할 수 있게 된다.
* interface의 구현해야할 함수가 하나뿐이라면 Java, kotlin은 lambda로 변환가능.
* Iterable -> iterator() 라는 Iterator를 반환하는 함수를 구현해야하며, Iterator는 hasNext, next만을 가진다.
* for-each가 없던 경우(1.5이전),
```java
for(Iterator<Integer> it = iter.iterator(); it.hasNext(); ){
  it.next().
}
```
* Iterator <--- duality ---> Observable
  * Iterable: pull 방식, 필요할 때 내가 직접 가져온다. (it.next())
  * Observable: push 방식, 가져가라고 밀어넣어준다. (Observable은 Observer들에게 변화가 있을 경우, 그 정보를 미러넣어준다.)
  

## Observer Pattern

## Reactive Streams의 핵심 인터페이스
