# 1_k-NearestNeighbor 개념/공부 요약

## 1. 이 노트북에서 한 일 (큰 흐름)
- iris.csv를 불러와 데이터 구조를 탐색함
- 사용할 피처를 꽃잎 길이(PetalLength), 꽃잎 너비(PetalWidth)로 제한함
- 타깃은 Species(setosa, versicolor, virginica)로 설정함
- 학습/테스트 데이터를 train_test_split으로 분리함
- StandardScaler로 표준화(스케일링) 수행
- KNeighborsClassifier로 다중분류 수행
- k 값(이웃 수)을 바꿔 정확도 비교
- predict, predict_proba로 예측 클래스와 확률 확인

## 2. 핵심 개념 정리

### 2-1. KNN(K-최근접 이웃) 분류
- 새로운 샘플이 들어오면, 학습 데이터 중에서 가까운 이웃 k개를 찾음
- 이웃들의 다수결로 클래스를 결정함
- 거리 기반 알고리즘이라 피처 스케일(단위)에 매우 민감함

### 2-2. 왜 표준화가 중요한가
- 길이/너비처럼 단위와 범위가 다른 피처가 섞이면 큰 값 범위를 가진 피처가 거리 계산을 지배함
- StandardScaler는 평균 0, 표준편차 1로 맞춰 거리 계산의 균형을 맞춤
- 중요한 점: fit은 train 데이터에만 하고, test는 transform만 해야 데이터 누수(leakage)를 막을 수 있음

### 2-3. 다중분류(Multiclass Classification)
- 이 노트북은 3개 클래스(setosa, versicolor, virginica)를 동시에 분류함
- classes_ 속성으로 모델이 인식한 클래스 순서를 확인할 수 있음
- predict_proba 결과의 열 순서는 classes_ 순서와 동일함

## 3. 코드 흐름 요약

### 3-1. 입력/타깃 분리
- 입력 X: [PetalLength, PetalWidth]
- 타깃 y: Species

### 3-2. 데이터 분할
- test_size=0.2, random_state=11
- 학습: 120개, 테스트: 30개

### 3-3. 스케일링
- ss.fit(train_input)
- train_scaled = ss.transform(train_input)
- test_scaled = ss.transform(test_input)

### 3-4. KNN 학습/평가
- 기본 예시: k=3
- 정확도(score)로 성능 확인

## 4. 실행 결과 핵심
- k=3 정확도: 0.9333333333333333
- k=1 정확도: 0.9
- k별 비교 결과
  - k=1: 0.9
  - k=3: 0.9333333333333333
  - k=5: 0.9333333333333333
  - k=7: 0.9333333333333333
  - k=9: 0.9333333333333333

해석
- 이 실험에서는 k=3 이상에서 성능이 안정적으로 0.9333 수준
- k=1은 이웃 한 개에만 의존해 노이즈에 더 민감할 수 있음

## 5. 예측 결과 읽는 법
- classes_: ['setosa', 'versicolor', 'virginica']
- predict_proba는 각 샘플에 대해 클래스별 확률을 반환
- 예: [0.0, 0.88888889, 0.11111111]이면 versicolor 가능성이 가장 높아 versicolor로 예측

## 6. 공부 포인트 
- KNN은 학습이 빠른 대신(사실상 저장), 예측 시 거리 계산 비용이 큼
- 하이퍼파라미터 k 선택이 중요함
  - k가 너무 작으면 과적합 경향
  - k가 너무 크면 과소적합 경향
- 스케일링 없는 KNN은 성능 왜곡 가능성이 큼
- 데이터 분할 시 random_state를 고정하면 재현 가능성이 높아짐

## 7. 다음 복습 추천
- train/test를 한 번만 나누는 대신 교차검증(K-Fold)으로 k 선택해보기
- 거리 척도(euclidean, manhattan) 바꿔 성능 비교
- confusion matrix, classification report로 클래스별 성능 확인
- 피처 4개(꽃받침 포함) 사용 시 성능 변화 비교

## 한 줄 정리
이 노트북은 iris 데이터로 KNN 다중분류의 전체 파이프라인(탐색 -> 분할 -> 표준화 -> 학습 -> 평가/확률예측)을 연습하고, k 값 변화에 따른 성능 차이를 확인한 실습이다.
