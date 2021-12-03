# Chapter06_Team Project
---
## Project1: Text Classification
### 영어 문장에 대한 긍정/부정 리뷰를 구분하는 text classification task
#### 목표: 제한된 자원(시간, GPU)내에서 효율적으로 실험을 하고 기록, 재사용 가능성이 높은 코드 스니펫을 확보하기
<br>

### Project Process
1. 모델 선정
2. Data EDA
3. Data Preprocessing
4. Model Training & HyperParameter Tuning
5. 자체 평가 및 보완 사항
<br>

### 1. 모델 선정
#### 모델 선정 기준
- hugging face에 존재하는 모델로, SequenceClassification이 있는 경우
- 비교적 최근에 나와 성능이 좋다고 평가 받는 경우
 
#### 사용 모델
- Bert
- RoBerta : 테스트 결과 가장 성능 좋았음
- Bart
- XLNet
<br>

### 2. Data EDA
#### Train Data
- Count: 443,256
  * Positive: 266,041(60%)
  * Negative: 177,218(40%)
- Duplication: 62,689

#### Validation Data
- Count: 4,000
  * Positive: 2,000(50%)
  * Negative: 2,000(50%)
- Duplication: 145

#### Test Data
- Count: 1,000
- Duplication: 0

#### 이외에도 전체 데이터셋의 길이 분포, Common Words 등을 확인
<br>

### 3. Data Preprocessing
#### 1. 축약어
- n't -> not
- 's -> is
- 'm -> am
- 'll -> will

#### 2. 중복 제거
- 데이터셋의 중복 제거

#### 3. 불용어 제거

#### 4. 특수문자 제거
- `?, !, $, )`등의 특수문자 제거
<br>

### 4. Model Training & HyperParameter Tuning
#### Bert
- batch size가 증가할 때 accuracy 증가
- weight decay 적용 시 accuracy 증가
- 둘 다 적용하여 실험한 결과 하나씩 적용하였을 때보다 증가하는 추세를 보이지는 않음

#### XLNet
- batch size가 증가할 때 accuracy 증가
- weight decay 적용 시 accuracy 증가

#### Bart
- Bart-base가 Bart-Large보다 좋은 성능을 보임
- Learning rate Scheduler 적용 시 accuracy 증가

#### RoBERTa(최종 선택 모델)
- batch size가 증가할 때 accuracy 증가
- RoBERTa-large가 RoBERTa-base보다 좋은 성능을 보임
- batch size를 256으로 적용했을 때 가장 높은 accuracy(0.991)를 보임
<br>

### 5. 자체 평가 및 보완 사항
#### 1. 다양한 모델 활용
- 다양한 모델을 적용해 보면서 각 모델의 특성에 대해 파악하고, 문제에 적합한 최적의 모델을 찾을 수 있었다.

#### 2. GPU 메모리 용량 제한
- GPU 메모리의 한계로 input data의 max_lenght를 늘리는데 한계가 있었고, batch size에도 제한이 있어 아쉬움

#### 3. HyperParameter Tuning
- Learning rate, epoch, regularization등의 파라미터 튜닝을 다양하게 진행하여 더욱 좋은 결과를 찾아낼 수 있을 것 같다.

#### 4. Esemble 적용
- 모델에 대해 Esemble을 적용하여 더욱 좋은 결과를 얻을 수 있을 것이다.
---
