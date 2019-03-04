---
layout: post
title: "클러스터링 개괄적인 기초이론 요약"
tags: [머신러닝]
comments: true
---

.


#### # '패스트캠퍼스'에서 공부한 내용을 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr

- 참고자료 출처 : https://datascienceschool.net

### # 클러스터링 개괄적인 기초이론


- 클러스터링은 비지도학습이다. 비지도학습은 정답이라는 라벨이 없는 그런 문제다. 정답이 없는 여러가지 이유가 있는데 클러스터링에서는 우리가 풀려고 하는 문제 자체가 데이터가 주어지면 데이터들을 비슷한 데이터 비슷하지 않는 데이터로 묶어버리는 문제기 때문이다. 


- 클러스터링 문제가 성립하려면 가정이 하나 존재해야 한다. 그 가정은 데이터가 어떤 근처의 원래 클러스터를 이루고 있다. 원래 어떤 데이터들은 근처에 뭉쳐있고 데이터가 없는 부분이 존재해야 한다. 다시말해 데이터가 아무렇게나 존재하는게 아니라 어떤 특정 위치에만 있거나 특정형태로 존재하는 상황이 되야한다.


- 이렇게 되었을때 데이터들이 어떤식으로 뭉쳐져 있는 것을 찾아내는 문제가 클러스터링이다. 클러스터링이 기존의 분류문제와 다른점은 정답이라는게 없다보니까 점수를 매기기가 쉽지 않다. 


- 사람들이 그래서 클러스터링에서 점수를 매기는 방법을 만들었다. 이게 명확한 기준이 없다보니까 상당히 많은 점수들이 있는데 그러다 보니까 우리가 풀고자 하는 최적의 함수 자체가 사람마다 다 달랐다. 쉽게 말해 여러 방법들이 난립하게 되었다.


- 분류문제에서는 똑같은 목적함수를 푸는데 단지 푸는 방식이 달랐지만 클러스터링은 풀고자하는 목적함수 자체가 아예 다르다. 이런게 차이가 있다.


- 풀고자 하는 문제 자체가 다 다르기 때문에 푸는 방법의 특성도 다 다르다. 그래서 데이터의 특성에 따라서 성능도 다르게 된다. 


- 그래서 클러스터링은 어떤게 가장 좋다라는게 있는게 아니라 문제의 특성에 따라서 잘되는 것을 고르면 된다.

- 여러가지 클러스터링 방법이 있는데 푸는 방법도 다르고 특성도 다 다르다.


- 클러스터링 평가기준은 대표적으로 Adjusted Rand Index, Adjusted Mutual Information, Silhouette Coefficient 등이 있다.


- Adjusted Rand Index를 알아보기 전에 Incidence Matrix를 알아야 한다. 데이터가 만약에 0,1,2,3,4 5개가 있고 0,1,2와 3,4가 같은 클러스터라고 치자. 그러면 정답의 형태를 N X N 형태의 메트릭스 형태로 정의한다. 아래와 같다.


```python
groundtruth = np.array([
    [1, 1, 1, 0, 0],
    [1, 1, 1, 0, 0],
    [1, 1, 1, 0, 0],
    [0, 0, 0, 1, 1],
    [0, 0, 0, 1, 1],
])
```

- 여기에 있는 i번째 행의 j번째 열에서 생기는 숫자는 i번째 데이터와 j번째 데이터가 같은 클러스터인가 다른 클러스터인가를 나타낸다. 그래서 같은 클러스터에 속하면 1 아니면 0으로 표기한다.


- 데이터가 만약에 100개가 있으면 100 x 100 메트릭스가 된다.


- 그러면 대각선은 무조건 1이 된다. 그리고 i번째 데이터와 j번째 데이터가 같은 클러스터에 있으면 j번째와 i번째도 같다. 


- 만약에 기계가 클러스터링을 0,1 과 2,3,4가 같은 클러스터라고 prediction을 했다면 clustering 결과는 다음과 같다.


```python
clustering = np.array([
    [1, 1, 0, 0, 0],
    [1, 1, 0, 0, 0],
    [0, 0, 1, 1, 1],
    [0, 0, 1, 1, 1],
    [0, 0, 1, 1, 1],
])
```

- 이 두 행렬의 모든 원소에 대해 값이 같으면 1 다르면 0으로 계산한 행렬을 incidence matrix라고 한다. 즉 데이터 집합에서 만들수 있는 모든 데이터 쌍에 대해 정답과 클러스터링 결과에서 동일한 값을 나타내면 1, 다르면 0이 된다.

