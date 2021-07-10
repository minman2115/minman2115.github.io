---
layout: post
title: "데이터 파편화 문제 - 데이터 파일이 분산 저장되어 있는 경우"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210710)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 파편화 문제가 뭐냐

지도학습 모델을 학습하려면 아래와 같이 반드시 통합된 데이터 집합이 필요함

하지만 많은 경우에 데이터가 두개 이상으로 파일들로 나뉘어져 있어서, 이들을 통합하는 전처리를 수행해야함

예를 들어서 센서, 로그, 거래 데이터 등 데이터가 큰 경우에는 시간 또는 ID등에 따라 분할저장되어 있는게 일반적이다.

![1](https://user-images.githubusercontent.com/41605276/125153455-c31d1200-e18e-11eb-9488-068932d63d85.PNG)



- 파편화 문제를 해결하는 방법 : pandas.concat 함수를 이용해서 해결

![2](https://user-images.githubusercontent.com/41605276/125153543-60784600-e18f-11eb-9556-27c7274231b1.PNG)

통합해야 하는 파일개수가 많으면 빈 데이터프레임을 생성한 뒤, 이 데이터프레임과 반복문을 사용하여 불러온 데이터를 concat 함수를 이용하면 효율적으로 통합할 수 있다.


- pandas.concat

둘 이상의 데이터 프레임을 이어 붙이는데 사용하는 함수

주요 입력

1) objs : 데이터 프레임을 요소로하는 리스트로 입력순서대로 병합이 되는 형태

입력예시 : [df1, df2]

2) ignore_index : True이면 기존 인덱스를 무시하고 새로운 인덱스를 부여하며, False면 기존 인덱스를 사용

![3](https://user-images.githubusercontent.com/41605276/125153952-34aa8f80-e192-11eb-913b-1e22ea5b4ac0.PNG)

3) axis : 0 이면 행 단위로 병합을 수행하며, 1이면 열단위로 병합을 수행

- pandas.concat에서 axis 옵션

axis 옵션은 numpy 및 pandas의 많은 함수에서 사용되며 연산할때 축의 방향을 결정함

axis가 0이면 행을, 1이면 열을 나타내지만 이렇게만 기억하면 안됨

예시 1) `sum(axis=0)` : 열 기준 합

예시 2) `concat([df1, df2],axis=0)` : 행 단위 병합

![4](https://user-images.githubusercontent.com/41605276/125154074-1b561300-e193-11eb-8098-84b219c6a27b.PNG)

axis 옵션은 그 함수의 결과구조가 벡터 형태(1차원)인지, 행렬 형태(2차원) 인지에 따라, 그 역할이 다르다.

결과

1) asix:0일때 벡터=행백터

2) asix:0일때 행렬은 연산과정이 행 기준

3) asix:1일때 벡터=열백터

4) asix:1일때 행렬은 연산과정이 열 기준

- 파편화 문제를 해결시 사용하면 유용한 함수 : os.listdir

path상에 있는 모든 파일명을 리스트 형태로 반환

