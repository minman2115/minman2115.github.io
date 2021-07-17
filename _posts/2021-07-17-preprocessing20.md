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



- 참조데이터가 필요한 경우에 병합하기

도로명 주소 / 지번 주소, 회원명 / 회원아이디 등과 같이 일정한 패턴이 없이 포맷이 다른 경우에는 컬럼값을 참조 데이터를 이용해서 변경해줘야 함

예를 들어서 도로명주소가 있을때 지번주소로 바꾸기 위한 어떤 규칙이 있는것이 아니고 거기에 맞는 참조데이터를 바탕으로 변환을 해줘야 함. 마찬가지로 회원명을 회원아이디로 바꿀때도 어떤 규칙이 있는것이 아니기 때문에 이런 경우에도 참조데이터를 활용을 해서 변환을 해줘야 한다.

아래 그림을 보면 주황색 컬럼에 a,b,c가 빨간색 컬럼의 1,2,3으로 각각 맵핑되어야 한다고 하자.

![3](https://user-images.githubusercontent.com/41605276/126026080-cd5a6036-f174-41dc-b580-ceb34793159e.PNG)

이럴때 아래 그림과 같은 참조데이터를 불러와서 딕셔너리 형태로 바꿔준다. 그런다음에 이 딕셔너리를 이용해서 키에다가 replace 함수로 변환을 시키면 된다.

![4](https://user-images.githubusercontent.com/41605276/126026239-359d9c31-f0c1-49ad-91ec-7af9e6c253dd.PNG)

- 위에서 활용하는 함수 

1) Series.to_dict()

series의 index를 key로, Data를 value로하는 딕셔너리로 변환

![5](https://user-images.githubusercontent.com/41605276/126026347-ed704d58-ee1d-459a-8b42-1ebcf79416e7.PNG)

replace 등 딕셔너리를 입력받는 함수를 사용할때 주로 사용

2) Series.replace

딕셔너리를 입력받아 series내에 있는 요소 가운데 key와 같은 값을 value로 변환해주는 함수

![6](https://user-images.githubusercontent.com/41605276/126026404-60040a01-b707-4b97-8cad-96821970a325.PNG)

만약에 아래 그림과 같이 데이터프레임에 e라는 인덱스가 있다고 하자. 그러면 딕셔너리에는 e라는 데이터가 없을것이다. 이때 replace함수를 사용해서 변환을 하면 우측 데이터 프레임에 e라는 데이터가 그대로 붙어서 갈것이다.

![7](https://user-images.githubusercontent.com/41605276/126026454-228d3047-1b47-4f89-9962-d50fa935623b.png)

- 실습


데이터 load


```python
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
df1 = pd.read_excel("인구수데이터.xlsx", sheet_name = "202006")
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
      <th>행정구역</th>
      <th>총인구수</th>
      <th>남자인구수</th>
      <th>여자인구수</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>여주시</td>
      <td>111341</td>
      <td>56582</td>
      <td>54759</td>
    </tr>
    <tr>
      <td>1</td>
      <td>당진시</td>
      <td>166084</td>
      <td>88015</td>
      <td>78069</td>
    </tr>
    <tr>
      <td>2</td>
      <td>아산시</td>
      <td>313054</td>
      <td>162398</td>
      <td>150656</td>
    </tr>
  </tbody>
</table>
</div>




```python
df2 = pd.read_excel("인구수데이터.xlsx", sheet_name = "201108")
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
      <th>행정구역</th>
      <th>총인구수</th>
      <th>남자인구수</th>
      <th>여자인구수</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>여주군</td>
      <td>109019</td>
      <td>55122</td>
      <td>53897</td>
    </tr>
    <tr>
      <td>1</td>
      <td>아산시</td>
      <td>271356</td>
      <td>138150</td>
      <td>133206</td>
    </tr>
    <tr>
      <td>2</td>
      <td>당진군</td>
      <td>148175</td>
      <td>76555</td>
      <td>71620</td>
    </tr>
  </tbody>
</table>
</div>



참조데이터 확인


```python
ref_df = pd.read_csv("시승격정보.csv", encoding = "cp949")
ref_df.head()
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
      <th>승격전</th>
      <th>승격후</th>
      <th>승격날짜</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>여주군</td>
      <td>여주시</td>
      <td>2013년 09월 23일</td>
    </tr>
    <tr>
      <td>1</td>
      <td>당진군</td>
      <td>당진시</td>
      <td>2012년 01월 01일</td>
    </tr>
  </tbody>
</table>
</div>



참조데이터를 이용해서 딕셔너리 생성


```python
ref_dict = ref_df.set_index('승격전')['승격후'].to_dict() # 인덱스 설정 및 시리즈로 변환
ref_dict
```




    {'여주군': '여주시', '당진군': '당진시'}



딕셔너리를 이용해서 키변수값 변환


```python
df2['행정구역'] = df2['행정구역'].replace(ref_dict)
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
      <th>행정구역</th>
      <th>총인구수</th>
      <th>남자인구수</th>
      <th>여자인구수</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>여주시</td>
      <td>109019</td>
      <td>55122</td>
      <td>53897</td>
    </tr>
    <tr>
      <td>1</td>
      <td>아산시</td>
      <td>271356</td>
      <td>138150</td>
      <td>133206</td>
    </tr>
    <tr>
      <td>2</td>
      <td>당진시</td>
      <td>148175</td>
      <td>76555</td>
      <td>71620</td>
    </tr>
  </tbody>
</table>
</div>



df1과 df2에 있는 변수명 레코드가 모두 같으므로, 변수명을 다르게 수정해야 함.

그래서 변수명앞에 년도 prefix를 붙이면 된다. add_prefix라는 함수를 이용해서 prefix를 붙이게 되면 모든 컬럼에 prefix가 붙는다. 우리는 행정구역이라는 변수는 그대로 두기를 원하기 때문에 행정구역 변수명은 인덱스로 설정한다.


```python
df1.set_index("행정구역", inplace = True)
df2.set_index("행정구역", inplace = True)

df1 = df1.add_prefix("202006_")
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
      <th>202006_총인구수</th>
      <th>202006_남자인구수</th>
      <th>202006_여자인구수</th>
    </tr>
    <tr>
      <th>행정구역</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>여주시</td>
      <td>111341</td>
      <td>56582</td>
      <td>54759</td>
    </tr>
    <tr>
      <td>당진시</td>
      <td>166084</td>
      <td>88015</td>
      <td>78069</td>
    </tr>
    <tr>
      <td>아산시</td>
      <td>313054</td>
      <td>162398</td>
      <td>150656</td>
    </tr>
  </tbody>
</table>
</div>




```python
df2 = df2.add_prefix("201108_")
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
      <th>201108_총인구수</th>
      <th>201108_남자인구수</th>
      <th>201108_여자인구수</th>
    </tr>
    <tr>
      <th>행정구역</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>여주시</td>
      <td>109019</td>
      <td>55122</td>
      <td>53897</td>
    </tr>
    <tr>
      <td>아산시</td>
      <td>271356</td>
      <td>138150</td>
      <td>133206</td>
    </tr>
    <tr>
      <td>당진시</td>
      <td>148175</td>
      <td>76555</td>
      <td>71620</td>
    </tr>
  </tbody>
</table>
</div>



left_index = True, right_index = True 로 잡아서 df1과 df2가 인덱스를 기준으로 하도록 옵션을 설정해서 merge한다.


```python
merged_df = pd.merge(df1, df2, left_index = True, right_index = True)
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
      <th>202006_총인구수</th>
      <th>202006_남자인구수</th>
      <th>202006_여자인구수</th>
      <th>201108_총인구수</th>
      <th>201108_남자인구수</th>
      <th>201108_여자인구수</th>
    </tr>
    <tr>
      <th>행정구역</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>여주시</td>
      <td>111341</td>
      <td>56582</td>
      <td>54759</td>
      <td>109019</td>
      <td>55122</td>
      <td>53897</td>
    </tr>
    <tr>
      <td>당진시</td>
      <td>166084</td>
      <td>88015</td>
      <td>78069</td>
      <td>148175</td>
      <td>76555</td>
      <td>71620</td>
    </tr>
    <tr>
      <td>아산시</td>
      <td>313054</td>
      <td>162398</td>
      <td>150656</td>
      <td>271356</td>
      <td>138150</td>
      <td>133206</td>
    </tr>
  </tbody>
</table>
</div>




```python
converted_df=merged_df.reset_index()
converted_df.head()
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
      <th>행정구역</th>
      <th>202006_총인구수</th>
      <th>202006_남자인구수</th>
      <th>202006_여자인구수</th>
      <th>201108_총인구수</th>
      <th>201108_남자인구수</th>
      <th>201108_여자인구수</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>여주시</td>
      <td>111341</td>
      <td>56582</td>
      <td>54759</td>
      <td>109019</td>
      <td>55122</td>
      <td>53897</td>
    </tr>
    <tr>
      <td>1</td>
      <td>당진시</td>
      <td>166084</td>
      <td>88015</td>
      <td>78069</td>
      <td>148175</td>
      <td>76555</td>
      <td>71620</td>
    </tr>
    <tr>
      <td>2</td>
      <td>아산시</td>
      <td>313054</td>
      <td>162398</td>
      <td>150656</td>
      <td>271356</td>
      <td>138150</td>
      <td>133206</td>
    </tr>
  </tbody>
</table>
</div>