$$\ R_{ij} = 
\begin{cases} 
1 & \text{ if } T_{ij} = C_{ij} \\
0 & \text{ if } T_{ij} \neq C_{ij} \\
\end{cases} $$

다시말해 원래 정답에서 1번 데이터와 2번 데이터가 다른 클러스터인데 클러스터링 결과에서도 다른 클러스터라고 하면  R12=0 이다.

- 그래서 사람이 라벨링한 정답(groundtruth)와 clustering 결과 이 두개의 메트릭스를 겹쳤을때 두개가 일관되게 같은 경우 점수가 올라가게된다.


- 따라서 위의 예시에서  incidence matrix를 구하면 다음과 같다.


```python
incidence = 1 * (groundtruth == clustering)
incidence
```




    array([[1, 1, 0, 1, 1],
           [1, 1, 0, 1, 1],
           [0, 0, 1, 0, 0],
           [1, 1, 0, 1, 1],
           [1, 1, 0, 1, 1]])



- 이 행렬을 쭉 늘리면 결국 25개의 이진분류 문제가 되는 것이고 이것의 정확도를 rand inex라고 한다. 계산법은 아래와 같다.


```python
rand_index = np.sum(incidence) / np.prod(incidence.shape)
rand_index
```




    0.68



- 이 rand index에는 큰 문제점이 있다. 기본적으로 점수가 상당히 높게 나오는 편이다. 왜냐하면 일단 incidence matrix의 대각선은 무조건 맞추고 들어가기 때문이다.


- 그다음에 이게 symetric 한 매트릭스이기 때문에 또 반대편의 데이터도 동시에 맞출 수 있다. 아무리 난잡한 데이터여도 이 rand index는 반은 기본적으로 맞춘다.


- 그래서 rand index에서 나온 결과를 분포로 그려보면 원래는 0 ~ 1까지 나오는게 정상이지만 실제로 해보면 아무렇게나 해도 어느정도 기댓값 이상이 나온다.


- 그래서 나온 해결책이 scaling을 하는 것이다. 기댓값을 빼버리는 스케일링을 한다는 말이다. 다시말해 기대값과 최대값 사이가 1이 되게 스케일링 해주면 adjusted Rand index가 된다.


- adjusted Rand index를 쓰게 되면 주사위를 던져서 아무렇게나 클러스터링을 하게 되면 0근처에 값이 나올 수도 있고 음수가 나올 수도 있다. 기본적으로는 0근처에 나온다. 그리고 성능이 가장 좋은 것은 1이 된다.


- adjusted Rand index를 계산하려면 contingency table 이라는 것을 만들어야 한다. 정답과 클러스터링 결과에서 각각 같은 클러스터에 속하는 데이터의 갯수를 나타낸 것이다.


- 정답은 $$\ T=\{T_1, T_2,\ldots, T_r\} $$ 인데 r개의 집합의 집합이다.T1이라고 하는 집합은 내가 손으로 라벨링한 ground truth를 봤을때 1번 클러스터에 속하는 집합을 얘기하는 것이다. 기계가 클러스터링 한 결과는 $$\ C=\{C_1, C_2,\ldots, C_s\} $$라고 표기한다. 만약에 클러스터 갯수가 정해져 있지 않다면 T집합은 s개가 될 수도 있다.


$$\ \begin{array}{c|cccc|c}
T \; \backslash \; C &
C_1&
C_2&
\ldots&
C_s&
\text{소계}
\\
\hline
T_1&
n_{11}&
n_{12}&
\ldots&
n_{1s}&
a_1
\\
T_2&
n_{21}&
n_{22}&
\ldots&
n_{2s}&
a_2
\\
\vdots&
\vdots&
\vdots&
\ddots&
\vdots&
\vdots
\\
T_r&
n_{r1}&
n_{r2}&
\ldots&
n_{rs}&
a_r
\\
\hline
\text{소계}&
b_1&
b_2&
\ldots&
b_s&
\end{array} $$

nij는 정답에서는 클러스터  Ti에 속하고 클러스터링 결과에서는 클러스터  Cj에 속하는 데이터의 수를 말한다.

- 결론적으로 contingency table는 confusion matrix와 유사하다. 여기서 중요한것은 confusion matrix와 유사하지만 그거랑 똑같이 점수를 매기면 안된다. 라벨넘버가 달라져도 정답을 매길 수 있기 때문이다. 기계가 1번이라고 판정한 것이 내가 손으로 라벨링 한것이 2번일 수도 있기 때문이다. 그래서 confusion matrix 처럼 점수를 매기는 것이 아니라 행이랑 열로만든 소계값들을 이용해 랜드인덱스를 계산한다.

