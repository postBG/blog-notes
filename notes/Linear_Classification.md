# Linear Classification

### 개요

이 글은 cs231n의 참고자료이며, Linear Classification을 다루며 *score function*과 *loss function*의 내용을 설명하지만 사실 대부분의 내용은 loss function인 **Multiclass Support Vector Machine loss**와 **Cross-entropy loss**의 개념과 차이점에 관한 것이다.

* score function: input raw data를 class score로 mapping하는 함수
* loss function: predicted score와 ground truth가 얼마나 동일한지를 나타내는 척도



### Linear Classification

#### 0. Linear Classification

![l_c](assets/Linear_Classification/linear_classifier.PNG)



#### 1. Analogy of Images as High-demensional points

image를 하나의 점으로 보면 Linear Classification의 결과는 이 점들을 나누는 직선으로 볼 수 있다. (typical한 해석이므로 더이상의 설명은 생략)

![interpret_high_dim](assets/Linear_Classification/interpret_high_dim.PNG)



#### 2. Interpretation of Linear Classifiers as template matching 

**W**의 각 row는 해당 class의 *template(=prototype)*에 해당하며 score of class를 계산한다는 것은 이 template과 data를 inner product를 통해 비교한다는 것. 

즉, 우리는 Nearest Neighbor를 template을 이용해 효율적으로 수행하는 것과 같다는 의미이다. 이때 우리가 사용하는 distance는 L1이나 L2 distance가 아니라 negative inner product이다. 

![](assets/Linear_Classification/template.PNG)

위의 사진을 보면 horse같은 경우에 양방향의 머리를 가진 말처럼 보이는데, 이는 두 방향의 말을 모두 찾기 위해서이다. ship같은 경우에도 주위가 바다인 경우가 많아 테두리가 파란색이다.



### 3. Bias trick

![](assets/Linear_Classification/bias_trick.PNG)



#### 4. Image Data Processing

이미지 데이터를 다룰 경우 zero mean centering (range [-1, 1])로 해주는 것이 좋다. 이는 이후에 배울 gradient descent의 동작을 배우면 알 수 있음.



### Loss function

* loss function = const function = object function = objective

#### 1. Multiclass Support Vector Machine loss(SVM loss)

SVM loss는 간단히 말하면 정답인 class의 score가 정답이 아닌 class의 score보다 적어도 일정 크기($$Δ$$)만큼은 더 크기를 바란다. 

좀 더 자세히 알아보도록 하자. i번째 데이터를 $$x_i$$와 $$y_i$$라고 하자. 이때 score function으로 구한 score vector를 $$s$$라고 하고 j번째 클래스의 score는 $$s_j$$라고 하자. 즉, $$s_j=f(x_i, W)_j$$이다. 그러면 i번째 데이터에 대한 SVM loss는 다음과 같이 정의된다.

> SVM loss 중 hinge loss: $$Li=∑_{j≠yi}max(0,s_j−s_{y_i}+Δ)$$



**예제**

$$s=[13,−7,11]$$이고 $$y_i=0$$, $$Δ$$=10 이라면

$$L_i=max(0,−7−13+10)+max(0,11−13+10)$$

이때, 첫번째 term의 경우 $$s_1$$와 $$s_0$$의 차이가 $$Δ$$보다 더 크기 때문에 penalty가 0이 되지만 두번째 term의 경우 $$s_2와 s_0$$의 차이가 $$Δ$$보다 더 작기 때문에 penalty를 받게 된다.



간단히 말해 SVM loss는 incorrect class의 score가 correct class의 score보다 적어도  $$Δ$$만큼은 작기를 바란다.

참고로 hinge loss와는 다르게 $$max(0, -)^2$$으로 계산하는 squared hinge loss도 존재.



#### Regularization

모든 example에 대해 제대로 classify를 수행하는 $$W$$를 찾았다고 하자. 그런데 이 $$W$$는 unique하지 않다. 예를 들어 $$λW where λ>1$$이면 전부 제대로 classify한다고 볼 수 있다. 왜냐면 $$max(0, λ * -)$$이므로.

그렇다면 많은 $$W$$중에 어떤 것이 가장 좋을 것일까? loss function을 디자인할 때, 이런 preference도 **Regularization penalty** $$R(W)$$로 포함해주게 된다. 가장 유명한 것은 아래와 같은 **L2 norm**

$$R(W)=∑_k∑_lW_{k,l}^2$$

보면 알다시피 이런 Regularization loss는 데이터와는 상관없이 오직 $$W$$와만 관계된다.



위의 Regularization을 고려하면 **full SVM loss**는 아래와 같다.

![full_svm_loss](assets/Linear_Classification/full_svm.PNG)

혹은 풀어쓰면

![full_svm2](assets/Linear_Classification/full_svm2.PNG)

Regularization을 사용하면 부가적으로 아래와 같은 특징이 생긴다.

* penalizing을 하는 것 만으로 generatlization 능력이 증가한다. 예를 들어, $$x=[1, 1, 1,1]$$이고 $$W_1=[1, 0, 0, 0]$$이고 $$W_2=[0.25, 0.25, 0.25, 0.25]$$가 있다면 inner product를 하면 같은 결과를 내므로 data loss는 같지만 regularzation loss는 $$W_1$$이 더 크므로 우리는 $$W_2$$를 선호하게 된다. 이는 하나의 feature에 너무 큰 가중치를 주지 않고 모든 feature들을 두루 참고한다는 뜻이다.
* 실전에서는 큰 차이가 없지만 bias는 원래 penalize 대상이 아니다. 이 값들은 input demension(feature)의 strength에는 영향을 미치지 못하기 때문이다.
* 마지막으로 모든 weight가 0이 될 수는 없으므로 loss는 결코 정확히 0이 될 수 없다.



