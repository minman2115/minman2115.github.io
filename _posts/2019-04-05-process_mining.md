---
layout: post
title: "네덜란드 임대주택기관의 집수리 요구 처리 프로세스 데이터 분석결과"
tags: [개인프로젝트, 프로세스 마이닝]
comments: true
---

.

#  # 네덜란드 임대주택기관의 집수리 요구 처리 프로세스 데이터 분석결과

## [  개 요 ]
   
#### ㅇ 컨텐츠 : 네덜란드 임대 주택 기관의 집 수리 요구 처리 프로세스 데이터
        
#### ㅇ 목적 : 주어진 데이터를 분석하여 집수리 프로세스 및 유의미한 정보를 파악하고, 개선사항 등을 도출

#### ㅇ Dataset 구성 :

    1) CSV 파일구성 : RepairExample_English.csv

    2) column : 'caseID','timestamp', 'contact' 등 12개
    
    3) row : 13262개

## [ 세부과제 ]

### 1. 주어진 데이터에 대한 분석 및 수리 프로세스 판단결과

- 주어진 데이터를 자유롭게 분석해 보시고, 전체 수리(Repair) 프로세스를 어떻게 파악 했는지 간단히 묘사하거나 그림을 그려주세요.

### 2. 주어진 데이터로부터 유의미한 현상 분석결과

- 데이터를 보고 알 수 있는 현재 현상(AS-IS)에 대해  의미 있다고 생각한 지표와 이를 시각화한 그래프 등을 나열해 주세요.

### 3. 현재 수리 프로세스에 대한 개선방안

- 현재 주어진 수리 프로세스를 어떻게 개선할 수 있을 지 의견을 적어주세요

### 4. 데이터 규모가 커질 경우 처리방안

- 데이터 규모가 커질 경우 어떻게 처리할지 처리 방안을 알려주세요.

### [ 분석을 위한 데이터 로드 ]


```python
# 필요한 라이브러리 사용을 위한 임포트
%matplotlib inline
%config InlineBackend.figure_formats = {'png', 'retina'}
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import pandas as pd
import time

# 데이터 로드
df = pd.read_csv('RepairExample_English.csv')
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
      <th>caseID</th>
      <th>taskID</th>
      <th>originator</th>
      <th>eventtype</th>
      <th>timestamp</th>
      <th>contact</th>
      <th>RepairType</th>
      <th>objectKey</th>
      <th>RepairInternally</th>
      <th>EstimatedRepairTime</th>
      <th>RepairCode</th>
      <th>RepairOK</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>FirstContact</td>
      <td>Dian</td>
      <td>complete</td>
      <td>1970-01-02 8:08</td>
      <td>Phone</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>MakeTicket</td>
      <td>Dian</td>
      <td>start</td>
      <td>1970-01-02 8:08</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>MakeTicket</td>
      <td>Dian</td>
      <td>complete</td>
      <td>1970-01-02 8:11</td>
      <td>NaN</td>
      <td>E</td>
      <td>1340.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>ArrangeSurvey</td>
      <td>Dian</td>
      <td>start</td>
      <td>1970-01-02 8:11</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>ArrangeSurvey</td>
      <td>Dian</td>
      <td>complete</td>
      <td>1970-01-02 8:16</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## [  세부과제별 수행결과   ]

## 1. 주어진 데이터에 대한 분석 및 수리 프로세스 판단결과

### v 결론

#### 1)  수리 프로세스는 크게 'case 접수' -> '수리' -> 'case 완료처리' 세가지 국면으로 처리되는 것으로 판단하였으며, 총 20개의 패턴을 확인

  * 'case 접수' 국면 : FirstContact -> Maketicket -> ArrangeSurvey -> InformClientSurvey
    
    
  * '수리' 국면 : Survey -> (ImmediateRepair, InternRepair, ExternalRepair가 여러형태로 혼재) -> RepairReady
    
    
  * 'case 완료처리' 국면 : (SendticketToFinAdmin -> ReadyInformClient) 또는 (ReadyInformClient -> SendticketToFinAdmin) -> TicketReady


  * 일부 프로세스(73건)는 예외적으로 'FirstContact' -> 'InformClientWrongPlace' 두 단계로 종결
  

#### 2) 'RepairType' 및 'objectKey' 정보는 수리 프로세스 상의 'MakeTicket' 과정에서 입력되는 것을 확인

#### 3)  'RepairInternally', 'EstimatedRepairTime', 'RepairCode' 정보는 수리 프로세스 상의 'survey'가 완료되는 시점에서 입력되는 것을 확인

  - RepairInternally가 False인 caseID는 Repair 국면 시 ExternRepair만 실시

### v 판단과정

#### step 1) 주어진 데이터에서 집수리 진행 건수는 아래와 같이 총 1000건으로 파악


```python
len(set(df['caseID']))
```




    1000



#### step 2) 1000건의 집수리 진행건별 직관적인 프로세스 파악을 위한 새로운 데이터 프레임 생성

   - caseID와 taskID를 기준으로 정렬하여 데이터 프레임을 재생성
   
   
   - 참고사항 : 코드실행 시간 약 2분소요


```python
# 새로운 데이터 프레임 new_df은 원활한 프로세스 파악을 위하여 전체 컬럼중 일부만 축약
new_df = df[["caseID", "taskID", "originator", "eventtype", "timestamp"]]

# caseID를 입력하면 해당 caseID의 new_df를 출력해주는 함수정의
def check_caseID(caseID):
    return new_df[new_df['caseID'] == caseID]

# new_df에 "complete_time" 컬럼을 생성하기 위한 apply 함수
# eventtype 칼럼의 'complete'가 입력되면 하단의 for문에서 정의된 timestamp_complete_temp 리스트의 
# 가장 상단의 데이터를 pop해서 리턴, 'start'가 입력되면 가장 상단의 데이터를 리턴
def get_timestamp(data):
    if data == "complete":
        return timestamp_complete_temp.pop(0)
    else :
        return timestamp_complete_temp[0]

# new_df에 "repetition_check"컬럼을 생성하기 위한 apply 함수
# for문에 정의된 repetition_checking_temp_list에 데이터 없으면 false를 리턴하고,
# repetition_checking_temp_list에 데이터가 존재하면 기존에 존재하는 데이터를 pop하고 True를 리턴
def repetition_checking(data):
    if data not in repetition_checking_temp_list:
        repetition_checking_temp_list.append(data)
        return False
    else:
        repetition_checking_temp_list.pop()
        return True
    
# 최종 결과 데이터프레임의 틀을 정의
df_new = pd.DataFrame(columns=("caseID", "taskID", "originator", "timestamp", "complete_time")) 

