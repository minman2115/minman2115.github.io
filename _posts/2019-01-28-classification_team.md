---
layout: post
title: "'동물보호센터 유기동물 최종 처분결과' 예측 모델링"
tags: [머신러닝, Classification, 팀프로젝트]
comments: true
---

Classification 팀프로젝트 결과

# Shelter Animal Outcomes Classification

# 1. 개요

## 1.1 수행과제 선정
- Contents 주제 : **'Shelter Animal Outcomes'** on kaggle competition
   * URL : https://www.kaggle.com/c/shelter-animal-outcomes
        
- Contents 목적 : 동물보호센터 내 유기동물 데이터로부터 특정 유기동물에 대한 최종 처분결과가 어떻게 되었는지 예측

## 1.2 목적 재설정
- Austin Animal Center 에서 보호하게 되는 동물들 중, 새로운 보금자리를 찾아 주기 위해 센터에서 더욱 신경 써야하는 동물들 선별

## 1.3 종속변수 설정
- 기존 데이터 종속변수(Outcome)
    - Adoption
    - Return_to_owner
    - Transfer
    - Died
    - Euthanasia
    
    
- 변경한 종속변수(Outcome)
    - `Safe` : Adoption, Return_to_owner, Transfer
    - `Danger` : Died, Euthanasia

## 1.4 이슈 및 해결과정
**Issue 1)** 주어진 데이터의 일부가 Outcome 당시 데이터임을 파악  
**=>** 주어진 Outcome 데이터 중 Intake 당시에도 알 수 있는 Feature만 선택

**Issue 2)** 설명력 있는 변수가 부족    
**=>** Intake 당시의 데이터와 Merge

**Issue 3)** Test 데이터의 Merge 결과가 부정확  
**=>** Merge한 Train 데이터 내에서 Test 데이터를 별도로 분리

**Issue 4)** Danger의 저조한 Recall  
**=>** Imbalancing 처리

## 1.5 최종 모델
![1](https://user-images.githubusercontent.com/41605276/51813357-356c4580-22f9-11e9-928e-1eb786043585.png)

# 2. 문제해결 과정

## 2.1 솔루션 1
- Kaggle Competition에서 주어진 데이터를 그대로 이용
    - 데이터에 대한 설명 부족으로 데이터가 Outcome 당시의 데이터라는 확신이 없었음

### 2.1.1 데이터 탐색 및 전처리
#### [ Featues ]
- **OutcomeType** : 최종 보호&관리 결과, 종속 변수
    - 'Euthanasia', 'Return_to_owner', 'Adoption', 'Transfer', 'Died'

- **AnimalID** : 유기동물의 고유ID값

- **Name** : 유기동물의 이름  
    
- **DateTime** : Outcome 당시 Timestamp(2013-10-01 ~ 2016-02-21)

- **AnimalType** : Dog / Cat

- **SexuponOutcome** : Outcome 당시 성별 및 중성화 여부
    - 'Neutered Male', 'Intact Male', 'Spayed Female', 'Intact Female', 'Unknown'
 
- **AgeuponOutcome** : Outcome 당시 나이
    - ex) '12 years', '2 months', '4 weeks', '1 day'
    
- **Breed** : 종
    - ex) 'Domestic Shorthair Mix', 'Bulldog'

- **Color** : 동물의 털 색/패턴
    - ex) 'Blue/White', 'Cream Tabby' 등

#### [ Feature Engineering ]
- **Name** : 이름 존재 유(1)/무(0) 인코딩
- **DateTime** : 년, 월, 일, 요일, 시, 분 추출
- **AnimalType** : Dog(0) / Cat(1) 인코딩 
- **SexuponOutcome**
    - 성과 중성화 여부로 분리
    - 성은 Female/Male/Unkown 분류 후 원핫인코딩
    - 중성화 여부는 중성화 Intact/Neutered/Unkown 분류 후 원핫 인코딩
- **AgeuponOutcome** : day를 단위로 Numeric으로 환산
    - ex) '2 months' => 2 * 30 = 60
