---
layout: post
title: "데이터 파편화 문제 - 포맷이 다른 키 변수가 있는 경우"
tags: [데이터전처리]
comments: true
---

.


Data_Preprocessing_TIL(20210717)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 참조데이터가 필요없는 경우에서 데이터 병합하기

1) 시간과 날짜 컬럼 등은 데이터에 따라 포맷이 다른 경우가 있음

2) 키 변수의 포맷이 다른 두 데이터 프레임에 대해서 merge를 적용하면, 비정상적으로 병합이 이루어질 수 있으므로, 하나의 컬럼을 다른 컬럼의 포맷에 맞게 변경해주는 작업이 필요함

예를 들어서 아래 그림과 같이 두개의 데이터 프레임을 "날짜" 컬럼을 기준으로 병합을 하는데 포맷을 통일하지 않고 그대로 merge하게 되면 빈깡통의 dataframe이 만들어지게 된다. 당연하게도 머지하는 컬럼에서 일치하는 데이터가 없기 때문이다. 포맷을 통일하지 않았기 때문에 머지하는 두개의 데이터는 다른 데이터로 인식하기 때문이다. 

이런 경우에는 2020년 7월 15일,2020년 7월 16일,2020년 7월 17일 데이터를 python의 apply 함수를 이용해서 '년','월'.'일'을 뺀다음에 각각의 자리는 '/'로 해주면 된다. 그래서 전부 2020/07/15,2020/07/16,2020/07/17와 같이 merge하고자 하는 데이터와 통일해줘야 한다.

![1](https://user-images.githubusercontent.com/41605276/126024846-8296dd94-b829-45db-b448-962882319af9.PNG)

- 위에서 활용하는 함수 : Series.apply

1) Series에 있는 모든 요소에 function을 일괄 적용하는 함수

** python 내장함수인 map 함수와 유사

2) 입력 : function(series의 한 요소를 처리하는 함수)

![2](https://user-images.githubusercontent.com/41605276/126025128-09797070-f38c-431c-baf0-ce339ddca0a6.PNG)

apply 함수는 머신러닝 코드의 효율성을 위해 매우 자주 사용됨

- 실습

두개의 데이터 프레임을 "날짜" 컬럼을 기준으로 merge하는데 병합하는 데이터 프레임의 포맷이 서로 달라서 이를 통일해서 merge하는 실습


```python
# data 불러오기
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/part-4.-머신러닝을-위한-필수-전처리/Part 4. 머신러닝을 위한 필수 전처리/데이터/")

df1 = pd.read_csv("날짜포맷이다른데이터1.csv")
df1.head()
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
      <th>날짜</th>
      <th>V1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2018-01-01</td>
      <td>0.330646</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2018-01-02</td>
      <td>0.418242</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2018-01-03</td>
      <td>0.307254</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2018-01-04</td>
      <td>0.441689</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2018-01-05</td>
      <td>0.402440</td>
    </tr>
  </tbody>
</table>
</div>




```python
df2 = pd.read_csv("날짜포맷이다른데이터2.csv")
df2.head()
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
      <th>날짜</th>
      <th>V2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2018년 1월 1일</td>
      <td>0.936350</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2018년 1월 2일</td>
      <td>0.658967</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2018년 1월 3일</td>
      <td>0.895686</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2018년 1월 4일</td>
      <td>0.047194</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2018년 1월 5일</td>
      <td>0.520925</td>
    </tr>
  </tbody>
</table>
</div>



df1의 날짜 타입을 df2의 날짜 타입으로 변경하는 함수 작성 및 적용


```python
def date_type_converter(value):
    YYYY, MM, DD = value.split('-')
    return YYYY + "년 " + str(int(MM)) + "월 " + str(int(DD)) + "일"

df1['날짜'] = df1['날짜'].apply(date_type_converter)
df1.head()
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
      <th>날짜</th>
      <th>V1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2018년 1월 1일</td>
      <td>0.330646</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2018년 1월 2일</td>
      <td>0.418242</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2018년 1월 3일</td>
      <td>0.307254</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2018년 1월 4일</td>
      <td>0.441689</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2018년 1월 5일</td>
      <td>0.402440</td>
    </tr>
  </tbody>
</table>
</div>



데이터 병합


```python
merged_df = pd.merge(df1, df2, on = '날짜')
merged_df.head()
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
      <th>날짜</th>
      <th>V1</th>
      <th>V2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2018년 1월 1일</td>
      <td>0.330646</td>
      <td>0.936350</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2018년 1월 2일</td>
      <td>0.418242</td>
      <td>0.658967</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2018년 1월 3일</td>
      <td>0.307254</td>
      <td>0.895686</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2018년 1월 4일</td>
      <td>0.441689</td>
      <td>0.047194</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2018년 1월 5일</td>
      <td>0.402440</td>
      <td>0.520925</td>
    </tr>
  </tbody>
</table>
</div>


