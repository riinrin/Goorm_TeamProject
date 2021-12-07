# Chapter06_Team Project
---
## Project3: Neural Machine Translation
### 영어 -> 한국어 번역을 수행하는 NMT task
<br>

### Project Process
1. Data EDA
2. 모델 선정
3. Model Training & HyperParameter Tuning
4. 자체 평가 및 보완 사항
<br>

### 1. Data EDA
#### Train Data
| |sid|en|ko|
|:---:|:---:|:---:|:---:|
|`count`|150,000|150,000|150,000|
|`unique`|150,000|149,901|149,993|

#### Dev Data
| |sid|en|ko|
|:---:|:---:|:---:|:---:|
|`count`|10,000|10,000|10,000|
|`unique`|10,000|10,000|10,000|

#### Test1 Data
| |sid|en|
|:---:|:---:|:---:|
|`count`|10,000|10,000|
|`unique`|10,000|10,000|

#### Test2 Data
| |sid|en|
|:---:|:---:|:---:|
|`count`|10,000|10,000|
|`unique`|10,000|10,000|

<br>

### 2. 모델 선정
#### mT5
- 기존 T5(text-to-text transfer transformer)를 다국어 코퍼스로 확장함

<br>

### 3. Model Training & HyperParameter Tuning
#### parameter
- input max lenght: 60
- output max lenght: 60
- learning rate: 0.0005
- epoch: 10, 13(early stopping callback)
- batch size: 8(accumulate grad batches)

#### Post-processing
- Greedy Search: decision making을 early stage에서 하므로 초반에 틀리면 다 틀릴지도 모른다는 단점
- Exhaustive Search: 모든 가능성을 다 보기 때문에, 문장이 길어지거나 vocab size가 커지면 불가능함
- Beam Search: exhaustive search를 하되, 각 time step마다 가장 점수가 높은 top-k를 유지함

#### Predict-Ko Training
- 모델이 예측한 Predict와 ko의 어순, 어미의 미세한 차이를 조정하기 위해 재학습
- Train data의 모델이 예측한 Predict 값을 추가함
- Encoder에 Predict data, decoder에 ko data로 T5 모델 학습
- __성능이 좋아지지 않음__
    * train data의 전부를 predict data로 활용하지 못함
    
<br>

### 4. 자체 평가 및 보완 사항
#### 1. 다양한 모델을 활용하지 못함
- mT5 외의 다른 모델로 테스트 해보지 못하여 아쉬움

#### 2. HyperParameter Tuning
- 파라미터 튜닝을 다양하게 진행하지 못하여 아쉬움

#### 3. Back Translation 적용
- Back Translation을 적용한다면 더 좋은 성능을 낼 수 있을 것으로 생각되나, 시간상의 문제로 적용하지 못하여 아쉬움

#### 4. GPU 메모리 용량
- 이전 프로젝트에서는 batch size의 제약을 크게 받았으나, 이번 프로젝트에서는 accumulate grad batch의 사용을 통하여 적은 memory에서도 효율적인 학습이 가능하였음
---
