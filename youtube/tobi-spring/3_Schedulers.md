# Reactive Streams - Schedulers

Publisher와 Subscriber는 같은 스레드에서 돌아가도록 만들지 않는다.
둘다 같은 스레드에서 동작하면 Publisher가 이벤트를 모두 publish할 때까지 blocking이 되기 때문.

주로 Publisher는 다른 백그라운드 스레드에서 일어나도록 만드는 경우가 많다.
