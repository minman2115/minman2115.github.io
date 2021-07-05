---
layout: post
title: "일원분산분석 기초개념"
tags: [데이터전처리, 수학기초]
comments: true
---

.

Data_Preprocessing_TIL(20210705)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 일원분산분석(ANOVA) 을 하는 목적

셋 이상의 그룹 간에 차이가 존재하는지를 확인하기 위한 가설검증을 목적으로 함

** 두개의 그룹의 차이가 존재하는지를 확인하는 검정방법 : 독립표본 t검정

![1](https://user-images.githubusercontent.com/41605276/124464482-36113c00-ddcf-11eb-81dc-83d0e242ce97.png)

영가설은 모든그룹의 평균이 같다라는 것이고, 대립가설은 최소한 한개그룹은 다른 그룹들과는 차이가 있다 라는 것이다.

예시는 보면 지점 C는 정말 평균이 작은것인가, 이것이 유의한가를 보는 것이다.

- 그룹간 유의미한 차이가 존재하는지는 독립표본 t검정을 그냥 여러번 사용하면 되는거 아닌가 왜 굳이 일원분산분석을 사용하는가

일원분산분석은 독립표본 t검정을 여러번 사용한 것과 같은 결과를 낼것처럼 보인다. 아래와 같이 독립표본 t검정에서 하나이상의 영 가설이 기각되면, 자연스레 일원분산분석의 영가설 역시 기각되므로, 기각된 원인까지 알수 있기 때문에 일원분산분석이 필요없다는 의견도 있다.


![2](https://user-images.githubusercontent.com/41605276/124465150-07e02c00-ddd0-11eb-9332-0a91690a5552.PNG)


하지만 독립표본 t검정을 여러번 했을때, 아무리 높은 p-value가 나오더라도 그 신뢰성에 문제가 생길 수 있어서 일원분산분석이 필요하다.

예를 들어서 각 가설의 p-value가 0.95이고, 그룹의 개수가 k일때 모든 영가설이 참일 확률은 0.95의 k승이다. 그룹의 개수가 3개만 되어도 그 확률이 0.857로 크게 감소하며, 그룹의 개수가 14개가 되면 그 확률이 0.5 미만으로 떨어진다.

- 일원분산분석을 하기 위한 선행조건

선행조건 1. : 독립성 

모든 그룹은 서로 독립적이어야 함


선행조건 2. : 정규성

모든 그룹의 데이터는 정규분포를 따라야 함. 그렇지 않으면 비모수적인 방법인 Kruskal-Wallis H Test를 수행해야함


선행조건 3. : 등분산성

모든그룹에 데이터에 대한 분산이 같아야함. 그렇지 않으면 비모수적인 방법인 Kruskal-Wallis H Test를 수행해야함


- 일원분산분석의 통계량

아래 수식과 같이 통계량 F는 집단 간에 차이가 크면 클수록 커지게 되고, 집단 내 분산이 작으면 작을수록 또 통계량이 커질것이다. 통계량이 커진다는 것은 p-value가 작아진다는 의미이고, 이말은 예를 들어서 모든 그룹의 평균이 같다는 영가설은 기각될 가능성이 높아진다.

집단간 분석 수식을 보면 어떤 그룹에 속한 샘플의 평균과 전체 샘플의 평균의 차이가 크면 크다는 것은 당연히 집단간에 차이가 존재한다는 의미이기 때문에 통계량이 커질 것이다.

![3](https://user-images.githubusercontent.com/41605276/124469639-91dec380-ddd5-11eb-826e-f20e8d959666.PNG)

- Tukey HSD test : 사후분석

Tukey HSD test는 일원분산분석에서 두 그룹 a와 b간 차이가 유의한지 파악하는 사후분석 방법이다. 이게 왜 필요하냐면 일원분산분석의 대립가설을 보면 된다. 대립가설은 '최소한 한개 그룹에는 차이를 보인다'라고 되어 있는데 그러면 만약에 영가설이 기각된다면 어떤 그룹때문에 대립가설이 성립하는지 알고 싶은것이다. 이때 사용하는 것이 Tukey HSD test이다.

![4](https://user-images.githubusercontent.com/41605276/124470381-83dd7280-ddd6-11eb-9188-0e37354e81e4.PNG)

통계량이 유의수준보다 크면 a와 b가 차이가 크다는것이 유의하다고 판단하고 유의하기 때문에 영가설을 기각한다는 근거로 쓴다.


- 파이썬을 이용한 일원분산분석

1) 정규성 검정 (KS test)

코드


```python
scipy.stats.kstest(x,'norm')
```

결과해석

pvalue가 특정수치 미만이면 정규성을 따른다고 판단

2) 일원분산분석

코드


```python
scipy.stats.f_oneway(sample1,sample2,sample3,sample4,...)
```

결과해석

pvalue가 특정수치 미만이면 최소 하나의 그룹은 다른 그룹의 평균과 다르다고 판단

다시말해서 특정실험의 효과가 존재한다고 판단할 수 있음

3) 사후분석

코드


```python
statsmodels.stats.multicomp.pairwise_tukeyhsd(Data,Group)
```

결과해석

각 그룹간 reject 결과를 확인할 수 있음

reject 컬럼이 True이면 두 그룹간 차이가 유의하다고 할 수 있음


- 일원분산분석 실제 사용 예시


```python
# data 불러오기
import os
import pandas as pd
# pwd 시 경로 : /c/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터
os.chdir(r"C:/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터/")

# 데이터 불러오기 및 확인
df = pd.read_csv("지점별_일별판매량.csv", engine = "python")
df.head()
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
      <th>일자</th>
      <th>지점A</th>
      <th>지점B</th>
      <th>지점C</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>124</td>
      <td>118</td>
      <td>216</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2</td>
      <td>147</td>
      <td>141</td>
      <td>214</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
      <td>129</td>
      <td>130</td>
      <td>212</td>
    </tr>
    <tr>
      <td>3</td>
      <td>4</td>
      <td>123</td>
      <td>127</td>
      <td>179</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5</td>
      <td>147</td>
      <td>107</td>
      <td>222</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 데이터 분할 (결측이 포함되어 있을 수 있으므로 dropna를 사용)
A = df['지점A'].dropna().values
B = df['지점B'].dropna().values
C = df['지점C'].dropna().values
print(A)
```

    [124 147 129 123 147 149 140 142 124 166 127 130 139 121 149 155 140 150
     137 144 136 126 150 142 149 127 140 138 128 117]
    

그리고 일원분산분석이나 독립표본 t검정을 할때 박스플롯을 이용해서 데이터 현황을 파악하는 경우가 많다. 그려보니까 지점 C는 확실히 지점 A와 B보다 평균이 크다라는 것을 알 수 있다. 일반적인 경우에는 사실 이처럼 차이가 잘 안보이는 경우가 대다수이다.


```python
# 박스플롯으로 시각화
%matplotlib inline
from matplotlib import pyplot as plt
plt.rcParams["font.family"] = 'Malgun Gothic'
plt.rcParams["font.size"] = 20
plt.rcParams["figure.figsize"] = (10, 10)
plt.boxplot([A, B, C])
plt.xticks([1, 2, 3], ['지점 A', '지점 B', '지점 C'])
```




    ([<matplotlib.axis.XTick at 0x16a5e8d2588>,
      <matplotlib.axis.XTick at 0x16a6282d808>,
      <matplotlib.axis.XTick at 0x16a625ba0c8>],
     <a list of 3 Text xticklabel objects>)




![5](https://user-images.githubusercontent.com/41605276/124473644-89d55280-ddda-11eb-951c-4d73fa8056e2.png)



아래와 같이 정규성 검정을 했을때 세 변수 모두 정규 분포인 것을 판단할 수 있다.


```python
from scipy.stats import *
print(kstest(A, 'norm'))
print(kstest(B, 'norm'))
print(kstest(C, 'norm'))
```

    KstestResult(statistic=1.0, pvalue=0.0)
    KstestResult(statistic=1.0, pvalue=0.0)
    KstestResult(statistic=1.0, pvalue=0.0)
    

아래와 같이 일원분산분석을 수행했는데 p-value가 거의 0에 수렴하였다. 따라서 A, B, C의 평균은 유의한 차이가 존재한다고 판단할 수 있다.


```python
print(f_oneway(A, B, C))
```

    F_onewayResult(statistic=178.43825416847133, pvalue=1.6337420285664805e-31)
    

아래와 같이 사후분석을 진행하였는데 결론은 아래와 같다.


```python
from statsmodels.stats.multicomp import pairwise_tukeyhsd
Group = ['A'] * len(A) + ['B'] * len(B) + ['C'] * len(C)
print(Group)
```

    ['A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'A', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'B', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C', 'C']
    


```python
Data = A.tolist() + B.tolist() + C.tolist()
print(Data)
```

    [124, 147, 129, 123, 147, 149, 140, 142, 124, 166, 127, 130, 139, 121, 149, 155, 140, 150, 137, 144, 136, 126, 150, 142, 149, 127, 140, 138, 128, 117, 118, 141, 130, 127, 107, 140, 146, 122, 160, 157, 141, 142, 150, 136, 157, 107, 172, 126, 135, 115, 133, 132, 127, 128, 142, 161, 140, 125, 142, 135, 216, 214, 212, 179, 222, 238, 218, 200, 186, 188, 216, 183, 198, 199, 187, 183, 190, 161, 203, 192, 235, 208, 187, 201, 196, 202, 209, 179, 197, 204]
    


```python
print(pairwise_tukeyhsd(Data, Group))
```

    Multiple Comparison of Means - Tukey HSD, FWER=0.05 
    ====================================================
    group1 group2 meandiff p-adj  lower    upper  reject
    ----------------------------------------------------
         A      B     -1.4   0.9 -10.5745  7.7745  False
         A      C  62.2333 0.001  53.0589 71.4078   True
         B      C  63.6333 0.001  54.4589 72.8078   True
    ----------------------------------------------------
    

결론: [A, B] 와 [C]로 데이터가 구분됨

A와 B는 유의한 차이가 존재하지 않음

A와 C는 유의한 차이 존재

B와 C는 유의한 차이 존재 