# case 1번 ~ 1000번까지 반복수행
for caseID in range(1,1000+1):
    
    # caseID별 데이터프레임 로드
    temp_df = check_caseID(caseID)
    
    # eventtype가 complete인 데이터만 축약한 임시데이터 프레임 생성
    temp_df_complete = temp_df[temp_df["eventtype"] == "complete"]
    
    # temp_df_complete의 timestamp 데이터를 리스트형태로 저장
    timestamp_complete_temp = list(temp_df_complete['timestamp'])

    # 상단에 정의한 get_timestamp를 이용해서 complete_time 컬럼생성
    temp_df["complete_time"] = temp_df['eventtype'].apply(get_timestamp)
    
    # 상단에 정의한 repetition_checking을 이용해서 repetition_check 컬럼생성
    repetition_checking_temp_list = []
    temp_df["repetition_check"] = temp_df['taskID'].apply(repetition_checking)

    # repetition_check이 True인 데이터 삭제
    temp_df = temp_df[temp_df['repetition_check'] == False]
    
    # eventtype 및 repetition_check 칼럼 삭제
    del temp_df['eventtype']
    del temp_df['repetition_check']

    # 최종 정제된 데이터프레임을 최정결과 데이터프레임에 추가
    df_new = pd.concat([df_new,temp_df])
    df_new = df_new.reset_index(drop=True)

# timestamp 컬럼 이름을 start_time으로 변경
df_new.rename(columns={'timestamp':'start_time'}, inplace=True)

df_new.tail()
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
      <th>caseID</th>
      <th>taskID</th>
      <th>originator</th>
      <th>start_time</th>
      <th>complete_time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9546</th>
      <td>1000</td>
      <td>InternRepair</td>
      <td>John</td>
      <td>1970-01-19 14:34</td>
      <td>1970-01-19 18:34</td>
    </tr>
    <tr>
      <th>9547</th>
      <td>1000</td>
      <td>RepairReady</td>
      <td>System</td>
      <td>1970-01-19 18:34</td>
      <td>1970-01-19 18:34</td>
    </tr>
    <tr>
      <th>9548</th>
      <td>1000</td>
      <td>SendTicketToFinAdmin</td>
      <td>System</td>
      <td>1970-01-20 1:11</td>
      <td>1970-01-20 1:11</td>
    </tr>
    <tr>
      <th>9549</th>
      <td>1000</td>
      <td>ReadyInformClient</td>
      <td>System</td>
      <td>1970-01-20 2:25</td>
      <td>1970-01-20 2:25</td>
    </tr>
    <tr>
      <th>9550</th>
      <td>1000</td>
      <td>TicketReady</td>
      <td>System</td>
      <td>1970-01-20 2:25</td>
      <td>1970-01-20 2:25</td>
    </tr>
  </tbody>
</table>
</div>



#### step 3) caseID별로 taskID 데이터를 전부 추출하여, 고유한 패턴만 저장


```python
taskID_list = []

for caseID in range(1,1000+1):
    temp_df = df_new[df_new['caseID'] == caseID]
    taskID_list.append(list(temp_df['taskID']))
    
task_pattern_list = []

for data in taskID_list:
    if data not in task_pattern_list:
        task_pattern_list.append(data)
        
print("패턴 갯수 : " , len(task_pattern_list))
task_pattern_list
```

    패턴 갯수 :  20
    




    [['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'ExternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact', 'InformClientWrongPlace'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'InternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'ExternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ExternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'ExternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ExternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'InternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'InternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'InternRepair',
      'ExternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'ExternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'InternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'InternRepair',
      'ExternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'ImmediateRepair',
      'InternRepair',
      'InternRepair',
      'RepairReady',
      'ReadyInformClient',
      'SendTicketToFinAdmin',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'InternRepair',
      'ExternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady'],
     ['FirstContact',
      'MakeTicket',
      'ArrangeSurvey',
      'InformClientSurvey',
      'Survey',
      'InternRepair',
      'InternRepair',
      'InternRepair',
      'RepairReady',
      'SendTicketToFinAdmin',
      'ReadyInformClient',
      'TicketReady']]



#### step 4) networkx 패키지를 이용해서 저장한 20개의 패턴을 그래프로 시각화


```python
import networkx
import networkx as nx
from IPython.core.display import Image
from networkx.drawing.nx_pydot import to_pydot
import matplotlib.pyplot as plt
import pydot

temp = []
attrs = {'FirstContact': 1000 , 'SendTicketToFinAdmin': 927, 'MakeTicket': 927, 'TicketReady' : 927,\
        'ArrangeSurvey': 927,'Survey': 927,'InformClientSurvey': 927,'RepairReady': 927, 'ReadyInformClient' : 927,\
        'InternRepair':547, 'ImmediateRepair' : 383, 'ExternRepair' : 132, "InformClientWrongPlace" : 73}

for _list in task_pattern_list:
    
    graph = nx.DiGraph()
    temp_list = []
    
    for data in _list:
        
        if data == 'FirstContact':
            graph.add_node("{}".format(data),color='red')
        
        elif data == 'MakeTicket':
            graph.add_node("{}".format(data),color='green')
            
        elif data == 'ArrangeSurvey':
            graph.add_node("{}".format(data),color='yellow')
         
        elif data == 'InformClientSurvey':
            graph.add_node("{}".format(data),color='blue')
            
        elif data == 'Survey':
            graph.add_node("{}".format(data),color='purple')
            
        elif data == 'InternRepair':
            graph.add_node("{}".format(data),color='pink')
            
        elif data == 'RepairReady':
            graph.add_node("{}".format(data),color='orange')
            
        elif data == 'SendTicketToFinAdmin':
            graph.add_node("{}".format(data),color='brown')
            
        elif data == 'ReadyInformClient':
            graph.add_node("{}".format(data),color='gray')
            
        elif data == 'TicketReady':
            graph.add_node("{}".format(data),color='aqua')
            
        elif data == 'ExternRepair':
            graph.add_node("{}".format(data),color='black')
            
        elif data == 'ImmediateRepair':
            graph.add_node("{}".format(data),color='gold')
        
        else :
            graph.add_node("{}".format(data))

        start_edge = list(graph.nodes())[0]

        for data in list(graph.nodes())[1:]:
            weight = attrs['{}'.format(data)] - attrs['{}'.format(start_edge)]
            graph.add_edge(start_edge, data, weight = weight,label=str(weight))
            start_edge = data            

    drawing_graph = to_pydot(graph)
    drawing_graph.set_dpi(300)
    drawing_graph.set_rankdir("LR")
    drawing_graph.set_margin(1)
    temp.append(Image(drawing_graph.create_png(), width=1500))
```

** 아래 그래프에서 노드간 가중치는 caseID수 만큼의 1000개를 기준으로 시작해서 FirstContact부터 노드수 변화값을 의미


```python
# 패턴 1
temp.pop()
```




