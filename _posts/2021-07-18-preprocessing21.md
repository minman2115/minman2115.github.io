---
layout: post
title: "데이터 파편화 문제 - 거리 기반 병합이 필요한 경우"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210718)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 문제정의 

아파트 가격예측 등 지역이 포함되는 문제에서 주소나 위치변수 등을 기준으로 거리가 가까운 레코드 및 관련 통계치를 통합해야 하는 경우가 종종 있음

지리나 위치데이터를 다루는데 가끔 발생하는 경우이다.

- 해결방안

step 1) 데이터별로 레코드 간 거리를 나타내는 거리행렬을 생성

step 2) 거리행렬의 행 혹은 열 기준 최소 값을 가지는 인덱스를 바탕으로 이웃을 탐색

step 3) 이웃을 기존 데이터에 merge

![1](https://user-images.githubusercontent.com/41605276/126055739-a23aabb3-7d6d-468c-9c28-a051f8ab8240.png)

- 해결을 위해서 자주쓰이는 함수 : scipy.spatial.distance.cdist

1) 두개의 행렬을 바탕으로 거리행렬을 반환하는 함수

2) 주요입력

두개의 데이터 프레임을 XA와 XB를 입력받는다. 아래에 distance에서 cityblock은 멘하탄거리이다. XA와 XB의 컬럼개수는 같아야 한다. 물론 시계열 데이터나 텍스트 데이터인 경우에는 컬럼개수가 다를 수 있다.

![2](https://user-images.githubusercontent.com/41605276/126055789-ac7032c7-7ff4-4fa6-9ac5-021b821c6f9a.PNG)

- 해결을 위해서 자주쓰이는 함수 : ndarray.argsort

1) 작은값부터 순서대로 데이터 위치를 변환하는 함수로, 이웃을 찾는데 주로 활용하는 함수다. 인덱스를 sort하는 함수로 위의 경우에는 최소값을 갖는 인덱스를 찾는데 활용된다.

2) 주요입력