- **Breed** : Pure(순종), Two_Hybrid, Multi_Hybrid(잡종) 분류 후 원핫인코딩
- **Color** : 패턴(12가지) 추출 후 각 패턴의 존재 유(1)/무(0)를 인코딩

### 2.1.2 Model 구현 
#### [ LightGBM ]
- 그리드 서치로 하이퍼 파라미터 설정
    - `n_estimators` : 800
    - `max_depth` : 30
    - `learning_rate` : 0.05

#### [ Classification Report ]


```python
target_names = ['Adoption', 'Died', 'Euthanasia', 'Return_to_owner', 'Transfer']
print(classification_report(y_test, gbm.predict(X_test), target_names=target_names))
```

                     precision    recall  f1-score   support
    
           Adoption       0.72      0.82      0.77      3231
               Died       0.80      0.14      0.23        59
         Euthanasia       0.53      0.26      0.35       466
    Return_to_owner       0.52      0.48      0.50      1436
           Transfer       0.77      0.75      0.76      2827
    
          micro avg       0.70      0.70      0.70      8019
          macro avg       0.67      0.49      0.52      8019
       weighted avg       0.69      0.70      0.69      8019
    
    

#### [ Feature Importance ]
- Importance 순으로 상위 10개 Feature 추출
- Intake 당시에도 알수 있는 Feature들은 상대적으로 Importance가 낮음
    - `Name`, `AnimalType`


```python
feature_imp = pd.DataFrame(list(zip(gbm.feature_importances_, df_train.columns)), columns=['Value','Feature'])

plt.figure(figsize=(8, 5))
sns.barplot(x="Value", y="Feature", data=feature_imp.sort_values(by="Value", ascending=False).iloc[:10])
plt.title('LightGBM Features (avg over folds)')
plt.tight_layout()
plt.show()
```


![classification_team_15_0](https://user-images.githubusercontent.com/41605276/51813168-44062d00-22f8-11e9-9687-9bce46cc756a.png)


## 2.2 솔루션 2
- Outcome데이터 내에서 **Intake 당시에도 알수 있는 Feature만 선택**하여 진행

### 2.2.1 데이터 탐색 및 전처리
#### [ Featues ]
- **OutcomeType** : 최종 보호&관리 결과, 종속 변수
    - 'Euthanasia', 'Return_to_owner', 'Adoption', 'Transfer', 'Died'

- **AnimalID** : 유기동물의 고유ID값

- **Name** : 유기동물의 이름은 Intake 당시에도 알수 있음
    
- **AnimalType** : 유기 동물의 종(Dog/Cat)은 Intake 당시에도 알 수 있음

- **SexuponOutcome** : Outcome 당시 성별의 성별은 Intake 당시에도 알 수 있음
    - ex) 'Neutered Male' => 'Male'
    
- **Breed** : 유기 동물의 세부 종은 Intake 당시에도 알 수 있음
    - ex) 'Domestic Shorthair Mix', 'Bulldog'

- **Color** : 동물의 털 색/패턴은 Intake 당시에도 알 수 있음
    - ex) 'Blue/White', 'Cream Tabby' 등

### 2.1.2 Model 구현
#### [ LightGBM ]
- 그리드 서치로 하이퍼 파라미터 설정
    - `n_estimators` : 200
    - `max_depth` : 5
    - `learning_rate` : 0.05

#### [ Classification Report ]
-'Died'와 'Enthanasia'에 대하여 'Precision'과 'Recall'은 0으로 확인


