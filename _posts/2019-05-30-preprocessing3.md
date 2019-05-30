---
layout: post
title: "데이터의 파편화 및 분포문제"
tags: [데이터전처리]
comments: true
---

Data_Preprocessing_Studynotes_(20190529)

study program : https://www.fastcampus.co.kr/data_camp_ppc

#### [학습기록]

#### 1. 피쳐 엔지니어링

- 우리가 원하는 데이터프레임 모양의 이쁜데이터는 현실에서는 그다지 많지 않다. 그래서 우리는 지저분한 현실데이터를 이쁜데이터로 변환을 해줘야 하는데 그런 작업을 피쳐 엔지니어링 이라고 한다.


- 피쳐 엔지니어링은 '특징추출'과 '특징선택' 으로 이루어지는데 통상 특징추출이 특징선택보다 선행되어야 한다.


- 범주형 변수에 대한 더미화 vs 원핫인코딩

범주형 변수는 변수간에 대소관계가 존대하지 않기 때문에 이 변수가 "있냐", "없냐"로 변환을 해줘야 하는데 그렇게 해주는 방법이 더미화와 원핫인코딩이다.

1) 더미화 

기존에 특정 피쳐에서 n개의 범주형 변수를 n-1개의 피쳐로 만드는 것(이 피쳐가 있느냐 없느냐를 의미함) 

굳이 n개로의 원핫인코딩을 하지 않아도 된다 왜냐하면 예를들어서 아래와 같이 더미화를해서 2개 피쳐만으로도 세개를 표현할 수 있는데 뭐하러 피쳐를 늘리는가..

ex) '성별'이라는 변수에서 더미화 예시

![1](https://user-images.githubusercontent.com/41605276/58614859-8fefcf80-82f4-11e9-9d53-dc4339edf660.png)

2) 원핫인코딩

특정 피쳐에서 범주형 변수를 n개의 변수로 만들어주는 것, 신경망에서 대상이 출력노드를 구성할때 주로 사용(머신러닝시는 그냥 라벨링, 원핫인코딩 불가)

원핫인코딩 예시)

![2](https://user-images.githubusercontent.com/41605276/58614882-99793780-82f4-11e9-9ffd-619e04af22bb.png)

양념 = [1,0,0]

후라이드 = [0,1,0]

간장 = [0,0,1]

#### 2. 원핫인코딩 실제 적용사례

보통 겟더미즈 메서드를 많이 쓴느데 여기서는 딕트벡터라이저를 쓰겠다. 

- 딕트벡터라이저 쓰는 방법

1) 데이터프레임 -> 딕셔너리로 바꿔주는 작업 필요(df.dict() 메서드를 사용하면 된다.)

2) 중복되는 피쳐 제거

3) 딕트 벡터라이저 적용

위에 처럼 딕셔너리로 바꿔주고 중복되는 피쳐까지 제거해야하는 번거로움이 있음에도 불구하고 딕트벡터라이저를 쓴 이유는 겟더미즈 함수는 딕트벡터라이저보다 재사용성이 떨어진다. 다시말해서 추후 새로운 형태의 변수를 입력변수를 입력받기 어렵다.

- 메서드 설명

1) vec = sklearn.feature_extraction.DictVectorizer()

vec이라는 변수로 정의하는 인스턴스화

2) vec.fit(data)

data에 대한 피팅. 피팅하고 나면 명목변수를 변환할 사전을 저장한다.

3) vec.transform(data)

피팅한 vec 인스턴스를 data에 적용하여 변환시킴

4) vec.fit_trasform(data) 

vec 인스턴스를 data에 대해 피팅하고 data를 변환시킴(학습데이터에만 적용)

4) vec.get_feature_names()

피팅한 후 새로 만들어진 변수집합

- 적용예시


```python
from sklearn.feature_extraction import DictVectorizer

data = [
{'price': 850000, 'rooms': 4, 'neighborhood': 'Queen Anne'},
{'price': 700000, 'rooms': 3, 'neighborhood': 'Fremont'},
{'price': 650000, 'rooms': 3, 'neighborhood': 'Wallingford'},
{'price': 600000, 'rooms': 2, 'neighborhood': 'Fremont'}
]

vec = DictVectorizer (sparse=False, dtype=int)
vec.fit_transform(data)
```




    array([[     0,      1,      0, 850000,      4],
           [     1,      0,      0, 700000,      3],
           [     0,      0,      1, 650000,      3],
           [     1,      0,      0, 600000,      2]], dtype=int32)




```python
vec.get_feature_names()
```




    ['neighborhood=Fremont',
     'neighborhood=Queen Anne',
     'neighborhood=Wallingford',
     'price',
     'rooms']



#### 3. 데이터 파편화 문제패턴 중 ID별 파일이 따로 없는 경우

자식 데이터를 아이디 기준으로 요약하여 데이터를 통합한다.

![3](https://user-images.githubusercontent.com/41605276/58614894-9ed68200-82f4-11e9-82cc-28361f3fbd70.png)

aggregated_df=df.groupby('key')aggregate(['요약방법'])을 바탕으로 요약 결과 활용

다시말해 조건부 통계인 그룹바이을 활용하면 된다.

그룹바이 다른예시)

고객 아이디에 따른 구매금액의 합계 집계

![4](https://user-images.githubusercontent.com/41605276/58614918-a7c75380-82f4-11e9-997f-659591c36200.png)

위와 같은 경우 아래와 같은 코드로 병합하면 된다.


```python
demo_df = pd.read_csv("고객별 인구통계정보 .csv", engine = "python")
purchasing_df = pd.read_csv("고객별 구매금액 .csv", engine = "python")

purchasing_aggregated_df = purchasing_df.groupby('고객ID')['구매금액'].aggregate(['sum'])
merged_df = pd.merge(demo_df,purchasing_aggregated_df,\
                     left_on = '고객 ID', right_index = True)
```