![프로세스분석_입사지원과제_박민수_16_0](https://user-images.githubusercontent.com/41605276/55571260-26ae6e80-5740-11e9-8411-933529aeaeb1.png)




```python
# 패턴 2
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_17_0](https://user-images.githubusercontent.com/41605276/55571285-375ee480-5740-11e9-92a6-979b6453d9b9.png)




```python
# 패턴 3
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_18_0](https://user-images.githubusercontent.com/41605276/55571301-3f1e8900-5740-11e9-9961-d7a14da24c86.png)




```python
# 패턴 4
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_19_0](https://user-images.githubusercontent.com/41605276/55571319-4776c400-5740-11e9-9563-948661ddb74e.png)




```python
# 패턴 5
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_20_0](https://user-images.githubusercontent.com/41605276/55571339-4e053b80-5740-11e9-9917-d8b1cebeaf06.png)




```python
# 패턴 6
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_21_0](https://user-images.githubusercontent.com/41605276/55571359-578ea380-5740-11e9-8fa6-6320ff905b9d.png)




```python
# 패턴 7
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_22_0](https://user-images.githubusercontent.com/41605276/55571388-637a6580-5740-11e9-9524-27fddcb98cab.png)




```python
# 패턴 8
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_23_0](https://user-images.githubusercontent.com/41605276/55571401-69704680-5740-11e9-936d-928db0a12d05.png)




```python
# 패턴 9
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_24_0](https://user-images.githubusercontent.com/41605276/55571416-6f662780-5740-11e9-9b23-b89ddd9677f1.png)




```python
# 패턴 10
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_25_0](https://user-images.githubusercontent.com/41605276/55571433-7725cc00-5740-11e9-82fa-a3fedff48567.png)




```python
# 패턴 11
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_26_0](https://user-images.githubusercontent.com/41605276/55571461-8278f780-5740-11e9-84c9-215b738e54a8.png)




```python
# 패턴 12
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_27_0](https://user-images.githubusercontent.com/41605276/55571476-886ed880-5740-11e9-9d5f-fa35dbf52ff6.png)




```python
# 패턴 13
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_28_0](https://user-images.githubusercontent.com/41605276/55571485-8e64b980-5740-11e9-836e-6d51db7f83e0.png)




```python
# 패턴 14
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_29_0](https://user-images.githubusercontent.com/41605276/55571499-97558b00-5740-11e9-95dc-df8ce4c941bf.png)




```python
# 패턴 15
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_30_0](https://user-images.githubusercontent.com/41605276/55571517-9e7c9900-5740-11e9-8b2c-5e96e4139d5f.png)



```python
# 패턴 16
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_31_0](https://user-images.githubusercontent.com/41605276/55571533-a63c3d80-5740-11e9-995d-396d8506443f.png)




```python
# 패턴 17
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_32_0](https://user-images.githubusercontent.com/41605276/55571557-adfbe200-5740-11e9-9595-428bfe2a6cd6.png)




```python
# 패턴 18
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_33_0](https://user-images.githubusercontent.com/41605276/55571566-b522f000-5740-11e9-8f69-f0efadb1d3eb.png)




```python
# 패턴 19
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_34_0](https://user-images.githubusercontent.com/41605276/55571584-ba803a80-5740-11e9-8bdb-957d2896664e.png)



```python
# 패턴 20
temp.pop() 
```




![프로세스분석_입사지원과제_박민수_35_0](https://user-images.githubusercontent.com/41605276/55571589-bfdd8500-5740-11e9-8b06-601ee2cf2976.png)



#### step 5) 상단의 17번 패턴 데이터 확인

   - 일부 프로세스(73건)는 예외적으로 'FirstContact' -> 'InformClientWrongPlace' 두 단계로 종결 확인


```python
temp_list = list(df_new[df_new['taskID'] == 'InformClientWrongPlace']['caseID'])

result_df = pd.DataFrame(columns=("caseID", "taskID", "originator", "start_time", "complete_time"))

for caseID in temp_list:
    temp_df = df_new[df_new['caseID'] == caseID]
    result_df = pd.concat([result_df,temp_df])
    result_df = result_df.reset_index(drop=True)

result_df = result_df[["caseID", "taskID", "originator", "start_time", "complete_time"]]
print("caseID 건수 : ", len(set(result_df['caseID'])))
result_df.tail()
```

    caseID 건수 :  73
    




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
      <th>caseID</th>
      <th>taskID</th>
      <th>originator</th>
      <th>start_time</th>
      <th>complete_time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>141</th>
      <td>918</td>
      <td>InformClientWrongPlace</td>
      <td>Monica</td>
      <td>1970-01-07 20:52</td>
      <td>1970-01-07 20:52</td>
    </tr>
    <tr>
      <th>142</th>
      <td>942</td>
      <td>FirstContact</td>
      <td>Monica</td>
      <td>1970-01-07 13:14</td>
      <td>1970-01-07 13:14</td>
    </tr>
    <tr>
      <th>143</th>
      <td>942</td>
      <td>InformClientWrongPlace</td>
      <td>Monica</td>
      <td>1970-01-07 13:14</td>
      <td>1970-01-07 13:14</td>
    </tr>
    <tr>
      <th>144</th>
      <td>966</td>
      <td>FirstContact</td>
      <td>Dian</td>
      <td>1970-01-06 2:35</td>
      <td>1970-01-06 2:35</td>
    </tr>
    <tr>
      <th>145</th>
      <td>966</td>
      <td>InformClientWrongPlace</td>
      <td>Dian</td>
      <td>1970-01-06 2:35</td>
      <td>1970-01-06 2:35</td>
    </tr>
  </tbody>
</table>
</div>



#### step 6) 'RepairType' 및 'objectKey' 정보는 수리 프로세스 상의 'MakeTicket' 과정에서 입력되는 것을 확인

- 'RepairType' 정보가 수리 프로세스 상의 'MakeTicket' 과정에서 입력되는 것인지 확인


```python
repair_list = list(df['RepairType'].value_counts().index)

temp_df = pd.DataFrame(columns=("caseID", "taskID", "originator", "timestamp", "EstimatedRepairTime"))

for data in repair_list:
    temp = df[df['RepairType'] == data]
    temp_df = pd.concat([temp_df,temp])
    
temp_df = temp_df.reset_index(drop=True)
temp_df = temp_df[["caseID", "taskID", "originator","timestamp","contact","RepairType", "objectKey",\
                   "RepairInternally","EstimatedRepairTime","RepairCode","RepairOK"]]
                  
temp_df['taskID'].value_counts()
```




    MakeTicket    927
    Name: taskID, dtype: int64



- 'objectKey' 정보가 수리 프로세스 상의 'MakeTicket' 과정에서 입력되는 것인지 확인


```python
ob_list = list(df['objectKey'].value_counts().index)

temp_df = pd.DataFrame(columns=("caseID", "taskID", "originator", "timestamp", "EstimatedRepairTime"))

for data in ob_list:
    temp = df[df['objectKey'] == data]
    temp_df = pd.concat([temp_df,temp])
    
temp_df = temp_df.reset_index(drop=True)
temp_df = temp_df[["caseID", "taskID", "originator","timestamp","contact","RepairType", "objectKey",\
                   "RepairInternally","EstimatedRepairTime","RepairCode","RepairOK"]]
                  
temp_df['taskID'].value_counts()
```




    MakeTicket    927
    Name: taskID, dtype: int64



#### step 7) 'RepairInternally', 'EstimatedRepairTime', 'RepairCode' 정보는 수리 프로세스상의 'survey'가 완료되는 시점에서 입력되는 것을 확인

- 'RepairCode' 데이터가 존재하는 로우만 추출하여 새로운 데이터 프레임 생성


- 'taskID' 칼럼에 'Survey', 'eventtype' 칼럼에 'complete' 데이터만 존재하는 것을 확인


```python
repair_list = list(df['RepairCode'].value_counts().index)

temp_df = pd.DataFrame(columns=("caseID", "taskID","eventtype", "originator", "timestamp", "EstimatedRepairTime"))

for data in repair_list:
    temp = df[df['RepairCode'] == data]
    temp_df = pd.concat([temp_df,temp])
    
temp_df = temp_df.reset_index(drop=True)
temp_df = temp_df[["caseID", "taskID", "eventtype", "originator","timestamp","contact","RepairType", "objectKey",\
                   "RepairInternally","EstimatedRepairTime","RepairCode","RepairOK"]]

temp_df.tail()
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
      <th>caseID</th>
      <th>taskID</th>
      <th>eventtype</th>
      <th>originator</th>
      <th>timestamp</th>
      <th>contact</th>
      <th>RepairType</th>
      <th>objectKey</th>
      <th>RepairInternally</th>
      <th>EstimatedRepairTime</th>
      <th>RepairCode</th>
      <th>RepairOK</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>922</th>
      <td>946</td>
      <td>Survey</td>
      <td>complete</td>
      <td>Anne</td>
      <td>1970-01-07 22:06</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>False</td>
      <td>360.0</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>923</th>
      <td>952</td>
      <td>Survey</td>
      <td>complete</td>
      <td>Lex</td>
      <td>1970-01-02 11:37</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>False</td>
      <td>480.0</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>924</th>
      <td>972</td>
      <td>Survey</td>
      <td>complete</td>
      <td>Anne</td>
      <td>1970-01-11 19:53</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>False</td>
      <td>360.0</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>925</th>
      <td>992</td>
      <td>Survey</td>
      <td>complete</td>
      <td>Lex</td>
      <td>1970-01-03 7:51</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>False</td>
      <td>480.0</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>926</th>
      <td>999</td>
      <td>Survey</td>
      <td>complete</td>
      <td>Ben</td>
      <td>1970-01-07 5:11</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>False</td>
      <td>480.0</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



- 로우데이터에도 survey와 complete 데이터만 추출한 데이터 숫자도 927개이다.


```python
df[df['taskID'] == 'Survey'].tail()
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
      <th>caseID</th>
      <th>taskID</th>
      <th>originator</th>
      <th>eventtype</th>
      <th>timestamp</th>
      <th>contact</th>
      <th>RepairType</th>
      <th>objectKey</th>
      <th>RepairInternally</th>
      <th>EstimatedRepairTime</th>
      <th>RepairCode</th>
      <th>RepairOK</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>13228</th>
      <td>997</td>
      <td>Survey</td>
      <td>John</td>
      <td>complete</td>
      <td>1970-01-12 11:23</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>240.0</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>13241</th>
      <td>998</td>
      <td>Survey</td>
      <td>John</td>
      <td>start</td>
      <td>1970-01-15 20:07</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>13242</th>
      <td>998</td>
      <td>Survey</td>
      <td>John</td>
      <td>complete</td>
      <td>1970-01-15 20:35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>220.0</td>
      <td>2.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>13255</th>
      <td>999</td>
      <td>Survey</td>
      <td>Ben</td>
      <td>start</td>
      <td>1970-01-07 4:50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>13256</th>
      <td>999</td>
      <td>Survey</td>
      <td>Ben</td>
      <td>complete</td>
      <td>1970-01-07 5:11</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>False</td>
      <td>480.0</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
len(df[df['taskID'] == 'Survey']) / 2
```




    927.0




```python
temp_df['taskID'].value_counts()
```




    Survey    927
    Name: taskID, dtype: int64




```python
df['EstimatedRepairTime'].value_counts().sum()
```




    927




```python
df['RepairCode'].value_counts().sum()
```




    927



#### step 8) RepairInternally가 False인 caseID는 ExternRepair 만 실시하는지 여부 확인

- EstimatedRepairTime 데이터가 존재하는 로우만 추출하여 새로운 데이터 프레임 'temp_df'생성


```python
temp_list = list(df['EstimatedRepairTime'].value_counts().index)

temp_df = pd.DataFrame(columns=("caseID", "taskID", "originator", "timestamp", "EstimatedRepairTime"))

for data in temp_list:
    temp = df[df['EstimatedRepairTime'] == data]
    temp_df = pd.concat([temp_df,temp])
    
temp_df = temp_df.reset_index(drop=True)
temp_df = temp_df[["caseID", "taskID", "originator","timestamp","contact","RepairType", "objectKey",\
                   "RepairInternally","EstimatedRepairTime","RepairCode","RepairOK"]]
temp_df.tail()
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
      <th>caseID</th>
      <th>taskID</th>
      <th>originator</th>
      <th>timestamp</th>
      <th>contact</th>
      <th>RepairType</th>
      <th>objectKey</th>
      <th>RepairInternally</th>
      <th>EstimatedRepairTime</th>
      <th>RepairCode</th>
      <th>RepairOK</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>922</th>
      <td>738</td>
      <td>Survey</td>
      <td>Ben</td>
      <td>1970-01-08 1:47</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>923</th>
      <td>786</td>
      <td>Survey</td>
      <td>Nick</td>
      <td>1970-01-03 16:57</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>924</th>
      <td>812</td>
      <td>Survey</td>
      <td>Ben</td>
      <td>1970-01-04 7:01</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>925</th>
      <td>844</td>
      <td>Survey</td>
      <td>Eric</td>
      <td>1970-01-05 18:31</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>926</th>
      <td>90</td>
      <td>Survey</td>
      <td>Ben</td>
      <td>1970-01-05 11:02</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>True</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



- 'temp_df' 데이터 프레임을 베이스로 'RepairInternally' 가 False인 경우 'InternRepair' 나 'ImmediateRepair'가 존재하는지 확인


```python
temp_df = temp_df[temp_df['RepairInternally']==False]

temp_list = list(temp_df['caseID'].value_counts().index)

temp_df = pd.DataFrame(columns=("caseID", "taskID", "originator", "timestamp", "EstimatedRepairTime"))

for data in temp_list:
    temp = df[df['caseID'] == data]
    temp_df = pd.concat([temp_df,temp])
    
temp_df = temp_df.reset_index(drop=True)
temp_df = temp_df[["caseID", "taskID", "originator","timestamp","contact","RepairType", "objectKey",\
                   "RepairInternally","EstimatedRepairTime","RepairCode","RepairOK"]]

## 결과가 False가 출력되어 RepairInternally가 False인 caseID는 ExternRepair 만 실시한다고 할 수 있다.
('InternRepair' or 'ImmediateRepair') in temp_df['taskID']
```




    False



## 2. 주어진 데이터로부터 유의미한 현상 분석결과

### v 결론

#### 1) taskID 별 평균 처리소요시간은 Repair종류 별로 차이가 있었으며 ImmediateRepair에서 ExternRepair로 갈수록 처리소요시간이 증가함

   - ImmediateRepair = 1 시간 56 분, InternRepair = 3 시간 44 분, ExternRepair = 4 시간 58 분
   
   
   - RepairInternally가 True일때 평균 EstimatedRepairTime :  180.18, RepairInternally가 False일때 평균 EstimatedRepairTime :  402.71
   
#### 2) task별 처리시간(ex) 오전인지 오후인지)에 따른 유의미한 결과가 있을 것이라는 가설을 세우고 접근했으나 큰 의미가 없는 것으로 판단

#### 3) 상단의 그래프 그림에서 FirstContact -> MakeTicket 이 -73인 이유는 FirstContact -> InformClientWrongPlace의 73개를 의미

### v 판단과정

#### step 1) 시간데이터 전처리

- 결론 1), 2)번 확인을 위한 'start_hour', 'complete_hour', 'run_time' 컬럼 생성


