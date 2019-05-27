---
layout: post
title: "white noise와 random walk 기초개념"
tags: [시계열분석]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://datascienceschool.net

#### 1. white noise(백색잡음)

![1](https://user-images.githubusercontent.com/41605276/58423342-d4e6ec80-80cf-11e9-9add-f99a78b15992.jpg)

#### 2. Discrete-time random walk(이산시간 랜덤워크)

![2](https://user-images.githubusercontent.com/41605276/58423355-db756400-80cf-11e9-914c-071f8e05b17f.jpg)

이산 시간 랜덤 워크는 아래와 같은 특성을 가진다.

![3](https://user-images.githubusercontent.com/41605276/58423362-e0d2ae80-80cf-11e9-9d8c-89418224443c.jpg)

Discrete-time random walk는 white noise에 대한 cumsum(누적합)으로 구현할 수 있다.
