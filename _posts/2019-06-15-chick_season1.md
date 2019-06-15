---
layout: post
title: "칰러닝(시즌1) 프로젝트 결과"
tags: [Classification, 개인프로젝트]
comments: true
---

.

##  칰러닝(시즌1) 프로젝트 결과

> 치킨브랜드별 이미지 분류를 위한 그레디언트 부스트 모델 구현 및 AWS sagemaker를 이용한 모델 서비스 제공 아키텍처 구축

### 1. 개요

#### 1. 1. 아이디어

- 인스타그램에서 크롤링한 치킨이미지 데이터를 input 값으로 넣어주면 ouput 값으로 치킨브랜드 이름을 분류하는 치믈리에 머신러닝 구현


- 추가적으로 AWS sagemaker를 이용하여 해당 머신러닝 모델을 제공하는 시스템을 구축


- 즉, 인스타그램 크롤링 -> 머신러닝 모델 구현 -> AWS를 이용한 서비스 제공 시스템 구축

   
#### 1. 2. 프로젝트 목적

- 치킨브랜드별 이미지 분류 가능한 그레디언트 부스트 모델 구현 및 AWS sagemaker를 이용한 서비스 제공 시스템 구축

#### 1. 3.  프로젝트 구현 아키텍처

![1](https://user-images.githubusercontent.com/41605276/59547725-62ef1e00-8f7e-11e9-8d13-5bcf77b2f31e.png)

### 2. 프로젝트 로드맵 및 수행경과

#### STEP 1) 문제정의 및 프로젝트 계획 구상

- 아이디어 도출 


- 프로젝트 구현 아키텍처 구상


- 프로젝트 시행 계획 수립


#### STEP 2) 데이터 수집


- 인스타그램 웹크롤러를 이용한 데이터 수집


- 사용한 웹크롤러 : https://github.com/huaying/instagram-crawler


- 이슈 : 순수한 치킨 사진 외 관련 없는 사진도 수집되는 현상 식별


- 해결방안 : 순수한 치킨 사진 감별 및 불필요한 사진 데이터 제거


- 결론적으로 4개의 브랜드(BBQ, 굽네, 교촌, 네네), 브랜드별 170장의 치킨이미지 데이터 수집

#### STEP 3) 데이터 탐색 및 전처리


- 원활한 모델학습을 위해 치킨이 화면 대부분을 차지하지 않거나, 다른음식도 같이 섞여 있는 이미지 데이터는 삭제조치


- 머신러닝 모델 반영을 위한 픽셀 단위의 행렬데이터로의 변환을 수행하는 코딩 수행


- data shuffling 및 train, test data set 무작위 split


#### STEP 4) 모델링 및 평가

- 'STEP3) 데이터 탐색 및 전처리 작업'까지는 로컬에서 수행하고, 이후 전처리한 데이터에 대해 AWS S3 탑재


- 차원의 저주문제 해결을 위한 특징선택 방법 적용


- 그레디언트 부스트(XGboost) 모델 적용

#### STEP 5) AWS sagemaker 로 모델 training 및 Endpoint 등록

- sagemaker 노트북 인스턴스 생성 및 모델링 작업 수행

![6](https://user-images.githubusercontent.com/41605276/59547728-6bdfef80-8f7e-11e9-8f2d-016aaf00d4dc.png)

- training 한 모델 저장 및 S3 업로드


- Endpoint 등록


- Endpoint 등록된 모델 테스트

### 3. AWS sagemaker 노트북에서 사용한 코드

### 1) Setup


```python
%%time

import os
import boto3
import re
import json
from sagemaker import get_execution_role

region = boto3.Session().region_name

role = get_execution_role()

bucket='minman2115' # put your s3 bucket name here, and create s3 bucket
prefix = 'sagemaker/chicklearning'
bucket_path = 'https://s3-{}.amazonaws.com/{}'.format(region,bucket)
# customize to your bucket where you have stored the data
```

    CPU times: user 938 ms, sys: 151 ms, total: 1.09 s
    Wall time: 989 ms
    


```python
!conda install -y -c conda-forge xgboost
```

    Solving environment: done
    
    
    ==> WARNING: A newer version of conda exists. <==
      current version: 4.5.12
      latest version: 4.6.14
    
    Please update conda by running
    
        $ conda update -n base -c defaults conda
    
    
    
    ## Package Plan ##
    
      environment location: /home/ec2-user/anaconda3/envs/tensorflow_p36
    
      added / updated specs: 
        - xgboost
    
    
    The following packages will be downloaded:
    
        package                    |            build
        ---------------------------|-----------------
        libxgboost-0.82            |       he1b5a44_0         3.9 MB  conda-forge
        xgboost-0.82               |   py36he1b5a44_0           9 KB  conda-forge
        _py-xgboost-mutex-2.0      |            cpu_0           8 KB  conda-forge
        py-xgboost-0.82            |   py36he1b5a44_0          70 KB  conda-forge
        openssl-1.0.2r             |       h14c3975_0         3.1 MB  conda-forge
        ------------------------------------------------------------
                                               Total:         7.1 MB
    
    The following NEW packages will be INSTALLED:
    
        _py-xgboost-mutex: 2.0-cpu_0           conda-forge
        libxgboost:        0.82-he1b5a44_0     conda-forge
        py-xgboost:        0.82-py36he1b5a44_0 conda-forge
        xgboost:           0.82-py36he1b5a44_0 conda-forge
    
    The following packages will be UPDATED:
    
        ca-certificates:   2019.1.23-0                     --> 2019.3.9-hecc5488_0 conda-forge
        certifi:           2019.3.9-py36_0                 --> 2019.3.9-py36_0     conda-forge
        openssl:           1.0.2r-h7b6447c_0               --> 1.0.2r-h14c3975_0   conda-forge
    
    
    Downloading and Extracting Packages
    libxgboost-0.82      | 3.9 MB    | ##################################### | 100% 
    xgboost-0.82         | 9 KB      | ##################################### | 100% 
    _py-xgboost-mutex-2. | 8 KB      | ##################################### | 100% 
    py-xgboost-0.82      | 70 KB     | ##################################### | 100% 
    openssl-1.0.2r       | 3.1 MB    | ##################################### | 100% 
    Preparing transaction: done
    Verifying transaction: done
    Executing transaction: done
    

### 2) Data preprocessing


```python
import numpy as np
import os, sys
from PIL import Image
from sklearn.preprocessing import LabelEncoder
from sklearn.utils import shuffle
import boto3
import pandas as pd
import s3fs

def process_data():
    
    brend_names = ['BBQ', 'goobne', 'kyochon', 'nene']

    img_data = []
    target_data = []

    # 이미지 데이터 탑재한 S3에 접근
    fs = s3fs.S3FileSystem()
    
    # 브랜드별 이미지 처리
    # 기존 이미지 데이터 사이즈 = 640 * 640 RGB
    # 기존 이미지 데이터 사이즈를 40 * 40 RGB 1 * 4800 사이즈 행렬로 변경
    for brend in brend_names:

        s3_data_list = fs.ls('s3://minman2115/data/' + brend)

        for data in s3_data_list[0:170+1]:
            with fs.open('s3://{}'.format(data)) as f:
                img = Image.open(f)

                resize_img = img.resize((40,40))
                array_of_img = np.array(resize_img)

                img_data.append(array_of_img.reshape(4800,).astype('float32') / 255.0)
                
                # 각 데이터에 대한 라벨링 처리
                if brend == 'BBQ':
                    target_data.append(0)
                    
                elif brend == 'goobne' : 
                    target_data.append(1)
                
                elif brend == 'kyochon' : 
                    target_data.append(2)
                
                else :
                    target_data.append(3)                    
                    
    img_data = np.array(img_data)
    target_data = np.array(target_data).astype('int32')
    
    # train, test split
    x, y = shuffle(img_data, target_data, random_state=42)

    x_train = x[:600+1]
    y_train = y[:600+1]

    x_test = x[600:]
    y_test = y[600:]
                                          
    return x_train, y_train, x_test, y_test

x_train, y_train, x_test, y_test = process_data()
```

### 3) Select features

conclusion = select 400 features by mutual information


```python
import matplotlib.pyplot as plt
%matplotlib inline
%config InlineBackend.figure_format = 'retina'
import xgboost as xgb
import sklearn as sk 
from sklearn.feature_selection import *
from sklearn.metrics import f1_score

k_list = []
score_list = []
for k in range(1, 4800, 400):
    model = xgb.XGBClassifier(max_depth = 5,learning_rate = 0.2,n_estimators = 10,objective = 'multi:softmax')
    selector = SelectKBest(mutual_info_classif, k).fit(x_train, y_train)
    New_Train_X = selector.transform(x_train)
    New_Test_X = selector.transform(x_test)    
    model.fit(New_Train_X, y_train)
    predicted = model.predict(New_Test_X)
    score = f1_score(y_test, predicted, average='micro')
    k_list.append(k)
    score_list.append(score)

plt.plot(k_list, score_list)
plt.xlabel("number of Best features")
plt.ylabel("f1 score")
plt.show()
```


<img width="394" alt="칰러닝(시즌1) 프로젝트 결과_10_0" src="https://user-images.githubusercontent.com/41605276/59547730-77cbb180-8f7e-11e9-9021-bfe221c2e1bb.png">


### 4) modeling


```python
model = xgb.XGBClassifier(max_depth = 5,learning_rate = 0.2,n_estimators = 10,objective = 'multi:softmax')
selector = SelectKBest(mutual_info_classif, 400).fit(x_train, y_train)

New_Train_X = selector.transform(x_train)
New_Test_X = selector.transform(x_test)

model.fit(New_Train_X, y_train)
```




    XGBClassifier(base_score=0.5, booster='gbtree', colsample_bylevel=1,
           colsample_bytree=1, gamma=0, learning_rate=0.2, max_delta_step=0,
           max_depth=5, min_child_weight=1, missing=None, n_estimators=10,
           n_jobs=1, nthread=None, objective='multi:softprob', random_state=0,
           reg_alpha=0, reg_lambda=1, scale_pos_weight=1, seed=None,
           silent=True, subsample=1)



### 5) Check classification report


```python
from sklearn.metrics import classification_report, accuracy_score

y_pred_train = model.predict(New_Train_X)
y_pred_test = model.predict(New_Test_X)

print(classification_report(y_train, y_pred_train))

print(classification_report(y_test, y_pred_test))

score = f1_score(y_test, y_pred_test, average='micro')
```

                  precision    recall  f1-score   support
    
               0       1.00      1.00      1.00       151
               1       1.00      1.00      1.00       149
               2       1.00      1.00      1.00       145
               3       1.00      1.00      1.00       156
    
       micro avg       1.00      1.00      1.00       601
       macro avg       1.00      1.00      1.00       601
    weighted avg       1.00      1.00      1.00       601
    
                  precision    recall  f1-score   support
    
               0       0.31      0.45      0.37        20
               1       0.50      0.45      0.48        22
               2       0.61      0.41      0.49        27
               3       0.41      0.47      0.44        15
    
       micro avg       0.44      0.44      0.44        84
       macro avg       0.46      0.44      0.44        84
    weighted avg       0.47      0.44      0.45        84
    
    

### 6) Save trained model instance


```python
model_file_name = "CL-model"
model._Booster.save_model(model_file_name)
```

- check my model instance


```python
!tar czvf model.tar.gz $model_file_name
```

    CL-model


### 7) Save my 'CL-model' to S3


```python
fObj = open("model.tar.gz", 'rb')
key= os.path.join(prefix, model_file_name, 'model.tar.gz')
boto3.Session().resource('s3').Bucket(bucket).Object(key).upload_fileobj(fObj)
```

7) 수행결과

![2](https://user-images.githubusercontent.com/41605276/59547733-82864680-8f7e-11e9-8d87-8c856b03fc8c.png)

### 8) Import model into hosting

This involves creating a SageMaker model from the model file previously uploaded to S3.


```python
from sagemaker.amazon.amazon_estimator import get_image_uri

container = get_image_uri(boto3.Session().region_name, 'xgboost')
```


```python
%%time
from time import gmtime, strftime

model_name = model_file_name + strftime("%Y-%m-%d-%H-%M-%S", gmtime())
model_url = 'https://s3-{}.amazonaws.com/{}/{}'.format(region,bucket,key)
sm_client = boto3.client('sagemaker')

print (model_url)

primary_container = {
    'Image': container,
    'ModelDataUrl': model_url,
}

create_model_response2 = sm_client.create_model(
    ModelName = model_name,
    ExecutionRoleArn = role,
    PrimaryContainer = primary_container)

print(create_model_response2['ModelArn'])
```

    https://s3-ap-northeast-2.amazonaws.com/minman2115/sagemaker/chicklearning/CL-model/model.tar.gz
    arn:aws:sagemaker:ap-northeast-2:161461013751:model/cl-model2019-06-15-05-08-47
    CPU times: user 50.9 ms, sys: 3.97 ms, total: 54.9 ms
    Wall time: 402 ms
    

8) 수행결과

![3](https://user-images.githubusercontent.com/41605276/59547735-887c2780-8f7e-11e9-857b-3cf178e5ed3a.png)

### 9) Create endpoint configuration


```python
from time import gmtime, strftime

endpoint_config_name = 'CL-modelEndpointConfig-' + strftime("%Y-%m-%d-%H-%M-%S", gmtime())
print(endpoint_config_name)
create_endpoint_config_response = sm_client.create_endpoint_config(
    EndpointConfigName = endpoint_config_name,
    ProductionVariants=[{
        'InstanceType':'ml.m4.xlarge',
        'InitialInstanceCount':1,
        'InitialVariantWeight':1,
        'ModelName':model_name,
        'VariantName':'AllTraffic'}])

print("Endpoint Config Arn: " + create_endpoint_config_response['EndpointConfigArn'])
```

    CL-modelEndpointConfig-2019-06-15-05-09-53
    Endpoint Config Arn: arn:aws:sagemaker:ap-northeast-2:161461013751:endpoint-config/cl-modelendpointconfig-2019-06-15-05-09-53
    

9) 수행결과

![4](https://user-images.githubusercontent.com/41605276/59547737-8dd97200-8f7e-11e9-9cd4-084367dd053d.png)

### 10) Create endpoint


```python
%%time
import time

endpoint_name = 'CL-modelEndpointConfig-' + strftime("%Y-%m-%d-%H-%M-%S", gmtime())
print(endpoint_name)
create_endpoint_response = sm_client.create_endpoint(
    EndpointName=endpoint_name,
    EndpointConfigName=endpoint_config_name)
print(create_endpoint_response['EndpointArn'])

resp = sm_client.describe_endpoint(EndpointName=endpoint_name)
status = resp['EndpointStatus']
print("Status: " + status)

while status=='Creating':
    time.sleep(60)
    resp = sm_client.describe_endpoint(EndpointName=endpoint_name)
    status = resp['EndpointStatus']
    print("Status: " + status)

print("Arn: " + resp['EndpointArn'])
print("Status: " + status)
```

    CL-modelEndpointConfig-2019-06-15-05-10-15
    arn:aws:sagemaker:ap-northeast-2:161461013751:endpoint/cl-modelendpointconfig-2019-06-15-05-10-15
    Status: Creating
    Status: Creating
    Status: Creating
    Status: Creating
    Status: Creating
    Status: Creating
    Status: Creating
    Status: Creating
    Status: InService
    Arn: arn:aws:sagemaker:ap-northeast-2:161461013751:endpoint/cl-modelendpointconfig-2019-06-15-05-10-15
    Status: InService
    CPU times: user 137 ms, sys: 12.1 ms, total: 149 ms
    Wall time: 8min 1s
    

10) 수행결과

![5](https://user-images.githubusercontent.com/41605276/59547739-929e2600-8f7e-11e9-974a-29af3a9fe885.png)

### 11) Validate the model for use


```python
runtime_client = boto3.client('runtime.sagemaker')

import numpy as np

point_X = New_Test_X[0]
point_X = np.expand_dims(point_X, axis=0)
point_y = y_test[0]
np.savetxt("test_point.csv", point_X, delimiter=",")
```


```python
%%time
import json


file_name = 'test_point.csv' 
#customize to your test file, will be 'mnist.single.test' if use data above

with open(file_name, 'r') as f:
    payload = f.read().strip()

response = runtime_client.invoke_endpoint(EndpointName=endpoint_name, 
                                   ContentType='text/csv', 
                                   Body=payload)
result = response['Body'].read().decode('ascii')
print('Predicted Class Probabilities: {}.'.format(result))
```

    Predicted Class Probabilities: [0.18522018194198608, 0.11626110225915909, 0.6055818200111389, 0.09293686598539352].
    CPU times: user 15.4 ms, sys: 0 ns, total: 15.4 ms
    Wall time: 170 ms
    


```python
floatArr = np.array(json.loads(result))
predictedLabel = np.argmax(floatArr)
print('Predicted Class Label: {}.'.format(predictedLabel))
print('Actual Class Label: {}.'.format(point_y))
```

    Predicted Class Label: 2.
    Actual Class Label: 2.
    

### 4. 향후 개선 및 보완사항

1) 브랜드 별 데이터 부족으로 과적합 현상 일부식별, 저조한 모델퍼포먼스 확인

해결방안 : 데이터를 추가적으로 수집

2) 모델의 정밀함 향상 필요

추진방향 : 모델 퍼포먼스 향상을 위한 브랜드 수, 픽셀 크기, 데이터 양, 모델 파라미터 등을 점진적으로 늘려서 시도

3) CNN 등 이미지 classification에 적합한 딥러닝 모델 적용시도 필요