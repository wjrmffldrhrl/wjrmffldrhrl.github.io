---
title:  "딥러닝 공부 1"
categories:
  - Study
tags: 
  - Deep Learning
  - Machine Learning
  - AI
header:
  image: /assets/images/study/study_deep.PNG
  teaser: /assets/images/study/study_deep.PNG

---  
딥러닝 기초 공부하기 시작.

# 책 소개  

![book]({{ site.baseurl }}/assets/images/study_deep.PNG)  

최근 머신러닝을 공부하기 위해 구입한 책이다.  

[구입경로](http://book.interpark.com/product/BookDisplay.do?_method=detail&sc.prdNo=263500510&gclid=Cj0KCQiAl5zwBRCTARIsAIrukdMzUMhc3xu_AfcI4Ah5AR6SAmcbHOM573wgcaKCfSI1VojyYPdmfWYaArrvEALw_wcB)

이 책에서는 아래와 같은 부분을 다룬다.  

- 라이브러리의 사용 최소화
- 간단한 기계학습에서 정확한 이미지 인식까지  
- **오차역전파법**, **합성곱연산**을 쉽게 이해
- 하이퍼파라미터 결정방식, 가중치 초기값 등 딥러닝을 활용하는데 도움이 되는 기술 소개  
- 배치 정규화, 드롭아웃, Adam 등 트렌드 소개
- 자연어 처리, 음성인식은 다루지 않는다.  

책 초반에는 파이썬의 문법을 소개하고 몇가지 라이브러리의 사용법을 익힌다.  

나는 파이썬의 기초는 넘기고 많이 사용해보지 못한 넘파이를 실습해봤다.  

# Python Library

## 넘파이  

딥러닝을 구현하다 보면 배열이나 행렬 계산이 많이 필요한데 이때 사용되는 것이 **넘파이**이다. 

~~~python
import numpy as np

x = np.array([1.0,2.0,3.0])
y = np.array([1,0,2.0,3.0])

print(x+y)
~~~
[2. 4. 6.]  

이처럼 행렬끼리의 계산을 하거나 스칼라 값과의 연산이 가능하다.  

~~~python
x * 10
~~~
array([10., 20., 30.])  

위와 같은 방식을 브로드캐스트라 한다.  

브로드캐스트는 계산되는 행렬의 형상에 맞게 스칼라 값이 확대되어 계산하는 것을 뜻한다.  

스칼라 뿐만 아니라 행렬과 행렬의 계산에서도 형상의 확장이 일어날 수 있다.  

## matplotlib  

그래프 작성 라이브러리로써 데이터 시각화에 용이하며 이미지 로드도 가능하다.  

~~~python 
import numpy as np
import matplotlib.pyplot as plt

x = np.arange(0,10,0.1)
y = np.sin(x)

plt.plot(x,y)
plt.show()
~~~
![plot1]({{ site.baseurl }}/assets/images/study/study_deep_1/plot1.PNG)  
**사인 그래프 출력**  


~~~python  
x = np.arange(0,6,0.1)
y1 = np.sin(x)
y2 = np.cos(x)

plt.plot(x,y1,label='sin')
plt.plot(x,y2,linestyle = '--',label='cos')
plt.xlabel('x')
plt.ylabel('y')
plt.title('sin & cos')
plt.legend()
plt.show

~~~
![plot2]({{ site.baseurl }}/assets/images/study/study_deep_1/plot2.PNG)  
**그래프의 제목과 x,y축 이름 등등 그래프 정보**  

~~~python
from matplotlib.image import imread

img = imread('hi_cat.jpg')
plt.imshow(img)
plt.show
~~~
![plot3]({{ site.baseurl }}/assets/images/study/study_deep_1/plot3.PNG)  

파이썬에 대한 설명은 여기까지 했고 딥러닝의 기초를 시작 했다.

# 퍼셉트론 Perceptron  

신경망의 기원이 되는 알고리즘이다.  

다수의 신호를 입력으로 받아 하나의 신호를 출력하는 것이다.  

여기서 신호는 흐름이 있는 것을 상상하면 된다. ex) 전류, 강물  

퍼셉트론은 흐름을 만들고 정보를 앞으로 전달하며 흐른다, 흐르지 않는다 두가지 값을 가진다.  

흐른다는 1, 흐르지 않는다는 0으로 이해하면 된다.  

![perceptron1]({{ site.baseurl }}/assets/images/study/study_deep_1/perceptron1.png)  

여기서 x1, x2는 입력 w1, w2는 가중치 y는 출력이다.  

입력신호가 뉴런에 보내질 때 각각의 고유한 가중치가 곱해지며 뉴런에서 보내는 신호의 총합이 정해진 한계를 넘길 때 1을 출력한다.  

여기서 정해진 한계를 임계값이라 부르며 θ를 사용한다.  

![perceptron2]({{ site.baseurl }}/assets/images/study/study_deep_1/perceptron2.png)  

**퍼셉트론 동작 수식**  












