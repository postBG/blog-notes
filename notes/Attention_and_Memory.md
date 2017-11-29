# Attention and Memory in Deep Learning and NLP

최근에 딥러닝에서 인기있는 Attention에 대해 고찰해보는 글. 개인적으로 매우 재미있게 잘 쓰여진 글이라고 생각됨.

## Attention은 어떤 문제를 해결하는가?

이 글은 간단히 NMT에서 그 유용성을 도출해낸다. 아래는 전통적인 NMT 모델(seq2seq)의 모습.

![RNN for Machine Translation](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/09/Screen-Shot-2015-09-17-at-10.39.06-AM-1024x557.png)

여기서 Decoder는 **h3**벡터에만 의존하여 문장을 생성해낸다. 즉, **h3**벡터에 source sentence의 모든 정보가 잘 embedding되어 있어야 한다. 하지만 English-German 같은 어순이 비슷한 경우는 첫번째 단어를 생성할 때, 원문의 첫번째 단어를 참고하는 것이 더 유리하다. 하지만 RNN(LSTM이 이론적으로 기억력이 좋다고 해도)은 저런 어순 정보까지 보존하지는 못한다. 이는 어순이 비슷한 언어를 번역할 때 단순히 원문을 뒤집는 것이 정확도를 높이는 이유를 설명해주기도 하는데, 아무튼 이런 hacking을 통한 성능향상 말고 제대로 문제를 풀기 위해 등장한 것이 Attention이다. 



## Attention and Memory

[Neural Machine Translation by Jointly Learning to Align and Translate](http://arxiv.org/abs/1409.0473)이라는 논문에서 위의 문제를 풀기 위해 Attention을 도입하였는데 아래의 그림을 보자.

![NMT Attention](http://d3kbpzbmcynnmx.cloudfront.net/wp-content/uploads/2015/12/Screen-Shot-2015-12-30-at-1.16.08-PM-235x300.png)

이 그림에서 **yt**를 생성하기 위해 **h1**에서 **hT**를 가중치를 두고 참고하는 것을 볼 수 있다. 즉, **yt**는 **weighted combination of all the input states**에 의존하고 있다. 만약 **a_t3**이 큰 수를 가진다면 이는 **yt**를 생성할 때, 3번째 input states를 중요하게 참고한다는 의미이다.



한편, 이 글의 필자는 이것이 진정한 의미의 Attention이 아니라 memory를 retreive하는 것이라고 주장하는데 근거는 다음과 같다. 인간의 Attention은 사실 인지기관의 cost를 줄이고 필요한 곳에 집중하기 위함이지만 위와 같은 Attention은 사실 많은 Computational Cost가 발생하기 때문이다. 또한 전체 states를 훏으면서 중요한 것들을 참고하므로 차라리 memory에 가깝다는 것이다.~~(인정)~~



아무튼 위와 같은 Attention은 NMT뿐만 아니라 다른 분야들에도 적용되고 있다. 유명한 것으로는 Show and Tell.