---
layout: post
title: "카이제곱검정 기초개념"
tags: [데이터전처리, 수학기초]
comments: true
---

.

Data_Preprocessing_TIL(20210708)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 개요

목적 : 두 범주형 변수가 서로 독립적인지를 검정

영가설 : 두 변수가 서로 독립이다.

대립가설 : 두 변수가 서로 종속된다.

시각화 방법 : 교차 테이블

- 교차 테이블과 기대값

교차테이블은 두 변수가 취할 수 있는 값의 조합의 출현 빈도를 나타냄

아래 그림에서 검정색 숫자는 실제값이고, 빨간색 숫자는 기대값을 의미한다. 

기대값이라는 것은 만족도와 성별이 서로 독립일때 남자와 여자를 합쳤을때 만족도별 평균인 것이다.

![1](https://user-images.githubusercontent.com/41605276/124910081-2475a180-e026-11eb-8b51-acc313e3dc15.PNG)

- 카이제곱 검정의 통계량

카이제곱 검정에 사용하는 카이제곱 통계량은 기대값과 실제값의 차이를 바탕으로 정의됨

아래 그림과 같이 기대값과 실제값의 차이가 크면 클수록 통계량이 커지고, 통계량이 커지면 커질수록 영가설을 기각하는 근거가 된다. 

![2](https://user-images.githubusercontent.com/41605276/124910225-538c1300-e026-11eb-88b7-a2c9a5085eb9.PNG)

기대값과 실제값의 차이가 클수록 통계량이 커지며, 통계량이 커질수록 영가설이 기각될 가능성이 높아짐(즉, pvalue가 감소함)

- 파이썬을 이용한 카이제곱 검정

1) 교차 테이블 생성

코드


```python
pandas.crosstab(S1,S2)
```

결과해석

Series S1과 S2로 구성된 교차 테이블을 생성

2) 카이제곱 검정

코드


```python
scipy.stats.chi2_contingency(obs)
# obs : 실제값으로 위와 같은 교차테이블 형태가 들어감
```

결과해석

교차 테이블의 실제값에 대한 기대값 계산

보통 pandas.crosstable의 결과에 대한 values를 입력으로 투입

result = (chi2, pvalue, dof, expected)

** 변수설명

chi2 : 카이제곱 통계량

pvalue : p-value

dof : 자유도

expected : 기대값

- 실습

성별 만족도에 대해서 만족도와 성별이 서로 독립적인지를 검증


```python
# data 불러오기
import os
import pandas as pd
# pwd 시 경로 : /c/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터
os.chdir(r"C:/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터/")

df = pd.read_csv("성별에따른만족도.csv", engine = "python")
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>만족도</th>
      <th>성별</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>불만족</td>
      <td>남성</td>
    </tr>
    <tr>
      <td>1</td>
      <td>보통</td>
      <td>여성</td>
    </tr>
    <tr>
      <td>2</td>
      <td>불만족</td>
      <td>여성</td>
    </tr>
    <tr>
      <td>3</td>
      <td>보통</td>
      <td>남성</td>
    </tr>
    <tr>
      <td>4</td>
      <td>만족</td>
      <td>여성</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>195</td>
      <td>보통</td>
      <td>남성</td>
    </tr>
    <tr>
      <td>196</td>
      <td>보통</td>
      <td>여성</td>
    </tr>
    <tr>
      <td>197</td>
      <td>보통</td>
      <td>여성</td>
    </tr>
    <tr>
      <td>198</td>
      <td>보통</td>
      <td>남성</td>
    </tr>
    <tr>
      <td>199</td>
      <td>보통</td>
      <td>여성</td>
    </tr>
  </tbody>
</table>
<p>200 rows × 2 columns</p>
</div>



교차테이블 생성


```python
cross_table = pd.crosstab(df['만족도'], df['성별'])
cross_table
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>성별</th>
      <th>남성</th>
      <th>여성</th>
    </tr>
    <tr>
      <th>만족도</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>만족</td>
      <td>50</td>
      <td>40</td>
    </tr>
    <tr>
      <td>보통</td>
      <td>40</td>
      <td>30</td>
    </tr>
    <tr>
      <td>불만족</td>
      <td>10</td>
      <td>30</td>
    </tr>
  </tbody>
</table>
</div>




```python
obs = cross_table.values
obs
```




    array([[50, 40],
           [40, 30],
           [10, 30]], dtype=int64)



카이제곱 통계량 및 p-value 산출 


```python
from scipy.stats import *
statistics, pvalue, dof, expected = chi2_contingency(obs)
print("카이제곱 통계량 : ", statistics)
print("p-value : ", pvalue)
# pvalue가 0.0018로 영가설 기각. 즉, 두 변수 간 독립이 아님을 확인
```

    카이제곱 통계량 :  12.53968253968254
    p-value :  0.001892528960792166
    

기대값 출력


```python
pd.DataFrame(expected, columns = cross_table.columns, index = cross_table.index)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>성별</th>
      <th>남성</th>
      <th>여성</th>
    </tr>
    <tr>
      <th>만족도</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>만족</td>
      <td>45.0</td>
      <td>45.0</td>
    </tr>
    <tr>
      <td>보통</td>
      <td>35.0</td>
      <td>35.0</td>
    </tr>
    <tr>
      <td>불만족</td>
      <td>20.0</td>
      <td>20.0</td>
    </tr>
  </tbody>
</table>
</div>