- 위에까지는 손으로 라벨링 할 수 있는 케이스이고, 손으로 라벨링 할 수 없는 경우가 현실적으로 가장 많다.


- 그다음에 많이 쓰는 방법이 Adjusted Mutual Information이다. 두 확률변수간의 상호 의존성을 측정한 값이다. 클러스터링 결과를 이산확률변수라고 가정한다.


- 정답은 $$\ T = \{T_1, T_2,\ldots, T_r\} $$라고 하고 클러스터링 결과는 $$\ C = \{C_1, C_2,\ldots, C_s\} $$이라고 하면 


- 전체 데이터의 갯수를  N 이라고하면 

$$\ P(i)=\frac{/T_i/}{N} $$와 

$$\ P'(j)=\frac{/C_i/}{N} $$를 

추정하여 상관관계를 계산한다. 

t와 c의 결합확률분포는 $$\ P(i,j)=\frac{/T_i \cap C_j/}{N} $$라고 추정한다. 

그 다음에 p하고 p프라임간의 $$\ MI(T,C)=\sum_{i=1}^r \sum_{j=1}^s P(i,j)\log \frac{P(i,j)}{P(i)P'(j)} $$와 같이 

계산하여 상관관계가 가장 높을때가 가장 좋은 성능이라고 할 수 있다.


- 완벽하게 서로 독립일경우에는 0이 되고, 완벽하게 상관관계가 있으면 1이된다. mutual information도 마찬가지로 adjust가 필요하다. 클러스터 개수에 따라서 아예 기본점수가 달라지기 때문이다. 클러스터를 2개로 하던 10개로 하던 비교하기 위해서는 기본점수가 같아야 한다. 그래서 adjusted Rand index를 계산하는 것처럼 기댓값을 빼주고 스케일링을 해주게 된다. 그렇게 되면 adjusted mutual information가 된다. adjusted Rand index 처럼 음수가 나오지는 않는다.

- 현실의 데이터는 차원이 너무 높기때문에 사람이 일일히 보면서 라벨링을 할 수 있는 수준이 아니다. 그래서 위의 지수들은 사실상 잘 안쓰인다.


- 이럴때 쓰는 것이 실루엣 계수이다. 데이터 하나하나에 대해 실루엣 계수를 계산할 수 있다. s = b-a / max(a,b) 수식으로 계산할 수 있는데 이는 데이터 하나에 대한 계산이다.

- 먼저 모든 데이터 쌍  i,j 에 대해 거리 혹은 비유사도을 구해서 이 결과를 이용하여 모든 데이터  i 에 대해 다음 값을 구한다.

ai = i 와 같은 클러스터에 속한 원소들의 평균 거리

bi = i 와 다른 클러스터 중 가장 가까운 클러스터까지의 평균 거리


- 잘된 클러스터면 a가 b보다 작은것이 정상적인 클러스터링이 이루어진것이다. b-a는 양수가 되는게 정상이다. 여기서도 노말라이징을 해줘야한다. a와 b중에서 큰걸로 나누어주면 1보다 큰 수가 나올 수 없게 된다. 이런식으로 모든데이터에 대해 실루엣 계수를 다 구한다. 다 구해서 평균을 내면 모든 데이터에 대한 실루엣 계수의 평균이 되고 이것을 클러스터링 점수로 쓰면 된다.

- 우리가 푸는 대부분의 클러스터링 문제는 정답을 정의하기가 힘들고 클러스터링이 잘되었냐 안되었냐 따지는 것도 힘들다. 그나마 우리는 하이퍼파라미터 최적화를 해야하고 다른 계수를 뭐를 써야할지 정해야하기 때문에 이를 가늠하기 위해 실루엣 계수를 쓴다.


- 예를 들어서 클러스터 갯수를 지정해줘야하는게 있으면 실루엣 계수가 좋게 나오는 갯수로 하면 되고 혹은 클러스터 넘버가 아닌 클러스터 넘버가 아닌 다른 하이퍼파라미터라면 역시 실루엣 계수를 활용하여 최적화하면 된다.


- 다만 데이터 하나하나에 대한 실루엣 계수를 구하다 보니 데이터가 많으면 많을 수록 연산량이 증가한다는 점은 단점이다.