- 시간데이터에 대한 datetime처리 parsing


```python
df_new['start_hour'] = df_new['start_time'].apply(lambda data : int(data[11:-3]))
df_new['complete_hour'] = df_new['complete_time'].apply(lambda data : int(data[11:-3]))

df_new['start_time'] = pd.to_datetime(df_new['start_time'])
df_new['complete_time'] = pd.to_datetime(df_new['complete_time'])
df_new['run_time'] = df_new['complete_time'] - df_new['start_time']

df_new.tail()
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
      <th>caseID</th>
      <th>taskID</th>
      <th>originator</th>
      <th>start_time</th>
      <th>complete_time</th>
      <th>start_hour</th>
      <th>complete_hour</th>
      <th>run_time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9546</th>
      <td>1000</td>
      <td>InternRepair</td>
      <td>John</td>
      <td>1970-01-19 14:34:00</td>
      <td>1970-01-19 18:34:00</td>
      <td>14</td>
      <td>18</td>
      <td>04:00:00</td>
    </tr>
    <tr>
      <th>9547</th>
      <td>1000</td>
      <td>RepairReady</td>
      <td>System</td>
      <td>1970-01-19 18:34:00</td>
      <td>1970-01-19 18:34:00</td>
      <td>18</td>
      <td>18</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>9548</th>
      <td>1000</td>
      <td>SendTicketToFinAdmin</td>
      <td>System</td>
      <td>1970-01-20 01:11:00</td>
      <td>1970-01-20 01:11:00</td>
      <td>1</td>
      <td>1</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>9549</th>
      <td>1000</td>
      <td>ReadyInformClient</td>
      <td>System</td>
      <td>1970-01-20 02:25:00</td>
      <td>1970-01-20 02:25:00</td>
      <td>2</td>
      <td>2</td>
      <td>00:00:00</td>
    </tr>
    <tr>
      <th>9550</th>
      <td>1000</td>
      <td>TicketReady</td>
      <td>System</td>
      <td>1970-01-20 02:25:00</td>
      <td>1970-01-20 02:25:00</td>
      <td>2</td>
      <td>2</td>
      <td>00:00:00</td>
    </tr>
  </tbody>
</table>
</div>



#### step 2) start_hour에 따른 유의미한 현상 여부확인

결론 : 특별하게 유의미한 결과 없음


```python
from collections import Counter