#### Practical Consideration

1. **Setting Delta**
   결론적으로 말하자면 $$Δ$$는 그냥 1.0으로 두고, $$λ$$ 만 테스트 해보면 된다. 왜냐하면, $$Δ$$와 $$λ$$는 서로 독립적으로 보이지만 사실은 trade-off관계에 있기 때문. 즉, data loss와 regularization loss사이에는 trade off가 있기 때문에 하나는 고정해두고($$Δ$$), 나머지 하나만 조절하면 된다($$λ$$). 둘 사이의 trade-off 관계를 이해하려면 $$W$$를 생각해보면 된다. $$λ$$를 줄이면 $$W$$의 크기가 커지므로, $$W$$는 score에 영향을 미치며, score 차이가 커지게 되므로 $$Δ$$ 는 커져야한다.
2. **Relation to Binary SVM**
   당연하게도 그냥 Binary SVM은 우리가 논의한 것의 특수형이다.
3. **Aside: Optimization in primal**
   그냥 kernel이나 max함수 같은 것을 사용해서 미분불가능하다고 슬퍼하지 말 것. subgradient를 사용하는 경우는 허다하다.
4. **Aside: Other Multiclass SVM formulations**
   다른 형태(예를 들면, One-vs-All)의 SVM formulation이 많이 있다는 것도 알아두자.




#### 2. Cross-entropy loss

#### Softmax Classifier

Cross entropy loss는 Softmax Classifier에서 사용된다. Softmax Classifier는 위에서 본 SVM이 output을 score로 다뤘던 것과는 달리 output은 normalized class probabilities로 해석한다. 

즉, 우리가 지금까지 $$ f(x_i, W) = Wx_i$$라는 식은 변함이 없지만, Softmax Classifier는 이를 unnormalized log probability로 해석한다. 또한 여기서는 *hinge loss*대신 **cross-entropy loss**를 사용하게 된다.



> $$L_i=−log(e^{f_{y_i}} / ∑_je^{f_j})$$  or equivalently $$L_i=−f_{y_i}+log∑_je^{f_j}$$
> $$f_j$$는 score vector $$f$$의 i번째 element를 뜻한다.



전체 loss는 $$L_i$$를 평균내고 $$R(W)$$와 합쳐주면 된다.

**softmax function**은 아래와 같다.

> $$f_j(z) = e^{f_j} / ∑_ke^{j_k}$$

이 함수는 실수 벡터 $$z$$를 입력받아 각 element가 0~1사이의 값을 가지고, 전체 element의 합이 1이 되는 벡터로 변환한다.



#### Interpretation of Cross-entropy loss

cross-entropy loss를 해석하는 방법 중 다음 2가지를 소개한다.

#### 1. Information theory view

"true" distirbution $$p$$와 estimated distribution $$q$$ 사이의 cross-entropy의 정의는 다음과 같다.

> $$H(p, q) = - ∑_x p(x) log q(x)$$

따라서 위에서 본 cross-entropy loss는 이 관점에서 우리가 예측한 class의 distribution과 실제 class들의 distribution의 cross-entropy와 같고, 이것을 줄이는 것이 우리의 목적이다.

또 위의 식은 Kullback-Leibler divergence를 이용하여 아래와 같이 표시되는데,

> $$H(p,q)=H(p)+D_{KL}(p||q)$$

이 또한 두 distribution 사이의 차이를 줄여야하는 것을 의미한다.



#### 2. Probabilistic interpretation

앞에서 score vector를 unnormalized log probability로 본다고 했었는데 그렇기 때문에 아래의 식이 성립한다.

> $$P(y_i∣x_i;W) = e^{f_{y_i}} / ∑_je^{f_j}$$

이 관점에서 본다면 우리가 cross-entropy loss를 낮추는 것은 correct class의 negative log likelihood를 낮추는 것과 같다. 즉, 이는 *Maximul Likelihood Estimation(MLE)*를 하는 것과 같다.



#### Practical Issue: Numerical stability

cross-entropy 혹은 softmax 함수를 이용할 때, exponential을 사용하므로 값이 커지면서 수치적으로 불안정해질 수 있다. 따라서 다음의 관계식을 이용하여 수치안정성을 보장하는 방법이 사용된다.

> $$e^{f_{y_i}} / ∑_je^{f_j} = Ce^{f_{y_i}} / C∑_je^{f_j} =e^{f_{y_i} + logC} / ∑_je^{f_j + logC}$$

이때 C는 주로 $$logC = -max_jf_j$$로 선택한다.



### SVM vs. Softmax

![svm_softmax](assets/Linear_Classification/svm_softmax.PNG)

**In practice, SVM and Softmax are usually comparable**

SVM과 Softmax의 성능은 사실 큰 차이가 없지만, SVM이 좀 더 *local*한 objective이다. 아래의 예를 보자.

만일, $$Δ$$ = 1.0이고 label = 0이 correct label이라고 하자.

SVM은 score vector가 [10, -100, -100]이거나 [10, 9, 9]거나 별 신경쓰지 않는다. 즉, 두 벡터의 penalty는 0으로 같다.

반면 Softmax의 경우, [10, 9, 9]가 훨씬 큰 penalty를 받는다. 

다시 말해, Softmax Classifier는 자신의 score vector에 결코 만족하지 않지만, SVM은 margin만 확보한다면 불만을 가지지 않는다.


