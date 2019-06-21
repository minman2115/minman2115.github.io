---
layout: post
title: "차원의 저주 문제 2-2"
tags: [데이터전처리]
comments: true
---

Data_Preprocessing_Studynotes_(20190612)

study program : https://www.fastcampus.co.kr/data_camp_ppc


#### [학습목표]


- 차원의 저주 문제 및 해결방법 이해


#### [학습기록]

#### 1. 유전 알고리즘 파이썬 코드 구현 예시


```python
df = pd.read_csv("아마존_리뷰_이진_분류_데이터.csv", engine = "python")
df.tail(5)
```

![1](https://user-images.githubusercontent.com/41605276/59923046-592d5500-946d-11e9-8ee3-6b9a037c0b27.png)



```python
# 특징 선택 - 래퍼 방법 (유전 알고리즘)
import numpy as np
def generating_initial_generation(number_of_solutions, number_of_features):
    return np.random.choice([True, False], (number_of_solutions, number_of_features))

## step0) 유전알고리즘 구현을 위한 함수 정의

from sklearn.model_selection import cross_val_score
def solution_evaulation(X, Y, generation, model, metric):
    score_list = []
    for solution in generation:
        score = cross_val_score(model, X.iloc[:, solution], Y, cv=5, scoring = metric).mean() 
        score_list.append(score)
    return score_list

def top_k_solution_selection(solutions, score_list, k):
    score_list = np.array(score_list)
    top_k_index = (-score_list).argsort()[:k]
    selected_solutions = solutions[top_k_index]
    return selected_solutions

def one_point_crossover(solution1, solution2):
    sol_length = len(solution1)
    point = np.random.choice(range(1, sol_length - 1))
    new_solution = list(solution1[:point]) + list(solution2[point:])
    return (np.array(new_solution))

def flip_bit_muation(solution, prob):
    for i in range(len(solution)):
        random_number = np.random.random()
        if random_number <= prob:
            solution[i] = 1 - solution[i]
    return solution

X = df.drop('Label', axis = 1)
Y = df['Label']

## Step1) best_score 을 0 으로 best feature set 을 빈 리스트로 초기화

best_score = 0.00
best_feature_set = []

## Step2) generating_initial_generation 함수를 사용하여 𝑛개의 초기해를 생성하고 이를 현재 세대에 저장
## n = 10 (보통 n = 100정도로 설정하지만, 실습용으로 10으로 설정한 것)

n = 10
current_generation = generating_initial_generation(number_of_solutions = n, number_of_features = len(X.columns))

## Step3) solution_evaluation 함수를 사용하여 현재 세대에 있는 모든 해를 평가하고 평가 값을 저장 
## 이 가운데 가장 우수한 평가 점수가 best score 보다 크면 이 점수를 best score 로 업데이트하고 이 해를 best feature set 으로 업데이트

for iter_num in range(1, 11): # 유전 알고리즘을 10회 수행
    print(iter_num, "번째 이터레이션")
    
    ## Step4) top_k_solution_selection 함수를 사용하여 𝑘<𝑛개의 해를 선택하고 이를 미래 세대로 저장
    
    evaluation_result = solution_evaulation(X, Y, current_generation, model = BernoulliNB(), metric = 'f1')
    current_best_score = max(evaluation_result) # 현재 세대의 최고 성능 저장
    if current_best_score > best_score:
        best_score = current_best_score
        best_score_index = np.where(evaluation_result == best_score)
        best_feature_set = current_generation[best_score_index]
    
    ## Step5) 미래 세대에 있는 해 가운데 2 개의 해를 임의로 선택하여 자식 해를 미래 세대에 추가하는 과정을 𝑛−𝑘번 반복
    ## 상위 k개 해를 선택 (k = 5) 및 미래 세대에 추가
    
    k = 5
    selected_solutions = top_k_solution_selection(current_generation, evaluation_result, k)
    future_generation = selected_solutions
    
    for i in range(n - k): # n-k번을 반복하여 해를 생성
        p1 = np.random.randint(len(future_generation))
        p2 = np.random.randint(len(future_generation))    
        parent_solution_1 = future_generation[p1] # future generation에서 하나를 선택
        parent_solution_2 = future_generation[p2] # future generation에서 하나를 선택
        child_solution = one_point_crossover(parent_solution_1, parent_solution_2)
        future_generation = np.vstack((future_generation, child_solution)) # child solution을 future generation에 추가
        
    ## Step6) 미래 세대에 있는 해 가운데 𝑝%의 해에 돌연변이 연산을 적용하고 현재의 미래 세대를 현재 세대로 대체
    ## Step7) 종료 조건을 만족하면 알고리즘을 종료하고 그렇지 않으면 Step 2 로 돌아감
    
    for s in range(len(future_generation)):
        random_number = np.random.random()
        if random_number <= 0.2: # 20%의 해에 대해 돌연변이 연산을 적용
            future_generation[s] = flip_bit_muation(future_generation[s], prob = 0.1) # 요소 돌연변이 비율 0.1

print(best_feature_set, best_score)
```

    1 번째 이터레이션
    2 번째 이터레이션
    3 번째 이터레이션
    4 번째 이터레이션
    5 번째 이터레이션
    6 번째 이터레이션
    7 번째 이터레이션
    8 번째 이터레이션
    9 번째 이터레이션
    10 번째 이터레이션
    [[ True False False ... False False  True]] 0.7020930972056061
    

#### 2. PCA(주성분 분석)

1) 기본 아이디어 : 유사한 변수들의 집합을 하나의 벡터로 표현하자는 의도

