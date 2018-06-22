# [Java Garbage Collection](https://d2.naver.com/helloworld/1329)

자바는 개발자가 직접 메모리를 할당하는 방식의 언어가 아닌데(이 메모리를 직접 할당하지 않는다는 건, C언어처럼 malloc과 free, 포인터처럼 메모리를 직접 다루는 매커니즘을 제공하지 않는다는 의미이다.)
그렇기 때문에 메모리 관리를 해줘야하는 메커니즘이 필요하고 이것이 Garbage Collector이다.

물론 자바에서도 신경써서 코드를 작성하지 않으면 메모리 누수를 만들 수 있다.

Garbage Collector가 동작하는 방식에 대해 적당히(?) 이해해보자.

## Stop-the-world
Stop-the-world란 GC를 하는 시점에, GC를 수행하는 스레드를 제외하고 다른 모든 실행중인 스레드를 멈추는 것이다.
이는 당연하게도 GC를 실행하는 시점에 참조 상태가 변화하는 것을 막기 위함이다.

한편 이는 어플리케이션의 성능에 안좋은 성능을 줄 수 있기 때문에, GC tuning이라함은 결국 stop-the-world상태에 있는 시간을 최소화하는 것을 의미한다.

## GC를 지탱하는 2가지 전제 (Weak Generational Hypothesis)
**Weak Generational Hypothesis**
  1. 대부분의 객체는 금방 unreachable해진다.
  2. 오래된 객체에서 젋은 객체로의 참조는 매우 적게 존재한다.
      * 직관적으로 이미 만든 객체의 속성에 새로 만든 객체를 넣는 경우(setter를 사용하는 경우)는 별로 없다는 의미


## Young 영역의 구성
### Young 영역은 3개의 영역으로 다시 구분된다
* Eden 영역
* Survivor 영역 2개

### Young 영역에서의 객체 처리 순서
1. 새로 생성된 객체는 먼저 Eden 영역으로 위치된다.
2. Minor GC가 발생했을 때, 객체들은 다음과 같이 이동한다.
  * Eden영역에서 한번 살아남은 객체는 Survivor 영역 중 하나로 이동한다. (가득 찰 때까지 하나의 Survivor영역만을 사용)
  * 그러면 해당 Survivor 영역은 Minor GC가 발생할 때마다 공간이 줄어들게 된다.
  * 이 Survivor 영역이 가득차게 되면, 나머지 Survivor 영역으로 객체를 전부 이동시킨다.
  * 그리고 이런 과정 중에서 일정 횟수 이상으로 살아남은 객체는 Old 영역으로 promotion된다.

![Young 영역](https://d2.naver.com/content/images/2015/06/helloworld-1329-3.png)

2개의 Survivor 영역은 서로 배타적으로 사용되어야 한다. 
즉, 둘다 사용량이 0이거나 둘 다 사용량이 0보다 크면 시스템에 문제가 있는 것이다.

#### HotSpot VM에서 빠른 메모리 활동을 위한 2가지 기술
1. bump-the-pointer
    * 그냥 Eden 영역이 가득찼는지 아닌지를 확인하기 위한 기술
    * Eden 영역에 객체를 차례로 쌓으면서 가장 마지막에 추가된 객체의 포인터만을 추적한다.
    * 새로운 객체에 메모리를 할당해야한다면, 마지막 객체를 추적하고 있는 포인터에서 Eden 영역의 끝부분까지의 남은 영역 크기를 검사해서 충분한 크기가 있다면 객체를 할당한다,
2. TLABs(Thread-Local Allocation Buffers)
    * bump-the-pointer 같은 경우에 멀티 스레드 환경에서는 검사시점과 할당시점의 차이가 생기기 때문에 문제가 발생할 여지가 존재한다.
    * 이를 해결하기 위해 bump-the-pointer는 lock을 발생시킬 수 밖에 없고, 이는 영 좋지 않다.
    * 이를 해결하기 위한 매커니즘을 가진 것이 TLABs이다.
    * 간단한 방식인데, 그냥 Thread 마다 Eden 영역의 일정 범위를 할당하는 것이다.
    * 이렇게 하면 서로 간섭을 일으키지 않으면서 bump-the-pointer를 사용할 수 있다.
  

