---
layout: post
title: "Autoregressive 모형 기초개념"
tags: [시계열분석]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://datascienceschool.net

#### 1.  Autoregressive 모델 개요

![0](https://user-images.githubusercontent.com/41605276/58713359-5787e880-83fd-11e9-9894-cb9ff5017d43.jpg)

#### 2. AR(1) 모델

![1](https://user-images.githubusercontent.com/41605276/58713374-61a9e700-83fd-11e9-9471-130b2150b4fd.jpg)

![2](https://user-images.githubusercontent.com/41605276/58713385-679fc800-83fd-11e9-9b52-ac11146f93e1.jpg)

![3](https://user-images.githubusercontent.com/41605276/58713396-6cfd1280-83fd-11e9-985f-b4e47fc7a7e3.jpg)


```python
%matplotlib inline
%config InlineBackend.figure_format = 'retina'

lag = np.arange(12)

plt.subplot(221)
acf = 0.9 ** lag
plt.stem(acf)
plt.xlim(-0.2, 11.2)
plt.ylim(-0.1, 1.1)

plt.subplot(222)
acf = 0.4 ** lag
plt.stem(acf)
plt.xlim(-0.2, 11.2)
plt.ylim(-0.1, 1.1)

plt.subplot(223)
acf = (-0.8) ** lag
plt.stem(acf)
plt.xlim(-0.2, 11.2)
plt.ylim(-1.1, 1.1)

plt.subplot(224)
acf = (-0.5) ** lag
plt.stem(acf)
plt.xlim(-0.2, 11.2)
plt.ylim(-1.1, 1.1)

plt.suptitle("example of auto-correlation function of AR(1) model")
plt.show()
```


<img width="377" alt="Autoregressive 모형 기초개념_6_0" src="https://user-images.githubusercontent.com/41605276/58713405-77b7a780-83fd-11e9-82cf-a2e906e0d7b7.png">


![4](https://user-images.githubusercontent.com/41605276/58713416-7edeb580-83fd-11e9-852c-5a899edc803c.jpg)

k-시차에 따라서  Yt 와  Yt−k는 지속적으로 강한 상관관계를 보인다. 그러다 점점 차수가 지날수록 서서히 작아진다.

#### 3. AR(2) 모형

![5](https://user-images.githubusercontent.com/41605276/58713426-856d2d00-83fd-11e9-9352-b1949237ba5d.jpg)

![6](https://user-images.githubusercontent.com/41605276/58713434-8b630e00-83fd-11e9-8f79-4cc5be2f9acb.jpg)


```python
import statsmodels.api as sm

plt.subplot(221)
p1 = sm.tsa.ArmaProcess([1, -0.5, -0.25], [1])
plt.stem(p1.acf(11))
plt.xlim(-0.1, 10.1)

plt.subplot(222)
p1 = sm.tsa.ArmaProcess([1, -1, 0.25], [1])
plt.stem(p1.acf(11))
plt.xlim(-0.1, 10.1)

plt.subplot(223)
p1 = sm.tsa.ArmaProcess([1, -1.5, 0.75], [1])
plt.stem(p1.acf(11))
plt.xlim(-0.1, 10.1)

plt.subplot(224)
p1 = sm.tsa.ArmaProcess([1, -1, 0.6], [1])
plt.stem(p1.acf(11))
plt.xlim(-0.1, 10.1)

plt.suptitle("example of auto-correlation function of AR(2) model")
plt.show()
```


<img width="379" alt="Autoregressive 모형 기초개념_11_0" src="https://user-images.githubusercontent.com/41605276/58713458-91f18580-83fd-11e9-8e29-220cfdfd6ff9.png">

