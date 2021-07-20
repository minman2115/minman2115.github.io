---
layout: post
title: "데이터 파편화 문제 - 데이터 요약이 필요한 경우"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210720)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 데이터 요약이 필요한 경우는 어떤 경우인가

일반적으로 1대 N 병합인 경우에 사용하는데, 거래데이터 또는 로그데이터와 병합하는 경우에 주로 사용된다.

중복레코드를 포함하는 데이터를 요약한 후 병합하는 방식으로 문제를 해결하면 된다.

![1](https://user-images.githubusercontent.com/41605276/126330005-04d13804-465a-4887-8ebe-6015e6c4d325.PNG)

- 위에 문제를 해결하기 위해 자주쓰는 함수 : DataFrame.groupby()

1) 조건부 통계량(조건에 따른 대상의 통계량)을 계산하기 위한 함수로 머신러닝 프로세스 뿐만 아니라, 통계 분석등에서도 상당히 많이 사용된다.

2) 주요입력

by : 조건변수(컬럼명 혹은 컬럼명 리스트로 입력)

as_index : 조건변수를 index로 설정할 것인지 여부

활용예시

`df.groupby(['성별'])['신장'].mean()`


---> 성별(조건)에 따른 신장(대상)의 평균(통계량)

![2](https://user-images.githubusercontent.com/41605276/126330387-8cb9330d-0eff-4a12-b51d-eab8de4091c1.PNG)

- 실습

고객별 인구통계정보에 고객별 구매기록을 병합하는 실습

먼저 데이터를 불러오자


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/part-4.-머신러닝을-위한-필수-전처리/Part 4. 머신러닝을 위한 필수 전처리/데이터/")

demo_df = pd.read_csv("고객별_인구통계정보.csv", engine = "python")
demo_df
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
      <th>고객ID</th>
      <th>나이</th>
      <th>성별</th>
      <th>주소</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>고객1</td>
      <td>48</td>
      <td>남</td>
      <td>A아파트</td>
    </tr>
    <tr>
      <td>1</td>
      <td>고객2</td>
      <td>40</td>
      <td>남</td>
      <td>B아파트</td>
    </tr>
    <tr>
      <td>2</td>
      <td>고객3</td>
      <td>37</td>
      <td>여</td>
      <td>C아파트</td>
    </tr>
    <tr>
      <td>3</td>
      <td>고객4</td>
      <td>35</td>
      <td>여</td>
      <td>A연립주택</td>
    </tr>
    <tr>
      <td>4</td>
      <td>고객5</td>
      <td>42</td>
      <td>남</td>
      <td>B연립주택</td>
    </tr>
    <tr>
      <td>5</td>
      <td>고객6</td>
      <td>44</td>
      <td>남</td>
      <td>C연립주택</td>
    </tr>
    <tr>
      <td>6</td>
      <td>고객7</td>
      <td>47</td>
      <td>남</td>
      <td>A단독주택</td>
    </tr>
    <tr>
      <td>7</td>
      <td>고객8</td>
      <td>38</td>
      <td>여</td>
      <td>B단독주택</td>
    </tr>
    <tr>
      <td>8</td>
      <td>고객9</td>
      <td>33</td>
      <td>여</td>
      <td>D아파트</td>
    </tr>
    <tr>
      <td>9</td>
      <td>고객10</td>
      <td>30</td>
      <td>남</td>
      <td>E아파트</td>
    </tr>
  </tbody>
</table>
</div>



구매기록도 확인해보면 한명의 고객이 여러번 구매한 기록도 확인할 수 있다.


```python
purchasing_df = pd.read_csv("고객별_구매금액.csv", engine = 'python')
purchasing_df
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
      <th>고객ID</th>
      <th>구매금액</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>고객10</td>
      <td>45990</td>
    </tr>
    <tr>
      <td>1</td>
      <td>고객2</td>
      <td>29800</td>
    </tr>
    <tr>
      <td>2</td>
      <td>고객7</td>
      <td>31400</td>
    </tr>
    <tr>
      <td>3</td>
      <td>고객5</td>
      <td>35300</td>
    </tr>
    <tr>
      <td>4</td>
      <td>고객2</td>
      <td>41200</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>995</td>
      <td>고객8</td>
      <td>41270</td>
    </tr>
    <tr>
      <td>996</td>
      <td>고객3</td>
      <td>7520</td>
    </tr>
    <tr>
      <td>997</td>
      <td>고객2</td>
      <td>7210</td>
    </tr>
    <tr>
      <td>998</td>
      <td>고객5</td>
      <td>37760</td>
    </tr>
    <tr>
      <td>999</td>
      <td>고객10</td>
      <td>11610</td>
    </tr>
  </tbody>
</table>
<p>1000 rows × 2 columns</p>
</div>



groupby 함수를 이용해서 고객별로 구매금액을 합산하여 데이터를 요약해보자

대상 변수의 이름이 그대도 시리즈의 이름이 되기 때문에 이때 적절한 변수명으로 변경해줘야 한다.

(구매금액 --> 구매금액합계)


```python
purchasing_aggregated_df = purchasing_df.groupby('고객ID')['구매금액'].sum()
purchasing_aggregated_df = purchasing_aggregated_df.rename('구매금액합계') 
print(type(purchasing_aggregated_df))
purchasing_aggregated_df
```

    <class 'pandas.core.series.Series'>
    




    고객ID
    고객1     2494480
    고객10    2644200
    고객2     2573880
    고객3     2998590
    고객4     2875420
    고객5     2628430
    고객6     2419410
    고객7     2709620
    고객8     2914460
    고객9     2283150
    Name: 구매금액합계, dtype: int64



고객별 인구통계정보에 요약한 고객별 구매기록을 병합해보자


```python
merged_df = pd.merge(demo_df, purchasing_aggregated_df, left_on = '고객ID', right_index = True)
merged_df
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
      <th>고객ID</th>
      <th>나이</th>
      <th>성별</th>
      <th>주소</th>
      <th>구매금액합계</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>고객1</td>
      <td>48</td>
      <td>남</td>
      <td>A아파트</td>
      <td>2494480</td>
    </tr>
    <tr>
      <td>1</td>
      <td>고객2</td>
      <td>40</td>
      <td>남</td>
      <td>B아파트</td>
      <td>2573880</td>
    </tr>
    <tr>
      <td>2</td>
      <td>고객3</td>
      <td>37</td>
      <td>여</td>
      <td>C아파트</td>
      <td>2998590</td>
    </tr>
    <tr>
      <td>3</td>
      <td>고객4</td>
      <td>35</td>
      <td>여</td>
      <td>A연립주택</td>
      <td>2875420</td>
    </tr>
    <tr>
      <td>4</td>
      <td>고객5</td>
      <td>42</td>
      <td>남</td>
      <td>B연립주택</td>
      <td>2628430</td>
    </tr>
    <tr>
      <td>5</td>
      <td>고객6</td>
      <td>44</td>
      <td>남</td>
      <td>C연립주택</td>
      <td>2419410</td>
    </tr>
    <tr>
      <td>6</td>
      <td>고객7</td>
      <td>47</td>
      <td>남</td>
      <td>A단독주택</td>
      <td>2709620</td>
    </tr>
    <tr>
      <td>7</td>
      <td>고객8</td>
      <td>38</td>
      <td>여</td>
      <td>B단독주택</td>
      <td>2914460</td>
    </tr>
    <tr>
      <td>8</td>
      <td>고객9</td>
      <td>33</td>
      <td>여</td>
      <td>D아파트</td>
      <td>2283150</td>
    </tr>
    <tr>
      <td>9</td>
      <td>고객10</td>
      <td>30</td>
      <td>남</td>
      <td>E아파트</td>
      <td>2644200</td>
    </tr>
  </tbody>
</table>
</div>




```python
#하나의 변수를 추가하는 경우에 주로 사용하는 테크닉
#demo_df['구매금액합계_2'] = demo_df['고객ID'].replace(purchasing_aggregated_df.to_dict())
```