taskID_list = list(df_new['taskID'].value_counts().index)

for taskID in taskID_list:
    
    temp = df_new[df_new['taskID'] == '{}'.format(taskID)]
    temp['Morning_and_afternoon'] = df_new['start_hour'].apply(lambda data : "AM" if data < 12 else "PM" )
    temp_list =  list(temp['Morning_and_afternoon'])

    result = Counter(temp_list)
    print('{} 오전/오후별 카운트'.format(taskID),result)
    
    print('-----------------------------------------------------------')
    
    temp_list =  list(temp['start_hour'])
    
    result = Counter(temp_list)
    result = dict(sorted(dict(result).items()))
    
    print('{} 시간별 카운트'.format(taskID),result)
    
    plt.bar(range(len(result)), list(result.values()), align='center')
    plt.xticks(range(len(result)), list(result.keys()))
    plt.xlabel('hour')
    plt.ylabel('counts')
    plt.show()
    
    print('-----------------------------------------------------------')
```

    FirstContact 오전/오후별 카운트 Counter({'AM': 505, 'PM': 495})
    -----------------------------------------------------------
    FirstContact 시간별 카운트 {0: 41, 1: 49, 2: 34, 3: 38, 4: 58, 5: 51, 6: 48, 7: 38, 8: 30, 9: 39, 10: 38, 11: 41, 12: 39, 13: 50, 14: 42, 15: 42, 16: 35, 17: 36, 18: 45, 19: 48, 20: 37, 21: 36, 22: 46, 23: 39}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_1" src="https://user-images.githubusercontent.com/41605276/55571624-d683dc00-5740-11e9-84b5-a504014f573c.png">


    -----------------------------------------------------------
    MakeTicket 오전/오후별 카운트 Counter({'AM': 467, 'PM': 460})
    -----------------------------------------------------------
    MakeTicket 시간별 카운트 {0: 39, 1: 43, 2: 30, 3: 36, 4: 54, 5: 45, 6: 47, 7: 36, 8: 28, 9: 34, 10: 36, 11: 39, 12: 35, 13: 45, 14: 38, 15: 39, 16: 35, 17: 35, 18: 40, 19: 47, 20: 34, 21: 34, 22: 43, 23: 35}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_3" src="https://user-images.githubusercontent.com/41605276/55571638-dedc1700-5740-11e9-8908-7f1f11b3ce55.png">


    -----------------------------------------------------------
    ArrangeSurvey 오전/오후별 카운트 Counter({'AM': 464, 'PM': 463})
    -----------------------------------------------------------
    ArrangeSurvey 시간별 카운트 {0: 36, 1: 44, 2: 31, 3: 34, 4: 54, 5: 44, 6: 48, 7: 36, 8: 28, 9: 34, 10: 37, 11: 38, 12: 38, 13: 42, 14: 36, 15: 41, 16: 35, 17: 34, 18: 35, 19: 49, 20: 38, 21: 37, 22: 42, 23: 36}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_5" src="https://user-images.githubusercontent.com/41605276/55571649-e6032500-5740-11e9-825f-82542714d485.png">


    -----------------------------------------------------------
    SendTicketToFinAdmin 오전/오후별 카운트 Counter({'AM': 474, 'PM': 453})
    -----------------------------------------------------------
    SendTicketToFinAdmin 시간별 카운트 {0: 43, 1: 34, 2: 34, 3: 46, 4: 35, 5: 47, 6: 43, 7: 40, 8: 33, 9: 33, 10: 50, 11: 36, 12: 42, 13: 32, 14: 30, 15: 21, 16: 48, 17: 51, 18: 31, 19: 41, 20: 41, 21: 36, 22: 47, 23: 33}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_7" src="https://user-images.githubusercontent.com/41605276/55571665-eb606f80-5740-11e9-91d4-edb206697082.png">


    -----------------------------------------------------------
    RepairReady 오전/오후별 카운트 Counter({'PM': 473, 'AM': 454})
    -----------------------------------------------------------
    RepairReady 시간별 카운트 {0: 35, 1: 41, 2: 36, 3: 31, 4: 35, 5: 37, 6: 47, 7: 40, 8: 37, 9: 36, 10: 45, 11: 34, 12: 38, 13: 24, 14: 43, 15: 49, 16: 36, 17: 35, 18: 48, 19: 40, 20: 37, 21: 27, 22: 54, 23: 42}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_9" src="https://user-images.githubusercontent.com/41605276/55571693-f915f500-5740-11e9-98b7-542f198cf6e8.png">


    -----------------------------------------------------------
    ReadyInformClient 오전/오후별 카운트 Counter({'PM': 465, 'AM': 462})
    -----------------------------------------------------------
    ReadyInformClient 시간별 카운트 {0: 34, 1: 38, 2: 39, 3: 40, 4: 36, 5: 25, 6: 40, 7: 48, 8: 40, 9: 37, 10: 48, 11: 37, 12: 37, 13: 43, 14: 36, 15: 29, 16: 39, 17: 38, 18: 30, 19: 42, 20: 48, 21: 33, 22: 50, 23: 40}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_11" src="https://user-images.githubusercontent.com/41605276/55571706-00d59980-5741-11e9-87bf-5719827004ce.png">


    -----------------------------------------------------------
    TicketReady 오전/오후별 카운트 Counter({'AM': 467, 'PM': 460})
    -----------------------------------------------------------
    TicketReady 시간별 카운트 {0: 46, 1: 38, 2: 33, 3: 38, 4: 43, 5: 35, 6: 47, 7: 41, 8: 30, 9: 40, 10: 41, 11: 35, 12: 42, 13: 37, 14: 34, 15: 27, 16: 44, 17: 50, 18: 26, 19: 39, 20: 39, 21: 35, 22: 55, 23: 32}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_13" src="https://user-images.githubusercontent.com/41605276/55571732-0a5f0180-5741-11e9-982e-eaf3d9245dc3.png">


    -----------------------------------------------------------
    InformClientSurvey 오전/오후별 카운트 Counter({'AM': 485, 'PM': 442})
    -----------------------------------------------------------
    InformClientSurvey 시간별 카운트 {0: 39, 1: 47, 2: 34, 3: 37, 4: 48, 5: 46, 6: 42, 7: 47, 8: 26, 9: 31, 10: 41, 11: 47, 12: 39, 13: 29, 14: 33, 15: 41, 16: 34, 17: 33, 18: 48, 19: 38, 20: 36, 21: 38, 22: 42, 23: 31}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_15" src="https://user-images.githubusercontent.com/41605276/55571746-121ea600-5741-11e9-82e1-5b7ef133f798.png">


    -----------------------------------------------------------
    Survey 오전/오후별 카운트 Counter({'AM': 495, 'PM': 432})
    -----------------------------------------------------------
    Survey 시간별 카운트 {0: 31, 1: 50, 2: 46, 3: 31, 4: 47, 5: 46, 6: 43, 7: 39, 8: 31, 9: 38, 10: 49, 11: 44, 12: 34, 13: 27, 14: 33, 15: 40, 16: 40, 17: 36, 18: 30, 19: 33, 20: 35, 21: 31, 22: 56, 23: 37}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_17" src="https://user-images.githubusercontent.com/41605276/55571755-18148700-5741-11e9-9fcc-347e829fce28.png">


    -----------------------------------------------------------
    InternRepair 오전/오후별 카운트 Counter({'PM': 274, 'AM': 273})
    -----------------------------------------------------------
    InternRepair 시간별 카운트 {0: 16, 1: 18, 2: 34, 3: 20, 4: 17, 5: 18, 6: 30, 7: 28, 8: 23, 9: 14, 10: 26, 11: 29, 12: 31, 13: 16, 14: 25, 15: 27, 16: 18, 17: 11, 18: 34, 19: 27, 20: 19, 21: 17, 22: 24, 23: 25}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_61_19" src="https://user-images.githubusercontent.com/41605276/55571762-1d71d180-5741-11e9-873f-ba4c73e7b149.png">



    -----------------------------------------------------------
    ImmediateRepair 오전/오후별 카운트 Counter({'AM': 197, 'PM': 186})
    -----------------------------------------------------------
    ImmediateRepair 시간별 카운트 {0: 17, 1: 14, 2: 21, 3: 15, 4: 17, 5: 19, 6: 13, 7: 13, 8: 20, 9: 11, 10: 16, 11: 21, 12: 10, 13: 20, 14: 13, 15: 15, 16: 19, 17: 18, 18: 18, 19: 11, 20: 15, 21: 18, 22: 9, 23: 20}
    


<img width="399" alt="프로세스분석_입사지원과제_박민수_61_21" src="https://user-images.githubusercontent.com/41605276/55571777-2662a300-5741-11e9-894a-a53a3584d481.png">


    -----------------------------------------------------------
    ExternRepair 오전/오후별 카운트 Counter({'PM': 69, 'AM': 63})
    -----------------------------------------------------------
    ExternRepair 시간별 카운트 {0: 3, 1: 6, 2: 7, 3: 4, 4: 8, 5: 3, 6: 7, 7: 6, 8: 3, 9: 7, 10: 5, 11: 4, 12: 8, 13: 7, 14: 6, 15: 5, 16: 5, 17: 4, 18: 4, 19: 8, 20: 5, 21: 3, 22: 9, 23: 5}
    


<img width="383" alt="프로세스분석_입사지원과제_박민수_61_23" src="https://user-images.githubusercontent.com/41605276/55571784-2c588400-5741-11e9-9ade-ca242239c768.png">


    -----------------------------------------------------------
    InformClientWrongPlace 오전/오후별 카운트 Counter({'AM': 38, 'PM': 35})
    -----------------------------------------------------------
    InformClientWrongPlace 시간별 카운트 {0: 2, 1: 6, 2: 4, 3: 2, 4: 4, 5: 6, 6: 1, 7: 2, 8: 2, 9: 5, 10: 2, 11: 2, 12: 4, 13: 5, 14: 4, 15: 3, 17: 1, 18: 5, 19: 1, 20: 3, 21: 2, 22: 3, 23: 4}
    


<img width="383" alt="프로세스분석_입사지원과제_박민수_61_25" src="https://user-images.githubusercontent.com/41605276/55571796-35495580-5741-11e9-96e5-d075439eb9bb.png">


    -----------------------------------------------------------
    

#### step 3) complete_hour에 따른 유의미한 현상 여부확인

결론 : 특별하게 유의미한 결과 없음


```python
for taskID in taskID_list:
    
    temp = df_new[df_new['taskID'] == '{}'.format(taskID)]
    temp['Morning_and_afternoon'] = df_new['complete_hour'].apply(lambda data : "AM" if data < 12 else "PM" )
    temp_list =  list(temp['Morning_and_afternoon'])

    result = Counter(temp_list)
    print('{} 오전/오후별 카운트'.format(taskID),result)
    
    print('-----------------------------------------------------------')
    
    temp_list =  list(temp['start_hour'])
    
    result = Counter(temp_list)
    result = dict(sorted(dict(result).items()))
    
    print('{} 시간별 카운트'.format(taskID),result)
    plt.bar(range(len(result)), list(result.values()), align='center')
    plt.xticks(range(len(result)), list(result.keys()))
    plt.xlabel('hour')
    plt.ylabel('counts')
    plt.show()
    
    print('-----------------------------------------------------------')
