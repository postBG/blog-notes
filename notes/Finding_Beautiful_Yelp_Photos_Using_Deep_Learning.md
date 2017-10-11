# Finding Beautiful Yelp Photos Using Deep Learning

## 요약

간단히 정리하자면 "Yelp가 아름다운 사진을 구별하기 위해 Deep Learning을 어떻게 활용하였느냐'' 정도가 되겠음.



아름다운 사진을 구별하는 것에는 Depth와 Field, 'Rule of Third' 등등의 요소가 있는데, 이런 것 요소를 가진 사진들을 자동으로 분류하기 위해 CNN(AlexNet, GoogLeNet)을 사용함. 왜냐하면 CNN은 위치 정보를 고려할 수 있는 요소를 지니고 있기 때문.



#### 데이터의 확보 과정

질 좋은 데이터를 확보하기 위해서는 직접 engineer들이 사진을 한땀한땀 아름다운지 아닌지 Label을 달아야했는데, 이것은 아무래도 사람마다 기준이 다르기도 하고 노동력도 많이 들어가서 포기하기로 함.

다른 방법을 찾던 중에 DSLR을 이용하여 찍은 사진의 질이 좋다 판단을 하고 training데이터는 DSLR로 찍었는지 아닌지를 Label로 하여 학습을 시키기로 함. DSLR로 찍었는지의 여부는 EXIF data라는 곳에 들어있었다고 함.

그리고 테스트 데이터는 엔지니어들이 직접 아름다운지 아닌지를 확인해서 Label을 만들었는데, 아이폰같은 DSLR이 아닌 카메라로 찍은 사진들도 아름답다고 잘 평가해줬다고 함



#### Extra한 작업

Business Photo Ranking Pipeline은 위에서 만든 Quality Score에 Logo Score와 Resolution Score를 함께 사용하기로 함.

* **Logo Score:**  로고의 경우, 사용자들이 관심이 없음에도 불구하고 Quality Score가 높게 나왔기 때문에 이를 보정하는 모델을 추가로 만들어야했음. 사진의 intensity histogram의 entropy를 이용하는 classification model을 사용하여 로고 사진의 점수를 낮추는 모델을 만듦.
* **Resolution:** neural network의 입력으로 넣는데 용이하고, 계산 속도를 높이기 위해 이미지를 227 by 227로 바꾸는 작업을 하였는데, 사진의 크기가 너무 작아서 사용자들이 그 사진의 내용을 잘 이해하기 어려운 경우 점수를 낮추기 위해, 특정 크키 이하의 사진은 점수를 낮추는 작업을 수행.



#### Application

Cover photo sorting에 사용되어 아름다운 사진이 커버의 가운데 혹은 대표 사진으로 노출되도록 하였음.



## 흥미로운 점

* 데이터 확보 단계에서 DSLR 사용 여부를 사용한 것이 재기발랄하다고 느껴졌음