![3](https://user-images.githubusercontent.com/41605276/126056088-e4b7202a-1595-469e-a091-e46cd91faa03.png)


- 실습

아파트별로 가장가까운 지하철역 정보를 만드는 실습


```python
# data 불러오기
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/part-4.-머신러닝을-위한-필수-전처리/Part 4. 머신러닝을 위한 필수 전처리/데이터/")

# 아파트 관련 데이터 불러오기
df1 = pd.read_csv("2019년_서울_아파트매매_실거래가.csv", encoding = "cp949")
df2 = pd.read_csv("2019년_서울시_아파트주소.csv", encoding = "cp949")

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
      <th>거래일자</th>
      <th>법정동</th>
      <th>도로명</th>
      <th>아파트</th>
      <th>층</th>
      <th>전용면적</th>
      <th>건축년도</th>
      <th>거래금액</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2019-10-05</td>
      <td>면목동</td>
      <td>용마산로</td>
      <td>현대</td>
      <td>2</td>
      <td>84.81</td>
      <td>1994</td>
      <td>52000</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2019-10-07</td>
      <td>면목동</td>
      <td>중랑천로</td>
      <td>신성</td>
      <td>5</td>
      <td>59.91</td>
      <td>1998</td>
      <td>37000</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2019-10-09</td>
      <td>면목동</td>
      <td>용마산로</td>
      <td>현대</td>
      <td>19</td>
      <td>84.09</td>
      <td>1994</td>
      <td>57500</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2019-10-10</td>
      <td>면목동</td>
      <td>사가정로41길</td>
      <td>동원베네스트</td>
      <td>5</td>
      <td>84.86</td>
      <td>2003</td>
      <td>56000</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2019-10-14</td>
      <td>면목동</td>
      <td>용마산로</td>
      <td>현대</td>
      <td>9</td>
      <td>72.63</td>
      <td>1994</td>
      <td>50500</td>
    </tr>
  </tbody>
</table>
</div>




```python
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
      <th>시군구명</th>
      <th>읍면동명</th>
      <th>도로명</th>
      <th>건물명</th>
      <th>경도</th>
      <th>위도</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>종로구</td>
      <td>신교동</td>
      <td>필운대로</td>
      <td>월드빌</td>
      <td>126.966744</td>
      <td>37.586644</td>
    </tr>
    <tr>
      <td>1</td>
      <td>종로구</td>
      <td>숭인동</td>
      <td>숭인동1길</td>
      <td>삼성타운</td>
      <td>127.017780</td>
      <td>37.579576</td>
    </tr>
    <tr>
      <td>2</td>
      <td>종로구</td>
      <td>숭인동</td>
      <td>숭인동1나길</td>
      <td>삼성타운</td>
      <td>127.018026</td>
      <td>37.579582</td>
    </tr>
    <tr>
      <td>3</td>
      <td>종로구</td>
      <td>평창동</td>
      <td>평창6길</td>
      <td>월드빌</td>
      <td>126.973484</td>
      <td>37.620055</td>
    </tr>
    <tr>
      <td>4</td>
      <td>중구</td>
      <td>장충동1가</td>
      <td>장충단로6가길</td>
      <td>삼우빌라</td>
      <td>127.006113</td>
      <td>37.563739</td>
    </tr>
  </tbody>
</table>
</div>



두 데이터를 일단 이어붙인다.


```python
df = pd.merge(df1, df2,left_on = ['법정동', '도로명', '아파트'], right_on = ['읍면동명', '도로명', '건물명'])
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
      <th>거래일자</th>
      <th>법정동</th>
      <th>도로명</th>
      <th>아파트</th>
      <th>층</th>
      <th>전용면적</th>
      <th>건축년도</th>
      <th>거래금액</th>
      <th>시군구명</th>
      <th>읍면동명</th>
      <th>건물명</th>
      <th>경도</th>
      <th>위도</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2019-10-23</td>
      <td>면목동</td>
      <td>겸재로</td>
      <td>동서그랜드맨션</td>
      <td>4</td>
      <td>131.34</td>
      <td>1993</td>
      <td>43000</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>동서그랜드맨션</td>
      <td>127.075856</td>
      <td>37.588817</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2019-10-25</td>
      <td>면목동</td>
      <td>면목로</td>
      <td>킴스아파트</td>
      <td>4</td>
      <td>38.91</td>
      <td>2018</td>
      <td>28900</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>킴스아파트</td>
      <td>127.084734</td>
      <td>37.592972</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2019-10-29</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>12</td>
      <td>78.56</td>
      <td>2005</td>
      <td>45400</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2019-12-04</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>4</td>
      <td>81.96</td>
      <td>2005</td>
      <td>45500</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2019-12-26</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>3</td>
      <td>82.28</td>
      <td>2005</td>
      <td>45500</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
    </tr>
  </tbody>
</table>
</div>




```python
df3 = pd.read_excel("지하철역_위경도.xlsx")
df3.head()
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
      <th>역명</th>
      <th>위도</th>
      <th>경도</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>가양역[9호선]</td>
      <td>37.561560</td>
      <td>126.854261</td>
    </tr>
    <tr>
      <td>1</td>
      <td>완정역[2호선]</td>
      <td>37.592867</td>
      <td>126.673027</td>
    </tr>
    <tr>
      <td>2</td>
      <td>증미역[9호선]</td>
      <td>37.558144</td>
      <td>126.860617</td>
    </tr>
    <tr>
      <td>3</td>
      <td>마전역[2호선]</td>
      <td>37.597649</td>
      <td>126.667201</td>
    </tr>
    <tr>
      <td>4</td>
      <td>등촌역[9호선]</td>
      <td>37.550694</td>
      <td>126.865540</td>
    </tr>
  </tbody>
</table>
</div>



거리 행렬 생성을 위한 컬럼만 추출


```python
df_location = df[['경도', '위도']]
df3_location = df3[['경도', '위도']]

df_location.head()
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
      <th>경도</th>
      <th>위도</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>127.075856</td>
      <td>37.588817</td>
    </tr>
    <tr>
      <td>1</td>
      <td>127.084734</td>
      <td>37.592972</td>
    </tr>
    <tr>
      <td>2</td>
      <td>127.094170</td>
      <td>37.589641</td>
    </tr>
    <tr>
      <td>3</td>
      <td>127.094170</td>
      <td>37.589641</td>
    </tr>
    <tr>
      <td>4</td>
      <td>127.094170</td>
      <td>37.589641</td>
    </tr>
  </tbody>
</table>
</div>




```python
df3_location.head()
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
      <th>경도</th>
      <th>위도</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>126.854261</td>
      <td>37.561560</td>
    </tr>
    <tr>
      <td>1</td>
      <td>126.673027</td>
      <td>37.592867</td>
    </tr>
    <tr>
      <td>2</td>
      <td>126.860617</td>
      <td>37.558144</td>
    </tr>
    <tr>
      <td>3</td>
      <td>126.667201</td>
      <td>37.597649</td>
    </tr>
    <tr>
      <td>4</td>
      <td>126.865540</td>
      <td>37.550694</td>
    </tr>
  </tbody>
</table>
</div>




```python
# !pip install haversine # 위경도 거리 계산을 위한 모듈 설치
# 거리 행렬 생성
from scipy.spatial.distance import cdist
from haversine import haversine

# 위경도의 거리계산은 harversine 척도가 적합하다. 유클리디안 거리를 사용해도 된다.
dist_mat = cdist(df_location, df3_location, metric = haversine) # 'euclidean', 'cityblock', 'jaccard'
dist_mat
```




    array([[24.70758112, 44.79342922, 24.02119047, ..., 88.14854137,
            94.9533221 , 86.43696871],
           [25.71344931, 45.77984903, 25.02930797, ..., 88.09491531,
            94.78756009, 86.40922445],
           [26.74278032, 46.8295911 , 26.05525129, ..., 87.55632968,
            94.14934866, 85.89451817],
           ...,
           [11.19169406, 31.45145226, 10.46634004, ..., 90.80270294,
            98.97645174, 88.77523134],
           [11.19169406, 31.45145226, 10.46634004, ..., 90.80270294,
            98.97645174, 88.77523134],
           [11.19169406, 31.45145226, 10.46634004, ..., 90.80270294,
            98.97645174, 88.77523134]])




```python
# 기본적으로 argsort()에 아무것도 넣지 않으면 axis가 0으로 설정된다.
# 즉 열별로 가장 가까운 것을 가져오게 된다.
# 각 아파트별로 가장 가까운 지하철역이 나오게 될 것이다.
close_subway_index = dist_mat.argsort()[:, 0]
close_subway_index
```




    array([488,  74, 683, ..., 118, 118, 118], dtype=int64)




```python
df['가까운역'] = df3.iloc[close_subway_index]['역명'].values 
# 새로운 시리즈를 만들 때는 list, ndarray를 사용하는 것이 바람직하다!
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
      <th>거래일자</th>
      <th>법정동</th>
      <th>도로명</th>
      <th>아파트</th>
      <th>층</th>
      <th>전용면적</th>
      <th>건축년도</th>
      <th>거래금액</th>
      <th>시군구명</th>
      <th>읍면동명</th>
      <th>건물명</th>
      <th>경도</th>
      <th>위도</th>
      <th>가까운역</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2019-10-23</td>
      <td>면목동</td>
      <td>겸재로</td>
      <td>동서그랜드맨션</td>
      <td>4</td>
      <td>131.34</td>
      <td>1993</td>
      <td>43000</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>동서그랜드맨션</td>
      <td>127.075856</td>
      <td>37.588817</td>
      <td>중랑역[경춘선]</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2019-10-25</td>
      <td>면목동</td>
      <td>면목로</td>
      <td>킴스아파트</td>
      <td>4</td>
      <td>38.91</td>
      <td>2018</td>
      <td>28900</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>킴스아파트</td>
      <td>127.084734</td>
      <td>37.592972</td>
      <td>상봉역[7호선]</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2019-10-29</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>12</td>
      <td>78.56</td>
      <td>2005</td>
      <td>45400</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
      <td>망우역[경의중앙선]</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2019-12-04</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>4</td>
      <td>81.96</td>
      <td>2005</td>
      <td>45500</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
      <td>망우역[경의중앙선]</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2019-12-26</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>3</td>
      <td>82.28</td>
      <td>2005</td>
      <td>45500</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
      <td>망우역[경의중앙선]</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['가까운역까지_거리'] = dist_mat[close_subway_index][:, 0]
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
      <th>거래일자</th>
      <th>법정동</th>
      <th>도로명</th>
      <th>아파트</th>
      <th>층</th>
      <th>전용면적</th>
      <th>건축년도</th>
      <th>거래금액</th>
      <th>시군구명</th>
      <th>읍면동명</th>
      <th>건물명</th>
      <th>경도</th>
      <th>위도</th>
      <th>가까운역</th>
      <th>가까운역까지_거리</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2019-10-23</td>
      <td>면목동</td>
      <td>겸재로</td>
      <td>동서그랜드맨션</td>
      <td>4</td>
      <td>131.34</td>
      <td>1993</td>
      <td>43000</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>동서그랜드맨션</td>
      <td>127.075856</td>
      <td>37.588817</td>
      <td>중랑역[경춘선]</td>
      <td>22.308503</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2019-10-25</td>
      <td>면목동</td>
      <td>면목로</td>
      <td>킴스아파트</td>
      <td>4</td>
      <td>38.91</td>
      <td>2018</td>
      <td>28900</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>킴스아파트</td>
      <td>127.084734</td>
      <td>37.592972</td>
      <td>상봉역[7호선]</td>
      <td>25.366110</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2019-10-29</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>12</td>
      <td>78.56</td>
      <td>2005</td>
      <td>45400</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
      <td>망우역[경의중앙선]</td>
      <td>25.379852</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2019-12-04</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>4</td>
      <td>81.96</td>
      <td>2005</td>
      <td>45500</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
      <td>망우역[경의중앙선]</td>
      <td>25.379852</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2019-12-26</td>
      <td>면목동</td>
      <td>용마산로86길</td>
      <td>면목동2차미소지움아파트</td>
      <td>3</td>
      <td>82.28</td>
      <td>2005</td>
      <td>45500</td>
      <td>중랑구</td>
      <td>면목동</td>
      <td>면목동2차미소지움아파트</td>
      <td>127.094170</td>
      <td>37.589641</td>
      <td>망우역[경의중앙선]</td>
      <td>25.379852</td>
    </tr>
  </tbody>
</table>
</div>