```

    FirstContact 오전/오후별 카운트 Counter({'AM': 505, 'PM': 495})
    -----------------------------------------------------------
    FirstContact 시간별 카운트 {0: 41, 1: 49, 2: 34, 3: 38, 4: 58, 5: 51, 6: 48, 7: 38, 8: 30, 9: 39, 10: 38, 11: 41, 12: 39, 13: 50, 14: 42, 15: 42, 16: 35, 17: 36, 18: 45, 19: 48, 20: 37, 21: 36, 22: 46, 23: 39}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_1" src="https://user-images.githubusercontent.com/41605276/55571810-3d08fa00-5741-11e9-9371-b65d89b87444.png">


    -----------------------------------------------------------
    MakeTicket 오전/오후별 카운트 Counter({'AM': 464, 'PM': 463})
    -----------------------------------------------------------
    MakeTicket 시간별 카운트 {0: 39, 1: 43, 2: 30, 3: 36, 4: 54, 5: 45, 6: 47, 7: 36, 8: 28, 9: 34, 10: 36, 11: 39, 12: 35, 13: 45, 14: 38, 15: 39, 16: 35, 17: 35, 18: 40, 19: 47, 20: 34, 21: 34, 22: 43, 23: 35}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_3" src="https://user-images.githubusercontent.com/41605276/55571822-44c89e80-5741-11e9-9ee0-b2759a3a4659.png">


    -----------------------------------------------------------
    ArrangeSurvey 오전/오후별 카운트 Counter({'AM': 485, 'PM': 442})
    -----------------------------------------------------------
    ArrangeSurvey 시간별 카운트 {0: 36, 1: 44, 2: 31, 3: 34, 4: 54, 5: 44, 6: 48, 7: 36, 8: 28, 9: 34, 10: 37, 11: 38, 12: 38, 13: 42, 14: 36, 15: 41, 16: 35, 17: 34, 18: 35, 19: 49, 20: 38, 21: 37, 22: 42, 23: 36}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_5" src="https://user-images.githubusercontent.com/41605276/55571830-4b571600-5741-11e9-9ab5-bce558a9dfbf.png">


    -----------------------------------------------------------
    SendTicketToFinAdmin 오전/오후별 카운트 Counter({'AM': 474, 'PM': 453})
    -----------------------------------------------------------
    SendTicketToFinAdmin 시간별 카운트 {0: 43, 1: 34, 2: 34, 3: 46, 4: 35, 5: 47, 6: 43, 7: 40, 8: 33, 9: 33, 10: 50, 11: 36, 12: 42, 13: 32, 14: 30, 15: 21, 16: 48, 17: 51, 18: 31, 19: 41, 20: 41, 21: 36, 22: 47, 23: 33}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_7" src="https://user-images.githubusercontent.com/41605276/55571843-514cf700-5741-11e9-9cbc-689df619550a.png">


    -----------------------------------------------------------
    RepairReady 오전/오후별 카운트 Counter({'PM': 473, 'AM': 454})
    -----------------------------------------------------------
    RepairReady 시간별 카운트 {0: 35, 1: 41, 2: 36, 3: 31, 4: 35, 5: 37, 6: 47, 7: 40, 8: 37, 9: 36, 10: 45, 11: 34, 12: 38, 13: 24, 14: 43, 15: 49, 16: 36, 17: 35, 18: 48, 19: 40, 20: 37, 21: 27, 22: 54, 23: 42}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_9" src="https://user-images.githubusercontent.com/41605276/55571862-57db6e80-5741-11e9-9516-b85bcf1315e1.png">


    -----------------------------------------------------------
    ReadyInformClient 오전/오후별 카운트 Counter({'PM': 465, 'AM': 462})
    -----------------------------------------------------------
    ReadyInformClient 시간별 카운트 {0: 34, 1: 38, 2: 39, 3: 40, 4: 36, 5: 25, 6: 40, 7: 48, 8: 40, 9: 37, 10: 48, 11: 37, 12: 37, 13: 43, 14: 36, 15: 29, 16: 39, 17: 38, 18: 30, 19: 42, 20: 48, 21: 33, 22: 50, 23: 40}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_11" src="https://user-images.githubusercontent.com/41605276/55571883-6164d680-5741-11e9-9a30-5c0fd0a16a7e.png">


    -----------------------------------------------------------
    TicketReady 오전/오후별 카운트 Counter({'AM': 467, 'PM': 460})
    -----------------------------------------------------------
    TicketReady 시간별 카운트 {0: 46, 1: 38, 2: 33, 3: 38, 4: 43, 5: 35, 6: 47, 7: 41, 8: 30, 9: 40, 10: 41, 11: 35, 12: 42, 13: 37, 14: 34, 15: 27, 16: 44, 17: 50, 18: 26, 19: 39, 20: 39, 21: 35, 22: 55, 23: 32}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_13" src="https://user-images.githubusercontent.com/41605276/55571900-67f34e00-5741-11e9-8bd8-3795fad760c9.png">


    -----------------------------------------------------------
    InformClientSurvey 오전/오후별 카운트 Counter({'AM': 485, 'PM': 442})
    -----------------------------------------------------------
    InformClientSurvey 시간별 카운트 {0: 39, 1: 47, 2: 34, 3: 37, 4: 48, 5: 46, 6: 42, 7: 47, 8: 26, 9: 31, 10: 41, 11: 47, 12: 39, 13: 29, 14: 33, 15: 41, 16: 34, 17: 33, 18: 48, 19: 38, 20: 36, 21: 38, 22: 42, 23: 31}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_15" src="https://user-images.githubusercontent.com/41605276/55571907-6e81c580-5741-11e9-9b49-59e9db42c7f7.png">


    -----------------------------------------------------------
    Survey 오전/오후별 카운트 Counter({'AM': 495, 'PM': 432})
    -----------------------------------------------------------
    Survey 시간별 카운트 {0: 31, 1: 50, 2: 46, 3: 31, 4: 47, 5: 46, 6: 43, 7: 39, 8: 31, 9: 38, 10: 49, 11: 44, 12: 34, 13: 27, 14: 33, 15: 40, 16: 40, 17: 36, 18: 30, 19: 33, 20: 35, 21: 31, 22: 56, 23: 37}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_17" src="https://user-images.githubusercontent.com/41605276/55571915-7477a680-5741-11e9-9e0e-e8e4babe4767.png">


    -----------------------------------------------------------
    InternRepair 오전/오후별 카운트 Counter({'PM': 276, 'AM': 271})
    -----------------------------------------------------------
    InternRepair 시간별 카운트 {0: 16, 1: 18, 2: 34, 3: 20, 4: 17, 5: 18, 6: 30, 7: 28, 8: 23, 9: 14, 10: 26, 11: 29, 12: 31, 13: 16, 14: 25, 15: 27, 16: 18, 17: 11, 18: 34, 19: 27, 20: 19, 21: 17, 22: 24, 23: 25}
    


<img width="389" alt="프로세스분석_입사지원과제_박민수_63_19" src="https://user-images.githubusercontent.com/41605276/55571927-7b9eb480-5741-11e9-822e-9ec333492862.png">


    -----------------------------------------------------------
    ImmediateRepair 오전/오후별 카운트 Counter({'PM': 196, 'AM': 187})
    -----------------------------------------------------------
    ImmediateRepair 시간별 카운트 {0: 17, 1: 14, 2: 21, 3: 15, 4: 17, 5: 19, 6: 13, 7: 13, 8: 20, 9: 11, 10: 16, 11: 21, 12: 10, 13: 20, 14: 13, 15: 15, 16: 19, 17: 18, 18: 18, 19: 11, 20: 15, 21: 18, 22: 9, 23: 20}
    


<img width="399" alt="프로세스분석_입사지원과제_박민수_63_21" src="https://user-images.githubusercontent.com/41605276/55571940-80636880-5741-11e9-803d-fd81c0f8e205.png">


    -----------------------------------------------------------
    ExternRepair 오전/오후별 카운트 Counter({'PM': 69, 'AM': 63})
    -----------------------------------------------------------
    ExternRepair 시간별 카운트 {0: 3, 1: 6, 2: 7, 3: 4, 4: 8, 5: 3, 6: 7, 7: 6, 8: 3, 9: 7, 10: 5, 11: 4, 12: 8, 13: 7, 14: 6, 15: 5, 16: 5, 17: 4, 18: 4, 19: 8, 20: 5, 21: 3, 22: 9, 23: 5}
    


<img width="383" alt="프로세스분석_입사지원과제_박민수_63_23" src="https://user-images.githubusercontent.com/41605276/55571948-86f1e000-5741-11e9-9967-271e216aae9f.png">


    -----------------------------------------------------------
    InformClientWrongPlace 오전/오후별 카운트 Counter({'AM': 38, 'PM': 35})
    -----------------------------------------------------------
    InformClientWrongPlace 시간별 카운트 {0: 2, 1: 6, 2: 4, 3: 2, 4: 4, 5: 6, 6: 1, 7: 2, 8: 2, 9: 5, 10: 2, 11: 2, 12: 4, 13: 5, 14: 4, 15: 3, 17: 1, 18: 5, 19: 1, 20: 3, 21: 2, 22: 3, 23: 4}
    


<img width="383" alt="프로세스분석_입사지원과제_박민수_63_25" src="https://user-images.githubusercontent.com/41605276/55571965-8d805780-5741-11e9-9f92-e9f88ab99846.png">


    -----------------------------------------------------------
    

#### step4) task 별 평균 처리소요시간 산출 및 확인


```python
taskID_list = list(df_new['taskID'].value_counts().index)

