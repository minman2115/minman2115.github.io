---
layout: post
title: "시계열 데이터와 확률과정 기초개념"
tags: [시계열분석]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://datascienceschool.net

#### 1. 확률과정

![1](https://user-images.githubusercontent.com/41605276/58328698-b84b7a00-7e6d-11e9-8dab-17181e130e26.jpg)


```python
from matplotlib.patches import ConnectionPatch
%matplotlib inline

t = np.arange(10)
np.random.seed(99)
y1 = np.insert(np.cumsum(sp.stats.norm.rvs(size=9)), 0, 0)
y2 = np.insert(np.cumsum(sp.stats.norm.rvs(size=9)), 0, 0)
y3 = np.insert(np.cumsum(sp.stats.norm.rvs(size=9)), 0, 0)
y4 = np.insert(np.cumsum(sp.stats.norm.rvs(size=9)), 0, 0)

ax1 = plt.subplot(4, 1, 1)
ax1.plot(t, y1, '-o')
ax1.set_xticklabels([])
ax1.set_yticklabels([])
ax1.set_ylim(-9, 9)
ax1.set_zorder(2)
ax1.set_ylabel("sample 1").set_rotation(0)
ax1.yaxis.set_tick_params(pad=30)

plt.title("sample of Random process")

ax2 = plt.subplot(4, 1, 2)
ax2.plot(t, y2, '-o')
ax2.set_xticklabels([])
ax2.set_yticklabels([])
ax2.set_ylim(-9, 9)
ax2.set_zorder(1)
ax2.set_ylabel("sample 2").set_rotation(0)
ax2.yaxis.set_tick_params(pad=30)

ax3 = plt.subplot(4, 1, 3)
ax3.plot(t, y3, '-o')
ax3.set_xticklabels([])
ax3.set_yticklabels([])
ax3.set_ylim(-9, 9)
ax3.set_zorder(1)
ax3.set_ylabel("sample 3").set_rotation(0)
ax3.yaxis.set_tick_params(pad=30)

ax4 = plt.subplot(4, 1, 4)
ax4.plot(t, y4, '-o')
ax4.set_yticklabels([])
ax4.set_ylim(-9, 9)
ax4.set_zorder(1)
ax4.set_ylabel("sample 4").set_rotation(0)
ax4.yaxis.set_tick_params(pad=30)
ax4.annotate('$Y_6$', xy=(5.92, -19), xycoords='data', annotation_clip=False)

con = ConnectionPatch(xyA=(6, 9), xyB=(6, -9), ls="--", lw=2, color="gray",
                      coordsA="data", coordsB="data", axesA=ax1, axesB=ax4)
ax1.add_artist(con)

plt.show()
```


![1_2_0](https://user-images.githubusercontent.com/41605276/58328713-c13c4b80-7e6d-11e9-94f5-a31712a3aa78.png)


![2](https://user-images.githubusercontent.com/41605276/58328723-c6999600-7e6d-11e9-9e92-64a11f5a228f.jpg)

#### 2. 앙상블평균

![3](https://user-images.githubusercontent.com/41605276/58328819-095b6e00-7e6e-11e9-859e-46beca7d03da.jpg)

#### 5. 확률과정의 기댓값, 자기공분산, 자기상관계수

![4](https://user-images.githubusercontent.com/41605276/58328965-6ce59b80-7e6e-11e9-9e0e-c73f799f9143.jpg)