```python
target_names = ['Adoption', 'Died', 'Euthanasia', 'Return_to_owner', 'Transfer']
print(classification_report(y_test, gbm.predict(X_test), target_names=target_names))
```

                     precision    recall  f1-score   support
    
           Adoption       0.49      0.84      0.62      3231
               Died       0.00      0.00      0.00        59
         Euthanasia       0.00      0.00      0.00       466
    Return_to_owner       0.43      0.09      0.15      1436
           Transfer       0.65      0.50      0.57      2827
    
          micro avg       0.53      0.53      0.53      8019
          macro avg       0.31      0.28      0.27      8019
       weighted avg       0.50      0.53      0.47      8019
    
    

    /usr/local/lib/python3.7/site-packages/sklearn/metrics/classification.py:1143: UndefinedMetricWarning: Precision and F-score are ill-defined and being set to 0.0 in labels with no predicted samples.
      'precision', 'predicted', average, warn_for)
    /usr/local/lib/python3.7/site-packages/sklearn/metrics/classification.py:1143: UndefinedMetricWarning: Precision and F-score are ill-defined and being set to 0.0 in labels with no predicted samples.
      'precision', 'predicted', average, warn_for)
    /usr/local/lib/python3.7/site-packages/sklearn/metrics/classification.py:1143: UndefinedMetricWarning: Precision and F-score are ill-defined and being set to 0.0 in labels with no predicted samples.
      'precision', 'predicted', average, warn_for)
    

#### [ Classification Report ]
- 기존 Outcome Date에서 Intake 당시에도 알수 있는 데이터를 추론해서 추가
    - Infant, Birth(Year, Month)
- 크롤링을 통해 Feature 추가
    - 인기종, 알러지 유발 종, 인기있는 이름


```python
print(classification_report(y_test, gbm.predict(X_test), target_names=le_target.classes_))
```

                     precision    recall  f1-score   support
    
           Adoption       0.55      0.71      0.62      3231
               Died       0.00      0.00      0.00        59
         Euthanasia       0.53      0.08      0.13       466
    Return_to_owner       0.47      0.47      0.47      1436
           Transfer       0.64      0.54      0.58      2827
    
          micro avg       0.56      0.56      0.56      8019
          macro avg       0.44      0.36      0.36      8019
       weighted avg       0.56      0.56      0.55      8019
    
    

#### [ Feature Importance ]
- Importance 순으로 상위 10개 Feature 추출
- 저조한 성능 원인
    - **Intake** 당시에도 알 수 있는 Feature는 **Outcome Feature 보다 Importance가 상대적으로 매우 낮은 것으로 확인**


```python
feature_imp = pd.DataFrame(list(zip(gbm.feature_importances_, df_train.columns)), columns=['Value','Feature'])

plt.figure(figsize=(8, 5))
sns.barplot(x="Value", y="Feature", data=feature_imp.sort_values(by="Value", ascending=False).iloc[:10])
plt.title('LightGBM Features (avg over folds)')
plt.tight_layout()
plt.show()
```