print("task별 평균 처리소요시간 : \n")
for taskID in taskID_list:
    temp = df_new[df_new['taskID'] == taskID]
    print(taskID, '/', str(np.mean(temp['run_time']))[8],'시간',str(np.mean(temp['run_time']))[10:-10],'분')
```

    task별 평균 처리소요시간 : 
    
    FirstContact / 0 시간  분
    MakeTicket / 0 시간 04 분
    ArrangeSurvey / 2 시간 00 분
    SendTicketToFinAdmin / 0 시간  분
    RepairReady / 0 시간  분
    ReadyInformClient / 0 시간  분
    TicketReady / 0 시간  분
    InformClientSurvey / 0 시간  분
    Survey / 0 시간 40 분
    InternRepair / 3 시간 44 분
    ImmediateRepair / 1 시간 56 분
    ExternRepair / 4 시간 58 분
    InformClientWrongPlace / 0 시간  분
    

- 이는 'RepairInternally'가 True냐 False에 따라 EstimatedRepairTime 차이가 나는 정보로 뒷받침할 수 있다.


```python
repair_list = list(df['RepairCode'].value_counts().index)

temp_df = pd.DataFrame(columns=("caseID", "taskID", "originator", "timestamp", "EstimatedRepairTime"))

for data in repair_list:
    temp = df[df['RepairCode'] == data]
    temp_df = pd.concat([temp_df,temp])
    