![1](https://user-images.githubusercontent.com/41605276/59922943-03f14380-946d-11e9-8819-e6e9d1c64087.png)

2) 수학적 표현 및 수리 모델화

![2](https://user-images.githubusercontent.com/41605276/59922961-123f5f80-946d-11e9-8702-5a7af070895c.png)

3) 고유값과 고유벡터

![3](https://user-images.githubusercontent.com/41605276/59922987-1ec3b800-946d-11e9-820d-a896d48f1a8a.png)

4) PCA의 핵심개념

![4](https://user-images.githubusercontent.com/41605276/59922990-24b99900-946d-11e9-8e57-35f7b611ccbd.png)

5) PCA 파이썬 코드 구현 예시


```python
from sklearn.decomposition import PCA

df = pd.read_csv("아마존_리뷰_이진_분류_데이터.csv", engine = "python")
X = df.drop('Label', axis = 1)
Y = df['Label']

## n_components : 사용할 특징 개수
pca = PCA(n_components = 100)

new_X = pca.fit_transform(X)

print(len(X.columns))
print(len(new_X[0]))
print(pca.explained_variance_ratio_)

## 새로만든 변수들이 원 데이터의 분산을 설명하는 정도
print(sum(pca.explained_variance_ratio_))
```

    12532
    100
    [0.03264401 0.02632513 0.0203139  0.01701816 0.01595556 0.01536655
     0.01467607 0.01258312 0.01129611 0.01064863 0.00984115 0.00952442
     0.00895174 0.00858338 0.00847938 0.00833077 0.00789973 0.0066249
     0.00635942 0.00610172 0.00583657 0.00574372 0.00533351 0.00512803
     0.00505926 0.00490016 0.00472989 0.00470961 0.00456409 0.00451862
     0.00443722 0.00428358 0.004215   0.00419218 0.00404997 0.00398588
     0.00393346 0.00387123 0.00384925 0.00376112 0.00372318 0.00364764
     0.00361007 0.00351825 0.00347557 0.00341406 0.00335662 0.0033257
     0.00327761 0.00326467 0.00323149 0.00316545 0.00314633 0.0031145
     0.0030746  0.0030679  0.00303232 0.00294948 0.00293751 0.00288271
     0.00285587 0.00283748 0.00278162 0.00275063 0.00273317 0.00271245
     0.00264011 0.00262254 0.00261482 0.00258249 0.00254429 0.00251847
     0.00248897 0.00247401 0.0023917  0.00234562 0.00232569 0.0022759
     0.00226585 0.00218257 0.00216837 0.00214495 0.0021356  0.00207759
     0.00206533 0.00203183 0.00200933 0.00198167 0.00196711 0.00193518
     0.00191421 0.00190508 0.00186682 0.00183759 0.00182948 0.00179734
     0.00177325 0.00174839 0.00174001 0.00172717]
    0.5034424217740948
    

#### 3. 현실적인 모델구현 과정

![5](https://user-images.githubusercontent.com/41605276/59922994-2b481080-946d-11e9-8a76-a6550cd728b8.jpg)

