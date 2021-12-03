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
