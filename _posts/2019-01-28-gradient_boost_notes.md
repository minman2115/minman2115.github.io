---
layout: post
title: "그레디언트 부스트 모델"
tags: [머신러닝]
comments: true
---

그레디언트 부스트 모델 이론 및 간략한 사용법

1. 개념요약 : 
   1) 기존의 위원회 멤버 외에 '새로운 멤버'를 찾을때 그레디언트 디센트 방법으로 최적의 함수를
      찾는 방법. 즉 새로운 멤버는 그레디언트에 해당하는 것을 집어넣는 부스팅 방법.
     '그레디언트'라는 것은 미분값을 이용해서 부스팅을 하자는 것인데 '그레디언트 
      디센트' 방법으로 x를 찾듯이 범함수를 이용해서 최적의 함수를 찾는다. 
   2) 기존의 위원회 멤버외 새로운 멤버를 뽑는 방법에서 '에이다부스트'와 차이가 있음

       * '에이다부스트' : 학습데이터 집합에 가중치를 주고 분류 모형이 틀리게 예측한 데이터의 
                              가중치를 합한 값을 손실함수로 사용.(벌점제도와 유사한데 벌점을 
                              최소화해야함). 이 손실함수를 최소화하는 모형이 새로운 멤버로 추가됨

2. 그레디언트 부스트 구현을 위한 Scikit-learn API 종류
1) GradientBoostingClassifier
   - 특징 
      1) sklearn 라이브러리에 내장되어 있음
      2) 파이썬 코드로 구현되어 상당히 느린편
         (성격테스트용으로 추천)

2) XGBoost
   - 특징 : 
      1) GradientBoostingClassifier의 느린 단점을 보완하고자 등장함
      2) C언어로 구현되어 상당히 빠른편
      3) 아나콘다 클라우드에 윈도우즈용으로는 'py-xgboost'이라는 라이브러리가 탑재되어 있음

3) LightGBM
   - 특징 : 
      1) GradientBoostingClassifier의 느린 단점을 보완하고자 등장함
      2) 마이크로소프트 사에서 개발한 라이브러리
      3) 아나콘다 클라우드에 윈도우즈 용으로는 'lightgbm'이라는 라이브러리가 탑재되어 있음
      4) 경험상 GradientBoostingClassifier 보다 10배 이상은 빠르다고 판단됨
   
   - document URL : 
      1) https://lightgbm.readthedocs.io/en/latest/Python-API.html
      2) 스크롤 내리다보면 중간에 'Scikit-learn API' 참고