temp_df = temp_df.reset_index(drop=True)
temp_df = temp_df[["caseID", "taskID", "originator","timestamp","contact","RepairType", "objectKey",\
                   "RepairInternally","EstimatedRepairTime","RepairCode","RepairOK"]]

print('RepairInternally가 True일때 평균 EstimatedRepairTime : ' ,round(np.mean(temp_df[temp_df['RepairInternally']==True].EstimatedRepairTime),2))
print('RepairInternally가 False일때 평균 EstimatedRepairTime : ' ,round(np.mean(temp_df[temp_df['RepairInternally']==False].EstimatedRepairTime),2))
```

    RepairInternally가 True일때 평균 EstimatedRepairTime :  180.18
    RepairInternally가 False일때 평균 EstimatedRepairTime :  402.71
    

## 3. 현재 수리 프로세스에 대한 개선방안

- 주어진 데이터는 timestamp 기준의 데이터 정렬방식으로 수리 프로세스를 직관적으로 이해하기 어렵기 때문에 앞쪽에 정의한 'df_new' 데이터프레임과 같이 taskID를 기준으로 데이터 프레임을 재정렬 필요

## 4. 데이터 규모가 커질 경우 처리방안


- Apache Spark 등 대용량 데이터 처리툴을 이용하여 클러스터를 구성하고 분산저장 및 처리하는 방안을 강구할 수 있음


- 기존의 데이터 저장방식을 고수해야 한다면, 데이터 처리에 있어서는 AWS lambda나 python dask 등의 툴을 이용하는 방안을 강구할 수 있음
