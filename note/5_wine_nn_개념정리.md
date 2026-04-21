# 5_wine_nn 개념/공부 요약

## 1. 이 노트북에서 한 일 (큰 흐름)
- 와인 데이터(`wine.csv`)를 불러와 클래스 분포를 확인함
- 입력 특성(`alcohol`, `sugar`, `pH`)과 타깃(`class`)을 분리함
- StandardScaler로 입력값을 표준화함
- train/test를 8:2로 분할함
- TensorFlow Keras로 다층 신경망(MLP) 모델을 구성함
- 타깃을 one-hot encoding으로 변환해 다중 출력 형식에 맞춤
- `mse + sgd` 설정으로 200 epoch 학습을 수행함
- 테스트셋 성능 평가(`evaluate`)와 샘플 예측(`predict`, `argmax`)을 확인함

## 2. 핵심 개념 정리

### 2-1. 입력 전처리와 스케일링
- 신경망은 입력 특성의 스케일 차이에 민감하므로 표준화가 중요함
- 이 노트에서는 `StandardScaler`를 사용해 평균 0, 표준편차 1로 변환함
- 첫 샘플 표준화 결과: `[-0.91546416, -0.7447781, 1.81308951]`

### 2-2. 다층 퍼셉트론(MLP) 구조
- 모델 구조
  - Dense(16, relu, input_shape=(3,))
  - Dense(10, relu)
  - Dense(8, relu)
  - Dense(2, softmax)
- 출력층 softmax를 통해 2개 클래스 확률(화이트/레드)을 출력함
- 모델 파라미터 수
  - Total params: 340
  - Trainable params: 340

### 2-3. 원-핫 인코딩과 손실함수
- `to_categorical`로 타깃을 one-hot 형식으로 바꿔 다중 출력과 맞춤
- 변환 전/후
  - 전: `(5197,)`, `(1300,)`
  - 후: `(5197,)`, `(1300, 2)`
- 노트 설명에는 `categorical_crossentropy`, `sparse_categorical_crossentropy`, optimizer 개념(특히 SGD/Adam)도 함께 다룸

## 3. 코드 흐름 요약

### 3-1. 데이터 준비
- 클래스 분포: `0` 클래스 1599개, `1` 클래스 4898개
- 입력: `['alcohol', 'sugar', 'pH']`
- 타깃: `class`

### 3-2. 분할/모델 정의
- `train_test_split(..., test_size=0.2, random_state=11)`
- 분할 크기
  - `x_train`: `(5197, 3)`, `y_train`: `(5197,)`
  - `x_test`: `(1300, 3)`, `y_test`: `(1300,)`
- Keras `Sequential` 모델로 은닉층 3개 + softmax 출력층 구성

### 3-3. 학습/평가/예측
- 컴파일: `loss='mse'`, `optimizer='sgd'`, `metrics=['accuracy']`
- 학습: `model.fit(x_train, y_train_oh, epochs=200)`
- 평가: `model.evaluate(x_test, y_test_oh)`
- 예측: `predict` 확률 -> `argmax` 인덱스 -> 와인 이름 매핑

## 4. 실행 결과 핵심
- 학습 마지막(epoch 200) 지표
  - `accuracy: 0.8620`
  - `loss: 0.1013`
- 테스트셋 평가 결과
  - `loss: 0.10897628962993622`
  - `accuracy: 0.857692301273346`
- 테스트 첫 10개 정답
  - `[1., 1., 1., 0., 1., 1., 1., 1., 0., 0.]`
- 테스트 첫 10개 예측 인덱스
  - `[1 1 1 0 1 1 1 1 0 0]`
- 텍스트 매핑 결과
  - `red wine, red wine, red wine, white wine, red wine, red wine, red wine, red wine, white wine, white wine`

해석
- 테스트 정확도 약 `0.858`로 기본 MLP 분류가 안정적으로 동작함
- 샘플 10개 기준 정답과 예측이 모두 일치해 예측 흐름이 올바르게 구현됨
- softmax 확률을 함께 확인하면 모델의 확신 정도까지 해석 가능함

## 5. 공부 포인트
- softmax 출력층 + one-hot 인코딩 조합의 의미를 명확히 이해하기
- 현재 `mse` 손실을 사용했는데, 분류 문제에서 `categorical_crossentropy`와 성능 비교해보기
- optimizer를 `sgd`에서 `adam`으로 바꿔 수렴 속도/정확도 차이 확인하기
- epoch별 학습 곡선을 그려 과소적합/과적합 여부 점검하기

## 6. 다음 복습 추천
1. `loss='categorical_crossentropy'`로 바꿔 동일 조건 재학습 후 성능 비교
2. `sparse_categorical_crossentropy` 버전으로 원-핫 없이 학습해보기
3. 혼동행렬(confusion matrix)과 precision/recall/F1 추가 분석
4. 은닉층 노드 수(16-10-8)를 바꿔 모델 복잡도와 성능 관계 확인

## 한 줄 정리
이 노트북은 와인 데이터를 표준화·분할한 뒤 Keras 다층 신경망으로 이진 분류를 수행하고, one-hot/softmax 기반 예측 과정을 실습으로 정리한 학습이다.
