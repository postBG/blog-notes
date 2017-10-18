# Class Imbalance Problem

###  요약

Imbalance problem이란 positive의 수가 negative로 label된 데이터의 수보다 압도적으로 작은 경우를 말한다. (ex, 5:95의 비율)

이런 문제에서는 다른 classification처럼 accuracy를 측정하는 것은 사실상 의미가 없는데, 무조건 negative라고 predict하는 model의 경우도 95%의 정확도를 가지기 때문이다. 또한 학습시키는 것도 어렵다. 이런 문제를 해결하기 위해 아래와 같은 방법들을 사용하고 있다고 한다.

1. 모델 성능의 측정 방법을 변경한다. 주로 prediction, recall, F score 등을 사용하면 모델을 accuracy보다 더 잘 측정할 수 있다.
2. 모델을 더 잘 학습시키기 위해서는 아래와 같은 방법들을 사용한다.
   * Cost function을 변형한다. 예를 들면, 서비스적 측면에서 false positive를 발생하지 않도록 하는 것이 더 중요하다면 그곳에 더 큰 cost를 주는 방식으로 cost function을 수정할 수 있다.
   * 데이터가 충분할 경우 undersampling을 사용한다. 이는 negative의 갯수가 압도적으로 많으므로 negative를 조금만 뽑아 positive와 negative의 수를 비슷하게 맞춰주는 방식이다. 이 방식은 중요한 data들이 소실될 가능성이 존재하는 방식이다.
   * 데이터가 부족할 경우 oversampling을 사용한다. 단순히 positive 데이터를 중복시키는 것은 오히려 decision boundary를 심하게 왜곡하는 문제가 존재하기 때문에, SMOTE 같은 synthetic 기반의 oversampling이 많이 사용된다.
3. Test 데이터의 경우, Stratified sampling이라는 방식을 사용하면 테스트 데이터 내에 positive가 아예 없거나 너무 적은 경우를 방지할 수 있다.
4. 가능한 경우 Anomaly Detection문제로 변형하여 푸는 방식도 있다는데, 정확히는 모르겠다.





