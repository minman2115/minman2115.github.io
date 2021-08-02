---
layout: post
title: "변수분포문제 - 피쳐간에 상관성이 높을때"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210802)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 피쳐간에 상관성이 높을때 문제점

회귀모델, 신경망, SVM과 같이 `wx+b` 형태의 선형식이 모델에 포함되는 경우, 피쳐간에 상관성이 높으면 파라미터 추정이 어려울수 있음. 모델로 predict하는 값이 흔들린다는 말이다. 다시말해서 추정할때마다 결과가 달라질 수 있는 문제가 있음

아래 그림이 무슨말이냐면 y=2x1이고, x2=x1이라면 결론적으로 w1과 w2가 무수히 많은 해를 갖는다는 문제가 있다는 것이다. 회귀모델의 경우에는 더 심각한 현상이 있다. 회귀모델은 결국에 잔차제곱합을 최소화를 해야해서 미분을 해야하는데 미분할때 결과식에 역행렬이 존재하지 않을 수 있기 때문에 해를 구하는게 불가능할수도 있다.

![1](https://user-images.githubusercontent.com/41605276/127862557-81387ebc-babd-4094-a502-61ff95ce8186.PNG)

트리계열의 모델은 사실 피쳐간에 상관성이 높다고해서 모델 예측 성능에 영향을 받지 않지만, 상관성이 높은 변수 중 소수만 모델에 포함되기 때문에 설명력이 크게 영향을 받을 수 있음

위에 그림과 같이 극단적인 예를 들어서 x1과 x2처럼 아예 같은 특징이 있다면 x1으로 분리를 하던 x2를 기준으로 분리를 하던 똑같기 때문에 둘중하나를 임의로 선택을 할 것이다. 그러면 x1은 모델에 어떤 영향을 끼치는지 반영이 안되기 때문에 설명력 문제가 발생할 수 있다.


- 해결방법 1. VIF 활용

주로 회귀모델에서 다중공선성 문제를 해결할때 사용하는 지표다. Variance Inflation factors (VIF)는 하나의 피쳐를 라벨로 간주하고, 해당 라벨을 예측하는데 다른 피쳐를 사용한 회귀모델이 높은 `R 스퀘어`(회귀모델에서 적합성 지표로 0~1을 왔다갔다하고 1로 갈수록 좋은 모델이라고 판단할 수 있는 지표)를 보이는 경우 해당 특징이 다른 특징과 상관성이 있다고 판단한다.

![2](https://user-images.githubusercontent.com/41605276/127862927-c38743f2-9bed-4211-8a16-4d2a49c9ba94.PNG)

VIF가 10 이상인 경우 그 피쳐는 다른 피쳐와 상관성이 매우 높다고 판단해서 일반적으로는 삭제한다.

VIF가 높은 순서대로 피쳐를 제거하거나, VIF가 10 이상인 경우 일반적으로 삭제한다.

- 해결방법 2. 주성분 분석 (PCA)

주성분 분석을 이용하여 피쳐가 서로 직교하도록 만들어 피쳐간 상관성을 줄이는 방법도 있다. 예를 들어서 아래 그림과 같이 x1과 x2라는 피쳐가 있을때 이 데이터들을 원래의 피쳐가 아니라 새로운 차원으로 데이터를 투영시키는 방법이다. z1(z1이라고 정한 것은 데이터가 z1 방향으로 배치되어 있기 때문이다.) 이라는 축을 찾고 z2(z2이라고 정한 것은 데이터가 z2 방향으로 배치되어 있기 때문이다.)라는 축을 찾아서 이 두개의 축을 바탕으로 데이터를 투영을 시킨다. (이 z1과 z2를 주성분이라고 부른다. 그리고 이 그림에서는 수학적으로 z1이 z2보다는 데이터의 분산을 더 잘 설명한다.) 다시말해서 주성분 분석으로 데이터를 투영시키고 나면 데이터의 차원은 같지만 데이터의 분산을 설명하는 정도를 측정해서 정도가 높은 차원만 골라서 차원을 줄이게 된다. 주성분으로 축을 회전시키기 때문에(데이터를 더 잘설명하는 축방향으로) 관계가 있는 피쳐들의 상관성을 줄이게 된다.

![3](https://user-images.githubusercontent.com/41605276/127863110-724f9569-a274-48cd-aee8-5f737e9879ef.PNG)

n차원의 데이터는 총 n개의 주성분이 존재하지만, 차원축소 등을 위해 분산의 대부분을 설명하는 `m < n` 주성분만 사용하는게 일반적이다.

sklearn.decomposition.PCA : 주성분 분석을 수행하는 인스턴스를 생성하는 함수

주요입력 

n_components : 사용할 주성분 개수를 나타내며, 이 값은 기존 차원수보다 작아야 함

주요 attribute

.explained_variance_ratio : 각 주성분이 원 데이터의 분산을 설명하는 정도로 일반적으로는 1등부터 n등까지 점수가 있을때 누적합이 보통 90%(0.9)가 넘는정도에서 끊는다.


- 실습


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/5. 머신러닝 모델의 성능 향상을 위한 전처리\데이터")

df = pd.read_csv("abalone.csv")
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
      <th>Length</th>
      <th>Diameter</th>
      <th>Height</th>
      <th>Wholeweight</th>
      <th>Shuckedweight</th>
      <th>Visceraweight</th>
      <th>Shellweight</th>
      <th>Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.455</td>
      <td>0.365</td>
      <td>0.095</td>
      <td>0.5140</td>
      <td>0.2245</td>
      <td>0.1010</td>
      <td>0.1500</td>
      <td>15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.350</td>
      <td>0.265</td>
      <td>0.090</td>
      <td>0.2255</td>
      <td>0.0995</td>
      <td>0.0485</td>
      <td>0.0700</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.530</td>
      <td>0.420</td>
      <td>0.135</td>
      <td>0.6770</td>
      <td>0.2565</td>
      <td>0.1415</td>
      <td>0.2100</td>
      <td>9</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.440</td>
      <td>0.365</td>
      <td>0.125</td>
      <td>0.5160</td>
      <td>0.2155</td>
      <td>0.1140</td>
      <td>0.1550</td>
      <td>10</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.330</td>
      <td>0.255</td>
      <td>0.080</td>
      <td>0.2050</td>
      <td>0.0895</td>
      <td>0.0395</td>
      <td>0.0550</td>
      <td>7</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>4172</th>
      <td>0.565</td>
      <td>0.450</td>
      <td>0.165</td>
      <td>0.8870</td>
      <td>0.3700</td>
      <td>0.2390</td>
      <td>0.2490</td>
      <td>11</td>
    </tr>
    <tr>
      <th>4173</th>
      <td>0.590</td>
      <td>0.440</td>
      <td>0.135</td>
      <td>0.9660</td>
      <td>0.4390</td>
      <td>0.2145</td>
      <td>0.2605</td>
      <td>10</td>
    </tr>
    <tr>
      <th>4174</th>
      <td>0.600</td>
      <td>0.475</td>
      <td>0.205</td>
      <td>1.1760</td>
      <td>0.5255</td>
      <td>0.2875</td>
      <td>0.3080</td>
      <td>9</td>
    </tr>
    <tr>
      <th>4175</th>
      <td>0.625</td>
      <td>0.485</td>
      <td>0.150</td>
      <td>1.0945</td>
      <td>0.5310</td>
      <td>0.2610</td>
      <td>0.2960</td>
      <td>10</td>
    </tr>
    <tr>
      <th>4176</th>
      <td>0.710</td>
      <td>0.555</td>
      <td>0.195</td>
      <td>1.9485</td>
      <td>0.9455</td>
      <td>0.3765</td>
      <td>0.4950</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
<p>4177 rows × 8 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop(['Age'], axis = 1)
Y = df['Age']

# 학습 데이터와 평가 데이터 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)

# 피쳐 간 상관 행렬 출력 => 얼핏봐도 특징 간 선형 관계가 존재
Train_X.corr() 
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
      <th>Length</th>
      <th>Diameter</th>
      <th>Height</th>
      <th>Wholeweight</th>
      <th>Shuckedweight</th>
      <th>Visceraweight</th>
      <th>Shellweight</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Length</th>
      <td>1.000000</td>
      <td>0.986830</td>
      <td>0.808960</td>
      <td>0.926150</td>
      <td>0.899637</td>
      <td>0.905273</td>
      <td>0.896118</td>
    </tr>
    <tr>
      <th>Diameter</th>
      <td>0.986830</td>
      <td>1.000000</td>
      <td>0.815889</td>
      <td>0.926532</td>
      <td>0.895261</td>
      <td>0.902013</td>
      <td>0.904002</td>
    </tr>
    <tr>
      <th>Height</th>
      <td>0.808960</td>
      <td>0.815889</td>
      <td>1.000000</td>
      <td>0.802585</td>
      <td>0.762270</td>
      <td>0.781529</td>
      <td>0.797909</td>
    </tr>
    <tr>
      <th>Wholeweight</th>
      <td>0.926150</td>
      <td>0.926532</td>
      <td>0.802585</td>
      <td>1.000000</td>
      <td>0.969747</td>
      <td>0.965883</td>
      <td>0.955556</td>
    </tr>
    <tr>
      <th>Shuckedweight</th>
      <td>0.899637</td>
      <td>0.895261</td>
      <td>0.762270</td>
      <td>0.969747</td>
      <td>1.000000</td>
      <td>0.932528</td>
      <td>0.884695</td>
    </tr>
    <tr>
      <th>Visceraweight</th>
      <td>0.905273</td>
      <td>0.902013</td>
      <td>0.781529</td>
      <td>0.965883</td>
      <td>0.932528</td>
      <td>1.000000</td>
      <td>0.906717</td>
    </tr>
    <tr>
      <th>Shellweight</th>
      <td>0.896118</td>
      <td>0.904002</td>
      <td>0.797909</td>
      <td>0.955556</td>
      <td>0.884695</td>
      <td>0.906717</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



VIF 기준 특징 선택으로 상관성이 큰 피쳐들을 제거해보자


```python
# VIF 계산
from sklearn.linear_model import LinearRegression as LR
VIF_dict = dict()

# 하나의 특징을 라벨로 간주하고, 다른 특징들로 해당라벨을 맞추기 위한
# 선형회귀모델을 학습해서 그 모델의 R 스퀘어를 측정하는 방식임
for col in Train_X.columns:
    model = LR().fit(Train_X.drop([col], axis = 1), Train_X[col])
    r2 = model.score(Train_X.drop([col], axis = 1), Train_X[col]) # LinearRegression의 score가 r2 점수임
    VIF = 1 / (1 - r2)
    VIF_dict[col] = VIF
    
# Height를 제외하곤 VIF가 모두 높으므로, 이러한 상황에서는 사실 PCA를 사용하는 것이 바람직
VIF_dict 
```




    {'Length': 40.832544135121715,
     'Diameter': 41.94796343932718,
     'Height': 3.2001321014746384,
     'Wholeweight': 105.18662208852773,
     'Shuckedweight': 27.4371075730439,
     'Visceraweight': 17.047644855695154,
     'Shellweight': 20.42190528630344}




```python
from sklearn.neural_network import MLPRegressor as MLP
from sklearn.metrics import mean_absolute_error as MAE

# 전체 특징을 모두 사용하였을 때
model = MLP(random_state = 2313, max_iter = 500)
model.fit(Train_X, Train_Y)
pred_Y = model.predict(Test_X)
pred_Y
```




    array([ 9.59385993,  7.97062628, 11.2127649 , ...,  4.33436501,
           10.49422684,  8.14692041])




```python
score = MAE(Test_Y, pred_Y)
print(score)
```

    1.558908052248871
    


```python
# VIF 점수가 30점 미만인 특징만 사용하였을 때
selected_features = [key for key, val in VIF_dict.items() if val < 30]
selected_features
```




    ['Height', 'Shuckedweight', 'Visceraweight', 'Shellweight']




```python
model = MLP(random_state = 2313, max_iter = 500)
model.fit(Train_X[selected_features], Train_Y)
pred_Y = model.predict(Test_X[selected_features])
pred_Y
```




    array([ 9.55929626,  7.67010118, 11.22434191, ...,  4.13515311,
           10.68131374,  8.25269615])




```python
score = MAE(Test_Y, pred_Y)
print(score)
```

    1.4930139955574864
    

VIF 점수가 30점 미만인 특징만 사용하였을 때 성능이 더 좋아졌음을 알 수 있다.

PCA를 사용하여 상관성이 큰 피쳐를 제거해보자


```python
from sklearn.decomposition import PCA
PCA_model = PCA(n_components = 3).fit(Train_X)

Train_Z = PCA_model.transform(Train_X)
Test_Z = PCA_model.transform(Test_X)

print(Train_Z.shape)
```

    (3132, 3)
    


```python
model = MLP(random_state = 2313, max_iter = 500)
model.fit(Train_Z, Train_Y)
pred_Y = model.predict(Test_Z)
pred_Y
```




    array([ 9.28943443,  8.02894455, 10.61242906, ...,  3.4948838 ,
           10.27008999,  8.5751747 ])




```python
score = MAE(Test_Y, pred_Y)
print(score)
```

    1.469621601465586
    

PCA를 써서 모델을 테스트해보니까 더 성능이 좋아진걸 알 수 있다.
