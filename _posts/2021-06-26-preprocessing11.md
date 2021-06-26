---
layout: post
title: "단일 표본 t검정 기초개념"
tags: [데이터전처리, 수학기초]
comments: true
---

.

Data_Preprocessing_TIL(20210626)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 단일 표본 t검정을 하는 목적 : 그룹의 평균이 기준값과 차이가 있는지를 확인


- 영가설과 대립가설

영가설은 표본평균이 어떤 기준값과 같다 라는 것이고

대립가설은 같지 않다라던가 아니면 평균이 기준값보다 크거나 작다이다.


![1](https://user-images.githubusercontent.com/41605276/123504276-1b361d80-d693-11eb-85b1-1b750f44f777.PNG)

- 가설수립 예시 : 어떤 웹사이트를 운영하고 있다고 가정하자. 고객이 웹사이트에서 체류하는 평균 시간이 10분인지 아닌지를 알고 싶어서 다음과 같이 가설을 수립하였다.

이런 경우를 검증하고자 할때는 단일 표본 t검정을 하면된다.

영가설은 체류하는 평균 시간이 10분이다.

대립가설은 체류하는 평균 시간이 10분이 아니다.

![2](https://user-images.githubusercontent.com/41605276/123504290-39038280-d693-11eb-8595-0f74b87ead0a.PNG)


- 단일 표본 t검정을 하기위해서 선행되어야 하는 조건

1) 단일 표본 t검정은 해당 변수가 정규분포를 따라야 한다. 따라서 Kolmogorov-Smornov나 Shapiro-Wilk를 사용하여 정규성 검정이 먼저 선행되어야 한다.

2) 하지만 보통 샘플수가 많을수록 정규성을 띌 가능성이 높아지므로, 샘플 수가 부족한 경우에만 정규성 검정을 하고, 정규성을 띄지 않는다라고 판단하면 비모수적 방법인 부호검정(sign test)나 윌콕슨 부호-순위 검정을 수행해야 한다. 일반적으로는 샘플수가 30개가 넘어가면 특별히 정규성 검정을 하지 않고 정규성을 띈다고 가정한다.

- 단일표본 t검정에서 사용하는 통계량

통계량 t에서 표본평균과 기준값의 차이가 크면 통계량의 절대값이 매우 커지거나 작아질 것이다. 분모를 보면 표본 표준편차를 표본수로 나눈것을 알 수 있다. s가 커지면 커질수록 통계량 t의 절대값을 작게 만들것이다. 반면에 n이 커지면 커질수록 통계량 t의 절대값을 크게 할 것이다. 

정리하면 통계량 t는 표본평균과 기준값의 차이가 크고, 표본 표준편차가 작고, 표본수가 많을수록 영가설이 기각될 수 있다는 것을 알 수 있다. 정규분포와 유사한 t분포상에 통계량 t를 위치시켜 p-value를 계산한다.

통계량 t가 크면 클수록 분포상에 오른쪽에 위치할 것이다. 그리고 오른쪽으로 멀리갈수록 해당 면적이 줄어들것이다. 그 면적을 p-value로 확인할 수 있다. 이 p-value가 0.05보다 작으면 영가설을 기각하고, 표본평균이 뮤보다 크다고 판단할 것이다.

![3](https://user-images.githubusercontent.com/41605276/123504451-600e8400-d694-11eb-8a6c-1efcfcfca5f6.png)

- 정규성 검정 : Kolmogorov-Smornov

Kolmogorov-Smornov 검정은 관측한 샘플들이 특정분포를 따르는지 확인하기 위한 검증방법이다.

Kolmogorov-Smornov 검정은 특정분포를 따른다면 나올것이라 예상되는 값과 실제 값의 차이가 유의한지 확인하는 방법으로, 해당 특정 분포를 정규분포로 설정하여 정규성 검정에도 사용한다.

우리가 관측한 값과 특정분포를 따른다면 나올것이라 예상되는 값의 차이가 크면 클수록 관측한 데이터는 특정 분포를 따르지 않는다고 볼 것이고, 우리가 관측한 값과 특정분포를 따른다면 나올것이라 예상되는 값의 차이가 적으면 관측한 데이터는 특정 붙포를 따른다고 본다.

- python을 이용한 단일표본 t검정

1) 정규성 검정(Kolmogorov-Smornov 검정)

코드 


```python
# x = 수집한 데이터
# 'norm' = 정규분포를 의미
scipy.stats.kstest(x,'norm')
```

결과해석

`result = (statistics,pvalue)` 의 튜플형태

pvalue가 특정수치 미만이면 정규성을 따른다고 판단

2) 단일 표본 t검정

코드


```python
# x = 수집한 데이터
# popmean = 우리가 검정하고자 하는 기준값
scipy.stats.ttest_1samp(x,popmean)
```

결과해석

`result = (statistics,pvalue)` 의 튜플형태

statistics가 양수면 x의 평균이 popmean보다 큰 것이며, 음수면 x의 평균이 popmean보다 작음을 의미

pvalue가 특정수치 미만이면 x는 popmean과 같지 않다고 판단

3) 윌콕슨 부호-순위 검정

코드


```python
# x = 수집한 데이터
# 여기서는 popmean이라는 기준값을 사용자가 설정하지 않는다. popmean은 자동으로 x의 중위수로 설정된다.
scipy.stats.willcoxon(x)
```

결과해석

`result = (statistics,pvalue)` 의 튜플형태

단일표본 t검정과 결과해석이 같음

- python을 이용한 실제 실습

step 1) 데이터 load


```python
# data 불러오기
import os
# pwd 시 경로 : /c/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터
os.chdir(r"C:/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터/")

with open("성인여성_키_데이터.txt", "r") as f:
    data = f.read().split('\n')
    data = list(map(float, data))
    
print(data)
```

    [150.27, 142.94, 160.99, 157.48, 153.46, 137.5, 154.94, 159.51, 171.87, 143.69, 153.65, 160.25, 153.07, 154.42, 141.21, 154.17, 162.56, 164.44, 172.36, 141.51, 169.4, 167.97, 170.26, 157.8, 167.61]
    

step 2) 정규성 검정


```python
# 정규성 검정
from scipy.stats import *
kstest(data, 'norm') # p-value가 0.0 < 0.05으로 정규성을 띈다고 볼 수 있음 
```




    KstestResult(statistic=1.0, pvalue=0.0)



step 3) 정규성을 띈다면 단일 표본 t 검정 수행


```python
# 단일 표본 t 검정 수행
print(ttest_1samp(data, 163)) 

# pvalue가 0.05 미만이므로 영가설 (data의 평균 == 163) 기각
# 통계량이 음수이므로 data이 평균이 163보다 작음을 알 수 있음
```

    Ttest_1sampResult(statistic=-2.979804412662668, pvalue=0.006510445335847954)
    