![5](https://user-images.githubusercontent.com/41605276/125154657-ff07a580-e195-11eb-8dbf-b6df114a010b.PNG)

- 실습

날짜별로 에너지 사용량 예측 데이터 파일들이 나누어져 있는데 이들을 5월 데이터는 제외하고 하나로 통합해보는 실습


```python
# data 불러오기
import os
import pandas as pd

# pwd 시 경로 : /c/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터
os.chdir(r"C:/Users/user/Desktop/aa/part-4.-머신러닝을-위한-필수-전처리/Part 4. 머신러닝을 위한 필수 전처리/데이터/")

# 파일 목록 확인
os.listdir("에너지 사용량 예측")
```




    ['1월11일.csv',
     '1월12일.csv',
     '1월13일.csv',
     '1월14일.csv',
     '1월15일.csv',
     '1월16일.csv',
     '1월17일.csv',
     '1월18일.csv',
     '1월19일.csv',
     '1월20일.csv',
     '1월21일.csv',
     '1월22일.csv',
     '1월23일.csv',
     '1월24일.csv',
     '1월25일.csv',
     '1월26일.csv',
     '1월27일.csv',
     '1월28일.csv',
     '1월29일.csv',
     '1월30일.csv',
     '1월31일.csv',
     '2월10일.csv',
     '2월11일.csv',
     '2월12일.csv',
     '2월13일.csv',
     '2월14일.csv',
     '2월15일.csv',
     '2월16일.csv',
     '2월17일.csv',
     '2월18일.csv',
     '2월19일.csv',
     '2월1일.csv',
     '2월20일.csv',
     '2월21일.csv',
     '2월22일.csv',
     '2월23일.csv',
     '2월24일.csv',
     '2월25일.csv',
     '2월26일.csv',
     '2월27일.csv',
     '2월28일.csv',
     '2월29일.csv',
     '2월2일.csv',
     '2월3일.csv',
     '2월4일.csv',
     '2월5일.csv',
     '2월6일.csv',
     '2월7일.csv',
     '2월8일.csv',
     '2월9일.csv',
     '3월10일.csv',
     '3월11일.csv',
     '3월12일.csv',
     '3월13일.csv',
     '3월14일.csv',
     '3월15일.csv',
     '3월16일.csv',
     '3월17일.csv',
     '3월18일.csv',
     '3월19일.csv',
     '3월1일.csv',
     '3월20일.csv',
     '3월21일.csv',
     '3월22일.csv',
     '3월23일.csv',
     '3월24일.csv',
     '3월25일.csv',
     '3월26일.csv',
     '3월27일.csv',
     '3월28일.csv',
     '3월29일.csv',
     '3월2일.csv',
     '3월30일.csv',
     '3월31일.csv',
     '3월3일.csv',
     '3월4일.csv',
     '3월5일.csv',
     '3월6일.csv',
     '3월7일.csv',
     '3월8일.csv',
     '3월9일.csv',
     '4월10일.csv',
     '4월11일.csv',
     '4월12일.csv',
     '4월13일.csv',
     '4월14일.csv',
     '4월15일.csv',
     '4월16일.csv',
     '4월17일.csv',
     '4월18일.csv',
     '4월19일.csv',
     '4월1일.csv',
     '4월20일.csv',
     '4월21일.csv',
     '4월22일.csv',
     '4월23일.csv',
     '4월24일.csv',
     '4월25일.csv',
     '4월26일.csv',
     '4월27일.csv',
     '4월28일.csv',
     '4월29일.csv',
     '4월2일.csv',
     '4월30일.csv',
     '4월3일.csv',
     '4월4일.csv',
     '4월5일.csv',
     '4월6일.csv',
     '4월7일.csv',
     '4월8일.csv',
     '4월9일.csv',
     '5월10일.csv',
     '5월11일.csv',
     '5월12일.csv',
     '5월13일.csv',
     '5월14일.csv',
     '5월15일.csv',
     '5월16일.csv',
     '5월17일.csv',
     '5월18일.csv',
     '5월19일.csv',
     '5월1일.csv',
     '5월20일.csv',
     '5월21일.csv',
     '5월22일.csv',
     '5월23일.csv',
     '5월24일.csv',
     '5월25일.csv',
     '5월26일.csv',
     '5월27일.csv',
     '5월2일.csv',
     '5월3일.csv',
     '5월4일.csv',
     '5월5일.csv',
     '5월6일.csv',
     '5월7일.csv',
     '5월8일.csv',
     '5월9일.csv',
     'desktop.ini']




```python
# 빈 데이터 프레임 생성
merged_df = pd.DataFrame() 

# file이 에너지 사용량 예측 폴더 내에 있는 파일명을 순회
for file in os.listdir("에너지 사용량 예측"): 
    # 5월 데이터는 제외하고 부른다고 가정
    if '5월' not in file and ".csv" in file:
        # 주의: 현재 경로는 ../데이터이므로 에너지 사용량 예측 폴더를 명시해줘야 함
        df = pd.read_csv("에너지 사용량 예측/" + file, encoding='euc-kr') 
        merged_df = pd.concat([merged_df, df], axis = 0, ignore_index = True,sort=False)

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
      <th>date</th>
      <th>Appliances</th>
      <th>lights</th>
      <th>T1</th>
      <th>RH_1</th>
      <th>T2</th>
      <th>RH_2</th>
      <th>T3</th>
      <th>RH_3</th>
      <th>T4</th>
      <th>RH_4</th>
      <th>T5</th>
      <th>RH_5</th>
      <th>T6</th>
      <th>RH_6</th>
      <th>T7</th>
      <th>RH_7</th>
      <th>T8</th>
      <th>RH_8</th>
      <th>T9</th>
      <th>RH_9</th>
      <th>T_out</th>
      <th>Press_mm_hg</th>
      <th>RH_out</th>
      <th>Windspeed</th>
      <th>Visibility</th>
      <th>Tdewpoint</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2016-01-11 17:00:00</td>
      <td>60</td>
      <td>30.0</td>
      <td>19.890000</td>
      <td>47.596667</td>
      <td>NaN</td>
      <td>44.790000</td>
      <td>19.79</td>
      <td>44.730000</td>
      <td>19.000000</td>
      <td>45.566667</td>
      <td>17.166667</td>
      <td>55.200000</td>
      <td>7.026667</td>
      <td>NaN</td>
      <td>17.200000</td>
      <td>41.626667</td>
      <td>18.200000</td>
      <td>48.900000</td>
      <td>17.033333</td>
      <td>45.530000</td>
      <td>6.600000</td>
      <td>733.500000</td>
      <td>92.000000</td>
      <td>7.000000</td>
      <td>63.000000</td>
      <td>5.300000</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2016-01-11 17:10:00</td>
      <td>60</td>
      <td>NaN</td>
      <td>19.890000</td>
      <td>46.693333</td>
      <td>19.200000</td>
      <td>44.722500</td>
      <td>19.79</td>
      <td>44.790000</td>
      <td>19.000000</td>
      <td>45.992500</td>
      <td>17.166667</td>
      <td>55.200000</td>
      <td>6.833333</td>
      <td>84.063333</td>
      <td>17.200000</td>
      <td>41.560000</td>
      <td>18.200000</td>
      <td>48.863333</td>
      <td>17.066667</td>
      <td>45.560000</td>
      <td>6.483333</td>
      <td>733.600000</td>
      <td>92.000000</td>
      <td>6.666667</td>
      <td>59.166667</td>
      <td>5.200000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2016-01-11 17:20:00</td>
      <td>50</td>
      <td>30.0</td>
      <td>19.890000</td>
      <td>46.300000</td>
      <td>19.200000</td>
      <td>44.626667</td>
      <td>19.79</td>
      <td>44.933333</td>
      <td>18.926667</td>
      <td>45.890000</td>
      <td>17.166667</td>
      <td>55.090000</td>
      <td>6.560000</td>
      <td>83.156667</td>
      <td>17.200000</td>
      <td>41.433333</td>
      <td>18.200000</td>
      <td>48.730000</td>
      <td>17.000000</td>
      <td>45.500000</td>
      <td>6.366667</td>
      <td>733.700000</td>
      <td>92.000000</td>
      <td>NaN</td>
      <td>55.333333</td>
      <td>5.100000</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2016-01-11 17:30:00</td>
      <td>50</td>
      <td>40.0</td>
      <td>19.890000</td>
      <td>46.066667</td>
      <td>19.200000</td>
      <td>44.590000</td>
      <td>19.79</td>
      <td>45.000000</td>
      <td>18.890000</td>
      <td>45.723333</td>
      <td>17.166667</td>
      <td>55.090000</td>
      <td>6.433333</td>
      <td>83.423333</td>
      <td>17.133333</td>
      <td>41.290000</td>
      <td>18.100000</td>
      <td>48.590000</td>
      <td>17.000000</td>
      <td>NaN</td>
      <td>6.250000</td>
      <td>733.800000</td>
      <td>92.000000</td>
      <td>6.000000</td>
      <td>51.500000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2016-01-11 17:40:00</td>
      <td>60</td>
      <td>40.0</td>
      <td>19.890000</td>
      <td>46.333333</td>
      <td>19.200000</td>
      <td>44.530000</td>
      <td>19.79</td>
      <td>45.000000</td>
      <td>18.890000</td>
      <td>45.530000</td>
      <td>17.200000</td>
      <td>55.090000</td>
      <td>6.366667</td>
      <td>84.893333</td>
      <td>17.200000</td>
      <td>41.230000</td>
      <td>18.100000</td>
      <td>48.590000</td>
      <td>17.000000</td>
      <td>NaN</td>
      <td>6.133333</td>
      <td>733.900000</td>
      <td>92.000000</td>
      <td>5.666667</td>
      <td>NaN</td>
      <td>4.900000</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
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
      <td>15877</td>
      <td>2016-04-09 23:10:00</td>
      <td>50</td>
      <td>0.0</td>
      <td>22.323333</td>
      <td>41.126667</td>
      <td>20.000000</td>
      <td>43.700000</td>
      <td>22.79</td>
      <td>39.700000</td>
      <td>20.566667</td>
      <td>38.500000</td>
      <td>NaN</td>
      <td>46.790000</td>
      <td>7.690000</td>
      <td>54.160000</td>
      <td>21.700000</td>
      <td>35.933333</td>
      <td>23.100000</td>
      <td>40.222857</td>
      <td>20.000000</td>
      <td>39.400000</td>
      <td>7.933333</td>
      <td>750.283333</td>
      <td>92.166667</td>
      <td>2.833333</td>
      <td>26.666667</td>
      <td>6.666667</td>
    </tr>
    <tr>
      <td>15878</td>
      <td>2016-04-09 23:20:00</td>
      <td>50</td>
      <td>0.0</td>
      <td>22.290000</td>
      <td>41.060000</td>
      <td>19.890000</td>
      <td>NaN</td>
      <td>22.79</td>
      <td>39.700000</td>
      <td>20.566667</td>
      <td>38.500000</td>
      <td>20.000000</td>
      <td>46.700000</td>
      <td>7.590000</td>
      <td>54.596667</td>
      <td>21.700000</td>
      <td>36.060000</td>
      <td>23.100000</td>
      <td>40.312000</td>
      <td>20.066667</td>
      <td>39.400000</td>
      <td>7.766667</td>
      <td>750.366667</td>
      <td>92.333333</td>
      <td>2.666667</td>
      <td>29.333333</td>
      <td>6.533333</td>
    </tr>
    <tr>
      <td>15879</td>
      <td>2016-04-09 23:30:00</td>
      <td>60</td>
      <td>0.0</td>
      <td>22.290000</td>
      <td>41.000000</td>
      <td>19.823333</td>
      <td>43.790000</td>
      <td>22.79</td>
      <td>39.700000</td>
      <td>20.500000</td>
      <td>38.500000</td>
      <td>20.085714</td>
      <td>46.777143</td>
      <td>7.530000</td>
      <td>54.930000</td>
      <td>21.700000</td>
      <td>36.200000</td>
      <td>23.028571</td>
      <td>40.321429</td>
      <td>20.066667</td>
      <td>39.526667</td>
      <td>7.600000</td>
      <td>750.450000</td>
      <td>92.500000</td>
      <td>2.500000</td>
      <td>32.000000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>15880</td>
      <td>2016-04-09 23:40:00</td>
      <td>60</td>
      <td>0.0</td>
      <td>22.200000</td>
      <td>40.900000</td>
      <td>19.790000</td>
      <td>43.790000</td>
      <td>22.79</td>
      <td>39.700000</td>
      <td>20.566667</td>
      <td>38.530000</td>
      <td>20.040000</td>
      <td>46.736000</td>
      <td>7.500000</td>
      <td>55.326667</td>
      <td>21.700000</td>
      <td>36.333333</td>
      <td>23.000000</td>
      <td>40.290000</td>
      <td>20.100000</td>
      <td>39.826667</td>
      <td>7.433333</td>
      <td>750.533333</td>
      <td>92.666667</td>
      <td>2.333333</td>
      <td>34.666667</td>
      <td>6.266667</td>
    </tr>
    <tr>
      <td>15881</td>
      <td>2016-04-09 23:50:00</td>
      <td>60</td>
      <td>0.0</td>
      <td>22.200000</td>
      <td>40.900000</td>
      <td>NaN</td>
      <td>43.790000</td>
      <td>22.79</td>
      <td>39.700000</td>
      <td>20.500000</td>
      <td>38.590000</td>
      <td>20.028571</td>
      <td>46.725714</td>
      <td>7.433333</td>
      <td>55.400000</td>
      <td>21.700000</td>
      <td>36.530000</td>
      <td>22.905714</td>
      <td>40.367143</td>
      <td>20.100000</td>
      <td>39.966667</td>
      <td>7.266667</td>
      <td>750.616667</td>
      <td>92.833333</td>
      <td>2.166667</td>
      <td>37.333333</td>
      <td>6.133333</td>
    </tr>
  </tbody>
</table>
<p>15882 rows × 27 columns</p>
</div>




```python
merged_df.sort_values('date', inplace = True) # 날짜 기준 정렬 수행 
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
      <th>date</th>
      <th>Appliances</th>
      <th>lights</th>
      <th>T1</th>
      <th>RH_1</th>
      <th>T2</th>
      <th>RH_2</th>
      <th>T3</th>
      <th>RH_3</th>
      <th>T4</th>
      <th>RH_4</th>
      <th>T5</th>
      <th>RH_5</th>
      <th>T6</th>
      <th>RH_6</th>
      <th>T7</th>
      <th>RH_7</th>
      <th>T8</th>
      <th>RH_8</th>
      <th>T9</th>
      <th>RH_9</th>
      <th>T_out</th>
      <th>Press_mm_hg</th>
      <th>RH_out</th>
      <th>Windspeed</th>
      <th>Visibility</th>
      <th>Tdewpoint</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2016-01-11 17:00:00</td>
      <td>60</td>
      <td>30.0</td>
      <td>19.89</td>
      <td>47.596667</td>
      <td>NaN</td>
      <td>44.790000</td>
      <td>19.79</td>
      <td>44.730000</td>
      <td>19.000000</td>
      <td>45.566667</td>
      <td>17.166667</td>
      <td>55.200000</td>
      <td>7.026667</td>
      <td>NaN</td>
      <td>17.200000</td>
      <td>41.626667</td>
      <td>18.20</td>
      <td>48.900000</td>
      <td>17.033333</td>
      <td>45.530000</td>
      <td>6.600000</td>
      <td>733.500000</td>
      <td>92.000000</td>
      <td>7.000000</td>
      <td>63.000000</td>
      <td>5.30</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2016-01-11 17:10:00</td>
      <td>60</td>
      <td>NaN</td>
      <td>19.89</td>
      <td>46.693333</td>
      <td>19.200000</td>
      <td>44.722500</td>
      <td>19.79</td>
      <td>44.790000</td>
      <td>19.000000</td>
      <td>45.992500</td>
      <td>17.166667</td>
      <td>55.200000</td>
      <td>6.833333</td>
      <td>84.063333</td>
      <td>17.200000</td>
      <td>41.560000</td>
      <td>18.20</td>
      <td>48.863333</td>
      <td>17.066667</td>
      <td>45.560000</td>
      <td>6.483333</td>
      <td>733.600000</td>
      <td>92.000000</td>
      <td>6.666667</td>
      <td>59.166667</td>
      <td>5.20</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2016-01-11 17:20:00</td>
      <td>50</td>
      <td>30.0</td>
      <td>19.89</td>
      <td>46.300000</td>
      <td>19.200000</td>
      <td>44.626667</td>
      <td>19.79</td>
      <td>44.933333</td>
      <td>18.926667</td>
      <td>45.890000</td>
      <td>17.166667</td>
      <td>55.090000</td>
      <td>6.560000</td>
      <td>83.156667</td>
      <td>17.200000</td>
      <td>41.433333</td>
      <td>18.20</td>
      <td>48.730000</td>
      <td>17.000000</td>
      <td>45.500000</td>
      <td>6.366667</td>
      <td>733.700000</td>
      <td>92.000000</td>
      <td>NaN</td>
      <td>55.333333</td>
      <td>5.10</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2016-01-11 17:30:00</td>
      <td>50</td>
      <td>40.0</td>
      <td>19.89</td>
      <td>46.066667</td>
      <td>19.200000</td>
      <td>44.590000</td>
      <td>19.79</td>
      <td>45.000000</td>
      <td>18.890000</td>
      <td>45.723333</td>
      <td>17.166667</td>
      <td>55.090000</td>
      <td>6.433333</td>
      <td>83.423333</td>
      <td>17.133333</td>
      <td>41.290000</td>
      <td>18.10</td>
      <td>48.590000</td>
      <td>17.000000</td>
      <td>NaN</td>
      <td>6.250000</td>
      <td>733.800000</td>
      <td>92.000000</td>
      <td>6.000000</td>
      <td>51.500000</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2016-01-11 17:40:00</td>
      <td>60</td>
      <td>40.0</td>
      <td>19.89</td>
      <td>46.333333</td>
      <td>19.200000</td>
      <td>44.530000</td>
      <td>19.79</td>
      <td>45.000000</td>
      <td>18.890000</td>
      <td>45.530000</td>
      <td>17.200000</td>
      <td>55.090000</td>
      <td>6.366667</td>
      <td>84.893333</td>
      <td>17.200000</td>
      <td>41.230000</td>
      <td>18.10</td>
      <td>48.590000</td>
      <td>17.000000</td>
      <td>NaN</td>
      <td>6.133333</td>
      <td>733.900000</td>
      <td>92.000000</td>
      <td>5.666667</td>
      <td>NaN</td>
      <td>4.90</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
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
      <td>14869</td>
      <td>2016-04-30 23:10:00</td>
      <td>40</td>
      <td>0.0</td>
      <td>21.29</td>
      <td>38.500000</td>
      <td>18.633333</td>
      <td>41.290000</td>
      <td>23.29</td>
      <td>36.290000</td>
      <td>19.790000</td>
      <td>NaN</td>
      <td>19.500000</td>
      <td>47.466667</td>
      <td>5.000000</td>
      <td>35.760000</td>
      <td>19.500000</td>
      <td>NaN</td>
      <td>22.39</td>
      <td>38.363333</td>
      <td>19.200000</td>
      <td>37.790000</td>
      <td>5.216667</td>
      <td>763.366667</td>
      <td>82.666667</td>
      <td>2.833333</td>
      <td>40.000000</td>
      <td>2.45</td>
    </tr>
    <tr>
      <td>14870</td>
      <td>2016-04-30 23:20:00</td>
      <td>20</td>
      <td>0.0</td>
      <td>21.26</td>
      <td>38.430000</td>
      <td>18.600000</td>
      <td>41.326667</td>
      <td>23.20</td>
      <td>36.290000</td>
      <td>19.790000</td>
      <td>38.060000</td>
      <td>19.500000</td>
      <td>47.400000</td>
      <td>4.830000</td>
      <td>36.256667</td>
      <td>19.500000</td>
      <td>NaN</td>
      <td>22.39</td>
      <td>39.000000</td>
      <td>19.200000</td>
      <td>37.863333</td>
      <td>5.033333</td>
      <td>763.433333</td>
      <td>83.333333</td>
      <td>2.666667</td>
      <td>40.000000</td>
      <td>2.40</td>
    </tr>
    <tr>
      <td>14871</td>
      <td>2016-04-30 23:30:00</td>
      <td>40</td>
      <td>0.0</td>
      <td>21.20</td>
      <td>38.290000</td>
      <td>18.533333</td>
      <td>41.400000</td>
      <td>23.20</td>
      <td>36.290000</td>
      <td>19.790000</td>
      <td>38.000000</td>
      <td>19.500000</td>
      <td>47.400000</td>
      <td>4.690000</td>
      <td>37.196667</td>
      <td>19.500000</td>
      <td>31.500000</td>
      <td>22.39</td>
      <td>39.400000</td>
      <td>19.200000</td>
      <td>38.030000</td>
      <td>4.850000</td>
      <td>763.500000</td>
      <td>84.000000</td>
      <td>2.500000</td>
      <td>40.000000</td>
      <td>2.35</td>
    </tr>
    <tr>
      <td>14872</td>
      <td>2016-04-30 23:40:00</td>
      <td>30</td>
      <td>0.0</td>
      <td>21.20</td>
      <td>38.260000</td>
      <td>18.500000</td>
      <td>41.450000</td>
      <td>23.20</td>
      <td>36.290000</td>
      <td>19.790000</td>
      <td>37.845000</td>
      <td>19.500000</td>
      <td>47.400000</td>
      <td>4.690000</td>
      <td>38.395000</td>
      <td>NaN</td>
      <td>31.500000</td>
      <td>22.29</td>
      <td>39.656667</td>
      <td>19.200000</td>
      <td>38.090000</td>
      <td>4.666667</td>
      <td>763.566667</td>
      <td>84.666667</td>
      <td>2.333333</td>
      <td>40.000000</td>
      <td>2.30</td>
    </tr>
    <tr>
      <td>14873</td>
      <td>2016-04-30 23:50:00</td>
      <td>70</td>
      <td>0.0</td>
      <td>21.20</td>
      <td>38.200000</td>
      <td>18.463333</td>
      <td>41.466667</td>
      <td>23.20</td>
      <td>36.363333</td>
      <td>19.790000</td>
      <td>37.790000</td>
      <td>19.500000</td>
      <td>47.345000</td>
      <td>4.726667</td>
      <td>39.600000</td>
      <td>19.500000</td>
      <td>31.533333</td>
      <td>22.23</td>
      <td>39.730000</td>
      <td>19.166667</td>
      <td>38.126667</td>
      <td>4.483333</td>
      <td>NaN</td>
      <td>85.333333</td>
      <td>2.166667</td>
      <td>40.000000</td>
      <td>2.25</td>
    </tr>
  </tbody>
</table>
<p>15882 rows × 27 columns</p>
</div>


