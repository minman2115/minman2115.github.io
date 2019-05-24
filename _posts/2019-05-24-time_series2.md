---
layout: post
title: "정상확률과정과 비정상확률과정 기초개념"
tags: [시계열분석]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://datascienceschool.net

#### 1. Stationary process(정상확률과정)

![1](https://user-images.githubusercontent.com/41605276/58334111-d0c19180-7e79-11e9-931d-40c6f2dc0fc2.jpg)

#### 2. 에르고딕 성질

![2](https://user-images.githubusercontent.com/41605276/58334123-d7500900-7e79-11e9-8d0a-375a3e438bae.jpg)

#### 3. Non-stationary process(비정상확률과정)

![3](https://user-images.githubusercontent.com/41605276/58334136-dcad5380-7e79-11e9-8575-103ccd5aec2f.jpg)

- 비정상확률 예시


```python
%matplotlib inline
%config InlineBackend.figure_format = 'retina'

df = sm.datasets.get_rdataset("CanPop", package="carData").data
df.plot(x="year", y="population")
plt.xlabel("year")
plt.ylabel("population")
plt.title("population of canada by time")
plt.show()
```


<img width="396" alt="정상확률과정과 비정상확률과정 기초개념_7_0" src="https://user-images.githubusercontent.com/41605276/58334150-e33bcb00-7e79-11e9-86ee-20305afe229f.png">


![4](https://user-images.githubusercontent.com/41605276/58334158-e8991580-7e79-11e9-825f-9717f5a7a213.jpg)


```python
%matplotlib inline
%config InlineBackend.figure_format = 'retina'

N = 500
t1 = 100
t2 = 400
t = np.arange(N)

np.random.seed(12)
y1 = np.insert(np.cumsum(sp.stats.norm.rvs(size=N-1)), 0, 0)
np.random.seed(18)
y2 = np.insert(np.cumsum(sp.stats.norm.rvs(size=N-1)), 0, 0)
np.random.seed(22)
y3 = np.insert(np.cumsum(sp.stats.norm.rvs(size=N-1)), 0, 0)
np.random.seed(24)
y4 = np.insert(np.cumsum(sp.stats.norm.rvs(size=N-1)), 0, 0)

plt.subplot(211)
plt.plot(t, y1)
plt.plot(t, y2)
plt.plot(t, y3)
plt.plot(t, y4)
plt.plot(t1, y1[t1], 'o', markersize=5)
plt.plot(t2, y1[t2], 'o', markersize=5)
plt.plot(t1, y2[t1], 'o', markersize=5)
plt.plot(t2, y2[t2], 'o', markersize=5)
plt.plot(t1, y3[t1], 'o', markersize=5)
plt.plot(t2, y3[t2], 'o', markersize=5)
plt.plot(t1, y4[t1], 'o', markersize=5)
plt.plot(t2, y4[t2], 'o', markersize=5)

plt.subplot(212)
plt.grid(False)
plt.plot(t, sp.stats.norm(t1, 0.08*t1).pdf(t))
plt.plot(t, sp.stats.norm(t2, 0.08*t2).pdf(t))

plt.show()
```


<img width="383" alt="정상확률과정과 비정상확률과정 기초개념_9_0" src="https://user-images.githubusercontent.com/41605276/58334171-ee8ef680-7e79-11e9-82ba-918c6e45b542.png">

