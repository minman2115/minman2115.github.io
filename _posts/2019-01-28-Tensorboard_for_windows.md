---
layout: post
title: "텐서보드 사용환경 구축방법"
tags: [딥러닝, 환경설정]
comments: true
---

예시를 통해서 텐서보드 환경구축 방법을 이해

#### step 1) 먼저 방화벽에서 '6006'번 포트를 개방해야 한다.

- 제어판 -> 시스템 및 보안 -> windows defender 방화벽 -> 고급설정 -> 인바운드 규칙 마우스 오른쪽 클릭 -> 새 규칙 클릭 -> 포트 클릭 후 다음 -> 특정 로컬 포트에 6006 입력 후 다음 -> 연결 허용 클릭 후 다음 -> 도메인, 개인, 공용 클릭 후 다음 -> 임의의 이름 입력 후 마침

#### step 2) 텐서보드에 전시하고 싶은 코드를 작성한다.

- iris 데이터 로드


```python
import tensorflow as tf
from sklearn.datasets import load_iris
import pandas as pd
import numpy as np

iris = load_iris()
idx = np.in1d(iris.target, [0, 2])
X_data = iris.data[idx, 0:2].astype("float32")
y_data = (iris.target[idx] - 1.0)[:, np.newaxis].astype("float32") 
# y=1 또는 y=-1. 2차원임을 명시
```

- Tensorflow를 이용 퍼셉트론을 생성하는 코드 작성


```python
tf.reset_default_graph()  # 지금까지 생성된 모든 텐서를 그래프에서 제거

np.random.seed(0)
with tf.variable_scope("perceptron", reuse=tf.AUTO_REUSE):
    w = 1e-3 * tf.get_variable("weight", [2, 1], dtype=tf.float32,
                               initializer=tf.random_uniform_initializer())
    b = 1e-3 * tf.get_variable("bias", [], dtype=tf.float32,##[]로 비워져 있으면 상수
                               initializer=tf.random_uniform_initializer())
    z = tf.tanh(tf.matmul(X_data, w) + b)

zero = np.zeros(1, dtype=np.float32)[0]  # float32 자료형의 0 값 생성
cost = tf.reduce_sum(tf.maximum(zero, tf.multiply(-y_data, z)))
optimizer = tf.train.GradientDescentOptimizer(1e-1)
train = optimizer.minimize(cost)
```

- Tensorboard 전시를 위한 코드


```python
tf.reset_default_graph()  
# 지금까지 생성된 모든 텐서를 그래프에서 제거

np.random.seed(0)
with tf.variable_scope("perceptron", reuse=tf.AUTO_REUSE):
    w = 1e-3 * tf.get_variable("weight", [2, 1], dtype=tf.float32,
                               initializer=tf.random_uniform_initializer())
    b = 1e-3 * tf.get_variable("bias", [], dtype=tf.float32,##[]로 비워져 있으면 상수
                               initializer=tf.random_uniform_initializer())
    z = tf.tanh(tf.matmul(X_data, w) + b)

zero = np.zeros(1, dtype=np.float32)[0]  # float32 자료형의 0 값 생성
cost = tf.reduce_sum(tf.maximum(zero, tf.multiply(-y_data, z)))
optimizer = tf.train.GradientDescentOptimizer(1e-1)
train = optimizer.minimize(cost)
```

#### step 3) CMD에서 텐서보드 가동

- tensorboard --logdir='로그 저장한 경로' 명령어 입력

ex) tensorboard --logdir=C:\Users\mypc\Desktop\virtualVE\board\5Step

- 웹 브라우저에서 http://localhost:6006 으로 접속
