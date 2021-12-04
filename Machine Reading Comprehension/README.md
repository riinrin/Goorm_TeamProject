# Chapter06_Team Project
---
## Project2: Machine Reading Comprehension
### 한국어 지문을 보고 질문에 맞는 답을 생성하는 Korean MRC task
#### 예시
- 지문:
```
올여름 장마가 17일 제주도에서 시작됐다. 서울 등 중부지방은 예년보다 사나흘 정도 늦은 이달 말께 장마가 시작될 전망이다.
17일 기상청에 따르면 제주도 남쪽 먼바다에 있는 장마전선의 영향으로 이날 제주도 산간 및 내륙지역에 호우주의보가 내려지면서 곳곳에 100㎜에 육박하는 많은 비가 내렸다. 
제주의 장마는 평년보다 2~3일, 지난해보다는 하루 일찍 시작됐다. 
장마는 고온다습한 북태평양 기단과 한랭 습윤한 오호츠크해 기단이 만나 형성되는 장마전선에서 내리는 비를 뜻한다.
장마전선은 18일 제주도 먼 남쪽 해상으로 내려갔다가 20일께 다시 북상해 전남 남해안까지 영향을 줄 것으로 보인다. 
이에 따라 20~21일 남부지방에도 예년보다 사흘 정도 장마가 일찍 찾아올 전망이다. 
그러나 장마전선을 밀어올리는 북태평양 고기압 세력이 약해 서울 등 중부지방은 평년보다 사나흘가량 늦은 이달 말부터 장마가 시작될 것이라는 게 기상청의 설명이다. 
장마전선은 이후 한 달가량 한반도 중남부를 오르내리며 곳곳에 비를 뿌릴 전망이다. 
최근 30년간 평균치에 따르면 중부지방의 장마 시작일은 6월24~25일이었으며 장마기간은 32일, 강수일수는 17.2일이었다.
기상청은 올해 장마기간의 평균 강수량이 350~400㎜로 평년과 비슷하거나 적을 것으로 내다봤다.
브라질 월드컵 한국과 러시아의 경기가 열리는 18일 오전 서울은 대체로 구름이 많이 끼지만 비는 오지 않을 것으로 예상돼 거리 응원에는 지장이 없을 전망이다.
```

- 질문:
```북태평양 기단과 오호츠크해 기단이 만나 국내에 머무르는 기간은?```
- 정답:
  * 478번째 글자부터 ```한 달 가량```
  * 478번째 글자부터 ```한 달```

정답은 항상 지문 안에 문자 그대로 존재하며, 학습 자료에서는 해당 정답이 시작되는 글자 위치를 제공한다.  
위의 예시와 같이 답은 여러 개 존재할 수 있으며, 어느 것을 학습에 사용하든 무방하다.
<br>

#### 평가는 편집거리(Levenshtein Distance)를 사용
<img src="https://user-images.githubusercontent.com/90172461/144612181-1b566308-74d3-4f58-8cd0-5f78771a3bad.gif"  width="740" height="370">
<img src="https://user-images.githubusercontent.com/90172461/144612203-947cc894-96e6-4a59-b49a-f70800dc58b1.PNG">
<br>

### Project Process
1. Data EDA
2. Model analysis
 2-1. Extraction-based Model
 2-2. Generation-based Model
3. Model Selection
4. 앙상블 적용
5. 자체 평가 및 보완 사항
6. feedback
<br>

### 1. Data EDA
#### Train Data
| |guid|title|news</br>category|context|question|text|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|`count`|12,037|12,037|8,494|12,037|12,037|12,037|
|`unique`|12,037|9,784|33|10,434|12,037|9,592|

#### Test Data
| |guid|title|news</br>category|context|question|text|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|`count`|4,008|4,008|8494|4,008|4,008|4,008|
|`unique`|4,008|3,709|32|10,434|3,841|4,008|

#### 이외에도 전체 데이터셋의 길이 분포, 결측값 등을 확인
<br>

### 2. Model analysis
#### 2-1. Extraction-based Model
<img src="https://user-images.githubusercontent.com/90172461/144711336-e73a89c0-e94e-4799-bfc3-57244e845fe2.png" width="800" height="400">

- 문제 유형: 지문(context) 내의 답의 `위치`를 예측
  * 각 토큰이 정답인지 아닌지를 판별하는 `분류 문제`
- Loss 계산을 위한 답(Prediction)의 형태
  * 지문 내 `답의 위치`

