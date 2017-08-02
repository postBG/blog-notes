# Understanding Xavier Initialization In Deep Neural Networks

## 요약

적절한 weight initialization은 학습할 때, signal의 explosion이나 vanishing을 방지하게 해주므로 중요하다.

우리는 network를 학습시킬 때, 각 layer를 통과하면서 variance가 일정하기를 바라는데, 이것이 곧 signal의 explosion이나 vanishing을 방지해주기 때문이다. 

이를 가능하게 해주는 weight initialization 방법이 바로 Xavier인데, 이 글에서는 linear neuron(즉, activation function이 없는 경우)로 예를 들어 Xavier의 원리를 유도하고 구현하는 방법을 설명하는데, 결론적으로 말하면 Xavier의 실제 구현은 input neuron들의 weight들의 variance가 1 / (# of input neurons)가 되도록 함으로써 위에서 말한 각 layer를 통과해도 variance를 일정하게 유지한다는 목적을 달성한다.

Xavier의 원리를 유도할 때, 가장 핵심이 된 가정은 각 input과 weight가 zero mean을 가지는 가우시안으로부터 추출된 것이라는 것이었다.



## 기술적인 면에서 새로 배운 것

* Xavier라는 것의 존재를 처음 알았음. GAN을 학습시킬 때, 명시적으로 initializer로 사용하면서 알게 되었음.
* 사용법은 tf.contrib.layers.xavier_initializer()로 생성해서 kernel_initializer 같은 키워드 변수에 넘겨주면 된다.



## 앞으로 할 일

* 사실 정확히 왜 variance가 일정하면 layer를 통과해도 vanishing이나 explosion같은 문제가 줄어드는지 정확히 모르겠어서 그 부분을 좀 찾아봐야할 것 같다.