![classification_team_24_0](https://user-images.githubusercontent.com/41605276/51813176-55e7d000-22f8-11e9-9be4-6f44483622a9.png)


## 2.3 솔루션 3
- Intake 당시의 데이터([Austin Open Data Portal](https://data.austintexas.gov/Health-and-Community-Services/Austin-Animal-Center-Intakes/wter-evkm)) 확보 및 기존의 Train 데이터와 Merge
- Intake 데이터
    - 2013-10-01부터 현재까지의 데이터
    - AnimalID가 여러번 중복 => 한 동물이 센터에 여러번 맡겨지는 경우 발생
- 기존 Train 데이터 내 'Outcome Date'를 기준으로 가장 최근의 'Intake Date'가 포함된 데이터를 선택하여 중복문제를 해결
- Merge된 데이터는 기존 Train 데이터 대비 663개 감소
- Test 데이터는 고유한 AnimalID가 존재하지 않기 때문에 Merge할 경우 데이터의 신뢰성을 보장할 수 없으므로 활용하지 않는 것으로 결정

### 2.4.2 결측값

#### 1) 결측값 검사


```python
train_intake.isna().sum()
```




    AnimalID              0
    Name               7584
    OutcomeType           0
    AnimalType            0
    Breed                 0
    Color                 0
    Intake_Datetime       0
    IntakeType            0
    IntakeCondition       0
    SexUponIntake         1
    AgeUponIntake         0
    dtype: int64



#### 2) 결측값 처리

##### SexUponIntake
'Unknown'으로 대체

##### Name
이후 전처리 과정에서 이름 없음(0)으로 처리

### 2.4.3 Binary Classification으로 변환
- Imbalance가 심한 데이터로 모든 OutcomeType을 정확하게 맞추는 것에 한계가 있음
- Animal Center에서 실질적으로 필요한 것은 좀 더 신경을 써야하는 동물을 선별해야 한다고 판단됨
- 종속변수의 범주를 'Danger'와 'safe' 로 변환
    - 'Return_to_owner', 'Adoption', 'Transfer' => **Safe**
    - 'Euthanasia', 'Died' => **Danger**

#### 1) 기존 종속변수


```python
train_intake['OutcomeType'].value_counts()
```




    Adoption           10382
    Transfer            9216
    Return_to_owner     4750
    Euthanasia          1529
    Died                 189
    Name: OutcomeType, dtype: int64



#### 2) 변경된 종속변수


```python
train_intake['OutcomeType'].value_counts()
```




    Safe      24348
    Danger     1718
    Name: OutcomeType, dtype: int64



### 2.4.4 EDA

### Name(이름)에 따른 분포차이
- 이름이 없으면 위험군에 분류될 가능성이 더 높다.

![2](https://user-images.githubusercontent.com/41605276/51813185-61d39200-22f8-11e9-8860-af870ff9ec1a.png)

### 중성화 여부에 따른 분포 차이

![3](https://user-images.githubusercontent.com/41605276/51813189-68faa000-22f8-11e9-964e-6864349d6d1e.png)

### 동물의 종류에 따른 분포 차이
- Danger만 따로 떼서 보면 고양이가 위험군일 가능성이 더 높다.

![4](https://user-images.githubusercontent.com/41605276/51813200-71eb7180-22f8-11e9-9340-0490beca40fa.png)

![5](https://user-images.githubusercontent.com/41605276/51813205-7a43ac80-22f8-11e9-9aad-670b73462c42.png)

### intake당시의 Type에 대한 분포 차이
- 주인이 안락사를 요구하면 당연히 위험군에 속할 가능성이 높아진다

![6](https://user-images.githubusercontent.com/41605276/51813212-82035100-22f8-11e9-82e8-3954b812a02e.png)

### 순종과 혼종(MIx)에 따른 분포차이
- Mix견일 수록 위험군에 속할 가능성이 높다

![7](https://user-images.githubusercontent.com/41605276/51813286-d7d7f900-22f8-11e9-81d2-cae9b8ad4fa6.png)

![8](https://user-images.githubusercontent.com/41605276/51813295-deff0700-22f8-11e9-968e-edfabc229a7f.png)

### Intake당시 Condtion에 따른 분포 차이
- 나이가 들었을 수록(Aged), 다쳤을 수록(Injured), 아플수록 위험성이 높았고(sick),
- 야생일수록(Feral), 보호를 받을수록(Nursing), 임신을 했을수록(Pregnant) 더 안전한 것으로 나타났다.

![9](https://user-images.githubusercontent.com/41605276/51813304-e6beab80-22f8-11e9-9a6d-325ec76c4f56.png)

### Mix견 세분화
- 순종과 2가지 혼종은 유의한 차이가 보이지 않았지만, 잡종은 위험도가 유의하게 높은 것으로 나타났다.

![10](https://user-images.githubusercontent.com/41605276/51813315-f0e0aa00-22f8-11e9-9ab3-e268c680362c.png)

![11](https://user-images.githubusercontent.com/41605276/51813317-f76f2180-22f8-11e9-96ae-269139ba62fa.png)

### 정리
- 위험군이라고 판단되는 Aged, injured, sick 이외에도, 이름의 유무, 순종인지 혼종인지 등이 유의미한 것으로 확인 
- 강아지인지 고양이인지에 따라서도 위험군의 차이가 유의하게 나타나므로, 이름이 없고, 고양이고, 잡종인 동물에 대해서 조금 더 섬세한 보호,관리가 필요하다고 판단

### 2.4.5 Test 데이터 분리
- 전체 데이터 중 30%를 OutcomeType의 비율을 유지하여 Test 데이터로 분리

### 2.4.6 데이터 전처리

#### Name

- 이름이 있으면 1, 없으면 0
- Popular Name 칼럼 추가
    - 인기있는 개/고양이 이름 크롤링
    - 그 이름들에 해당하면 1, 아니면 0

#### Animal_type

- 'Dog'는 0, 'Cat'은 1 로 변환

#### Breed
- Populr Breed 칼럼 생성
    - 인기있는 dog, cat breed 크롤링
    - 인기있는 종에 해당하면 1, 아니면 0

- 순종(pure), 2개종 mix(two_hybrid), 여러 종 mix(multi_hybrid)로 구분
    - `Cavalier Span`: pure
    - `Beagle/Laborador Retriever` : two_hybrid
    - `Domestic Shorhair Mix`: multi_hybrid



#### AgeuponIntake
- day로 단위를 통일해 연속형 numerical 데이터로 변환

#### DateTime
- str이었던 날짜를 datetime으로 형변환 후 {연, 월, 일, 요일, 시, 분} 데이터 추출

#### SexuponIntake
{'Intact Male', 'Intact Female', 'Neutered Male', 'Spayed Female', 'Unknown', NaN}
- NaN값은 "Unknown"으로 처리
- 성별을 구분하는 칼럼과 중성화 여부를 구분하는 칼럼으로 분리
- {'Male', 'Female', 'SexUnknown'}, {'Intact', 'Neutered', 'IntactUnknown'}

#### Color
- 순수 색상이 아닌 패턴을 의미하는 단어들만 추출
    - {"Agouti", "Brindle", "Calico", "Merle", "Point", "Smoke", "Tabby", "Tick", "Tiger", "Torbie", "Tortie", "Tricolor"}
- 이 패턴들을 갖고 있는 데이터들을 BoW로 벡터라이징

#### IntakeCondition
{'Aged', 'Feral', 'Injured', 'Normal', 'Nursing', 'Other', 'Pregnant', 'Sick'} 
- 원핫인코딩

#### IntakeType
{'Euthanasia Request', 'Owner Surrender', 'Public Assist', 'Stray'} 
- 원핫인코딩

### 2.4.7 Classification(Train)
#### Model
- `n_estimators` : 200
- `max_depth` : 5
- `learning_rate` : 0.05


```python
gbm = lightgbm.LGBMClassifier(n_estimators = 200, max_depth=5, learning_rate = 0.05, random_state=0)
gbm = gbm.fit(df_train.values, df_target.values)
```


```python
confusion_matrix(df_target.values, gbm.predict(df_train.values))
```




    array([[  519,   684],
           [   69, 16974]], dtype=int64)




```python
print(classification_report(df_target.values, gbm.predict(df_train.values), 
                            target_names=le_target.classes_))
```

                  precision    recall  f1-score   support
    
          Danger       0.88      0.43      0.58      1203
            Safe       0.96      1.00      0.98     17043
    
       micro avg       0.96      0.96      0.96     18246
       macro avg       0.92      0.71      0.78     18246
    weighted avg       0.96      0.96      0.95     18246
    
    


```python
recall_score(df_target.values, gbm.predict(df_train.values), average='macro')
```




    0.7136864316940442



## 2.5 솔루션 5
- 동물센터에서 중요한 의사결정과정 중 하나가 추후에 실제로 죽거나, 안락사(Danger)되는 동물들을 최대한 많이 선별하는 것
- 이를 위해 Danger Recall 비율을 높여야 한다고 판단
- Imbalancing 기법을 사용하여 Danger Recall 향상
- **최종 모델**은 **Random UnderSampler**를 사용하여 **Imbalancing 처리**한 모델을 사용

#### 1) Instance Hardness Threshold

##### Train


```python
confusion_matrix(df_target.values, pred)
```




    array([[  990,   213],
           [ 3984, 13059]], dtype=int64)




```python
print(classification_report(df_target.values, pred))
```

                  precision    recall  f1-score   support
    
               0       0.20      0.82      0.32      1203
               1       0.98      0.77      0.86     17043
    
       micro avg       0.77      0.77      0.77     18246
       macro avg       0.59      0.79      0.59     18246
    weighted avg       0.93      0.77      0.83     18246
    
    


```python
recall_score(df_target.values, pred, average='macro')
```




    0.7945904908561197



##### Train and Test Split


```python
confusion_matrix(y_test, pred)
```




    array([[ 269,   92],
           [1236, 3877]], dtype=int64)




```python
print(classification_report(y_test, pred))
```

                  precision    recall  f1-score   support
    
               0       0.18      0.75      0.29       361
               1       0.98      0.76      0.85      5113
    
       micro avg       0.76      0.76      0.76      5474
       macro avg       0.58      0.75      0.57      5474
    weighted avg       0.92      0.76      0.82      5474
    
    


```python
recall_score(y_test, gbm.predict(X_test), average='macro')
```




    0.751707802554241



##### K-Fold(5)
- 0.74216

#### 2) Edited Nearest Neighbours

##### Train


```python
confusion_matrix(df_target.values, pred)
```




    array([[  538,   665],
           [  109, 16934]], dtype=int64)




```python
print(classification_report(df_target.values, pred))
```

                  precision    recall  f1-score   support
    
               0       0.83      0.45      0.58      1203
               1       0.96      0.99      0.98     17043
    
       micro avg       0.96      0.96      0.96     18246
       macro avg       0.90      0.72      0.78     18246
    weighted avg       0.95      0.96      0.95     18246
    
    


```python
recall_score(df_target.values, pred, average='macro')
```




    0.7204098537321544



#### 3) Random Undersampler

##### Train


```python
confusion_matrix(df_target.values, pred)
```




    array([[  945,   258],
           [ 3101, 13942]], dtype=int64)




```python
print(classification_report(df_target.values, pred))
```

                  precision    recall  f1-score   support
    
               0       0.23      0.79      0.36      1203
               1       0.98      0.82      0.89     17043
    
       micro avg       0.82      0.82      0.82     18246
       macro avg       0.61      0.80      0.63     18246
    weighted avg       0.93      0.82      0.86     18246
    
    


```python
recall_score(df_target.values, pred, average='macro')
```




    0.8017923126233586



##### Train and Test Split


```python
confusion_matrix(y_test, pred)
```




    array([[ 240,  121],
           [ 982, 4131]], dtype=int64)




```python
print(classification_report(y_test, pred))
```

                  precision    recall  f1-score   support
    
               0       0.20      0.66      0.30       361
               1       0.97      0.81      0.88      5113
    
       micro avg       0.80      0.80      0.80      5474
       macro avg       0.58      0.74      0.59      5474
    weighted avg       0.92      0.80      0.84      5474
    
    


```python
recall_score(y_test, gbm.predict(X_test), average='macro')
```




    0.7363802441552222



##### K-Fold(5)
- 0.74439

#### 4) Random Oversampler

##### Train


```python
confusion_matrix(df_target.values, pred)
```




    array([[  945,   258],
           [ 1693, 15350]], dtype=int64)




```python
print(classification_report(df_target.values, pred))
```

                  precision    recall  f1-score   support
    
               0       0.36      0.79      0.49      1203
               1       0.98      0.90      0.94     17043
    
       micro avg       0.89      0.89      0.89     18246
       macro avg       0.67      0.84      0.72     18246
    weighted avg       0.94      0.89      0.91     18246
    
    


```python
recall_score(df_target.values, pred, average='macro')
```




    0.8430995942052397



##### Train and Test Split


```python
confusion_matrix(y_test, pred)
```




    array([[ 225,  136],
           [ 557, 4556]], dtype=int64)




```python
print(classification_report(y_test, pred))
```

                  precision    recall  f1-score   support
    
               0       0.29      0.62      0.39       361
               1       0.97      0.89      0.93      5113
    
       micro avg       0.87      0.87      0.87      5474
       macro avg       0.63      0.76      0.66      5474
    weighted avg       0.93      0.87      0.89      5474
    
    


```python
recall_score(y_test, gbm.predict(X_test), average='macro')
```




    0.7571653484437313



##### K-Fold(5)
- 0.7556

### Performance Test

#### 1) Instance Hardness Threshold


```python
confusion_matrix(y_test, IHT.predict(X_test))
```




    array([[ 396,  119],
           [1703, 5602]], dtype=int64)




```python
print(classification_report(y_test, IHT.predict(X_test), target_names=le_target.classes_))
```

                  precision    recall  f1-score   support
    
          Danger       0.19      0.77      0.30       515
            Safe       0.98      0.77      0.86      7305
    
       micro avg       0.77      0.77      0.77      7820
       macro avg       0.58      0.77      0.58      7820
    weighted avg       0.93      0.77      0.82      7820
    
    


```python
recall_score(y_test, gbm.predict(X_test), average='macro')
```




    0.7679020221553265



#### 2) Random Undersampler


```python
confusion_matrix(y_test, random_undersampler.predict(X_test))
```




    array([[ 379,  136],
           [1363, 5942]], dtype=int64)




```python
print(classification_report(y_test, random_undersampler.predict(X_test), target_names=le_target.classes_))
```

                  precision    recall  f1-score   support
    
          Danger       0.22      0.74      0.34       515
            Safe       0.98      0.81      0.89      7305
    
       micro avg       0.81      0.81      0.81      7820
       macro avg       0.60      0.77      0.61      7820
    weighted avg       0.93      0.81      0.85      7820
    
    


```python
recall_score(y_test, gbm.predict(X_test), average='macro')
```




    0.7746688994770173



#### 3) Random Oversampler


```python
confusion_matrix(y_test, random_oversampler.predict(X_test))
```




    array([[ 333,  182],
           [ 783, 6522]], dtype=int64)




```python
print(classification_report(y_test, random_oversampler.predict(X_test), target_names=le_target.classes_))
```

                  precision    recall  f1-score   support
    
          Danger       0.30      0.65      0.41       515
            Safe       0.97      0.89      0.93      7305
    
       micro avg       0.88      0.88      0.88      7820
       macro avg       0.64      0.77      0.67      7820
    weighted avg       0.93      0.88      0.90      7820
    
    


```python
recall_score(y_test, gbm.predict(X_test), average='macro')
```




    0.7697075417156756



### Final Model
- `n_estimators` : 200
- `max_depth` : 5
- `learning_rate` : 0.05
- `Random Undersampler`


```python
confusion_matrix(y_test, random_undersampler.predict(X_test))
```




    array([[ 379,  136],
           [1363, 5942]], dtype=int64)




```python
print(classification_report(y_test, random_undersampler.predict(X_test), target_names=le_target.classes_))
```

                  precision    recall  f1-score   support
    
          Danger       0.22      0.74      0.34       515
            Safe       0.98      0.81      0.89      7305
    
       micro avg       0.81      0.81      0.81      7820
       macro avg       0.60      0.77      0.61      7820
    weighted avg       0.93      0.81      0.85      7820
    
    


```python
recall_score(y_test, gbm.predict(X_test), average='macro')
```




    0.7746688994770173




```python
feature_imp = pd.DataFrame(list(zip(gbm.feature_importances_, df_train.columns)), columns=['Value','Feature'])

plt.figure(figsize=(8, 5))
sns.barplot(x="Value", y="Feature", data=feature_imp.sort_values(by="Value", ascending=False).iloc[:10])
plt.title('LightGBM Features (avg over folds)')
plt.tight_layout()
plt.show()
```


![classification_team_127_0](https://user-images.githubusercontent.com/41605276/51813340-1372c300-22f9-11e9-8b4d-936f63ab752d.png)


# 3. 결론
#### 유기동물 보호, 관리를 위해 실질적인 도움을 줄 수 있는 예측모델 구현
1) 추후에 실제 사망하거나 안락사 당하게 될 동물들을 최대한 예측
    - 위험군에 속한 동물들(515마리)의 약 74%(333마리) 예측
2) 비용(시간, 인력 등)을 고려하여 추후에 실제 안전군에 속하는 동물들에 대한 오분류(위험군) 최소화
    - 안전군에 속한 동물들(7305마리)의 약 19%(1363마리)를 위험군으로 오분류