- Post-processing
  - 불가능한 답 제거
      * End position이 Start Position보다 앞에 있는 경우
      * 예측한 위치가 context를 벗어난 경우
      * 미리 설정한 max_answer_length보다 길이가 더 긴 경우
  - 최적의 답안 찾기
      * Start/End Position prediction에서 score가 가장 높은 N개를 각각 찾음
      * 불가능한 Start/End 조합을 제거함
      * 가능한 조합들을 score의 합이 큰 순서대로 정렬함
      * score가 가장 큰 조합을 최종 prediction으로 선정함


#### 2-2. Generation-based Model
<img src="https://user-images.githubusercontent.com/90172461/144711338-f3b67abe-f671-4bd0-a7c2-b6da905e48ec.png" width="800" height="400">

- 문제 유형: 주어진 지문과 질의를 보고 답변 생성
  * 지문 내에 정답이 없어도 답을 생성하는 `생성 문제`
- Loss 계산을 위한 답(Prediction)의 형태
  * `Free-form text` 형태

- Post-processing
  - Greedy Search
      * decision making을 early stage에서 하므로, 초반에 틀리면 모두 틀릴지도 모른다는 단점
  - Exhaustive Search
      * 모든 가능성을 다 살펴보므로 가능한 경우의 수가 time step에 비례함
      * 문장이 비교적 길어지거나, vocab size가 비교적 커지는 경우에 불가능함
  - Beam Search(해당 방식 채택)
      * exhaustive search를 하되, 각 time step마다 가장 점수가 높은 top-k만 유지함
      * 가장 많이 사용하는 방식이다

<br>

### 3. Model Selection
- __Bert__
  * Bert보다 한국어 기반으로 제작된 KoBert가 더 좋은 성능을 보임
  * Train 데이터의 answer 평균 길이가 7~8 정도임을 감안하여 Prediction의 max_length를 제한하자 성능이 향상됨
  * Pretrained model의 input max_length가 512로 제한되어 있어, 주어진 긴 시퀀스의 답을 예측하는데에 한계가 있음

- __T5(최종 선정)__
  * batch_size의 크기를 키울수록 성능이 좋아짐
  * Post processing을 통하여 성능을 향상시킴
  * Num_beam이 5, max_length가 20일 때 가장 좋은 성능을 보임
  * AI_hub의 데이터를 25만개 추가하자 성능이 오히려 떨어짐

- __BigBird(최종 선정)__
  * AI_hub의 데이터를 3만개 추가한 버전에서 가장 좋은 성능을 보임
  * BigBird 모델이 주어진 데이터의 긴 문장을 가장 잘 파악하는 것으로 보임

<br>

### 4. 앙상블 적용
- 최종적으로 선택한 모델인 T5와 BigBird 모델을 앙상블 적용
- 도출한 Prediction을 가지고 hard voting 방식으로 적용
- 각각 모델의 답이 전부 다른 경우, BigBird 모델의 결과를 최종 제출 답안으로 선택

<br>

### 5. 자체 평가 및 보완 사항
1. 다양한 모델 활용
    - 다양한 모델을 문제에 적용해 보면서 각 모델의 특성에 대해 파악하고, 최적의 모델을 찾는 경험을 하였음
2. Esemble 적용
    - 2가지 모델을 가지고 앙상블을 진행하여 성능을 개선할 수 있었음
    - 더 다양한 모델을 가지고 앙상블을 적용할 경우, 성능 향상을 기대할 수 있음
3. GPU 메모리 용량 제한
    - GPU 메모리 용량의 한계로 인해, input data의 max_length의 길이를 늘리는데에 한계가 있음
    - batch size의 크기를 늘리는 점에도 한계가 있음
4. Hyper parameter Tuning
    - Learning rate, epoch, Regularization 등의 다양한 파라미터 튜닝을 진행하지 못했다는 아쉬움이 있음

<br>

### 6. feedback
- AI-hub의 데이터를 추가로 학습
    * 현재 데이터셋의 경우 AI-hub의 데이터셋보다 작기 때문에 AI-hub의 데이터셋으로 먼저 fine-tuning하여 성능 향상
    * 현재 데이터셋의 경우 'isImpossible'이라는 태그가 없지만, AI-hub의 데이터셋에는 존재하므로 제거가 필요함
- 데이터 전처리
    * '은', '는', '이', '가' 등의 불용어 전처리
- 데이터셋을 잘라서 학습
    * 질문과 답이 근처에 있다는 점을 이용하여 질문과 답에서 거리가 먼 앞뒤의 문장들을 제거한 후 학습
- Batch Size를 늘려 학습
    * GPU의 한계가 발생하여도, batch accumulation을 사용하여 gradient를 쌓아서 마치 batch size가 큰 것처럼 학습
- 편집거리의 이점 사용
    * 평가 방식인 `편집거리`의 경우, 답을 `Blank`로 처리하는 것이 오히려 성능이 좋은 경우가 발생할 수 있음
---
