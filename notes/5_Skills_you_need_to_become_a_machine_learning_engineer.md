# 5 Skills You Need to Become a Machine Learning Engineer

위의 글에 대한 요약과 나의 생각이 섞여있음.



### Data Analyst vs. Machine Learning Engineer

* 데이터 분석가
  * 결과물을 사람이 보고 비즈니스적 결정을 내리기 때문에 visualization 등이 중요
* 머신러닝 엔지니어
  * 일단 결과물부터가 working software이다. (ex) coupon generating system
  * 결과물을 주로 다른 소프트웨어 서비스가 사용하기 때문에 소프트웨어적인 능력이 중요



### Understanding The Ecosystem

*  머신러닝 엔지니어의 산출물은 working software이기 때문에, 알고리즘적인 측면보다는 전체 시스템을 잘 이해하고 다른 소프트웨어나 서비스에 잘 통합될 수 있는 인터페이스 설계와 같은 측면이 중요함
* 또한, 훌륭한 머신러닝 시스템을 만들기 위해서는 비즈니스 도메인에 대한 폭넓은 이해가 바탕이 되야함



## Summary of Skills



### 1. Computer Science Fundamentals and Programming

* 자료구조, 알고리즘, Complexity, 컴퓨터 아키텍쳐 등에 대한 지식을 아는 것이 중요
* 해커톤 같은 곳에 참여하고 연습 ㄱㄱ



### 2. Probability and Statistics

* conditional probability, Bayes rule, likelihood, independence 와 같은 확률의 성질을 이해하고
* 그런 것들로부터 유도된 기술들 (Bayes Nets, Markov Decision Processes, Hidden Markov Models, etc.)을 이해해야 함.  많은 머신러닝 알고리즘들의 핵심이기 때문.



### 3. Data Modeling and Evaluation

* 데이터 모델링이란 데이터의 underlying structure를 이해하고, 유용한 패턴과 속성을 발견하는 과정을 말한다.
* 만들고 있는 모델을 개선시키기 위해서나 현재 모델의 질을 평가하기 위해서 Evaluating Measure 등을 잘 선정하고 해석할 수 있어야 함
* 특히 딥러닝처럼 error를 모델 개선에 바로 사용하는 경우도 있으니 더욱 그러함



### 4. Applying Machine Learning Algorithms and Libraries

* 여러 모델간의 장단점을 이해하고 목적에 맞는 적절한 모델을 선택할 수 있어야 함.
* 또한 hyperparameter들이 모델에 어떤 영향을 미칠 수 있는지 이해해야 함.
* Kaggle 같은 곳에서 훈련해보길 권함



### 5. Software Engineering and System Design

* 머신러닝 프로젝트의 결과물은 소프트웨어이기 때문에, 소프트웨어 엔지니어링에 대한 지식의 중요성은 매우 높다.
* 다른 시스템들이 어떻게 서로 연동되고 동작하는지를 잘 이해하고 있어야하며(ex, RESTful API), 자신의 소프트웨어 컴포넌트(=머신러닝의 결과물)가 병목지점이 되서는 안되게 scaleability에 대한 고려도 필요하다.
* 또 모듈을 나누고, 인터페이스를 디자인 하는 등의 능력은 다른 사람과의 협업, 다른 소프트웨어와의 통합, 유지보수 모두에 중요한 요소이다.



![아름다운 사진](/assets/5skillsML/ML-graph.png)

[사진출처: https://blog.udacity.com/2016/04/5-skills-you-need-to-become-a-machine-learning-engineer.htㅡl]





###  