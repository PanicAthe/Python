# 7_mnist_cnn 개념/공부 요약

## 1. 이 노트북에서 한 일 (큰 흐름)
- MNIST 데이터를 불러와 이미지/레이블 구조와 클래스 분포를 확인함
- CNN 입력 형식에 맞게 `(28,28)` 이미지를 `(28,28,1)`로 reshape하고 정규화함
- one-hot 인코딩 후 train/validation/test로 분리해 학습 검증 체계를 만듦
- Conv2D + MaxPooling + Dense + Dropout 구조의 CNN 모델을 구성함
- 학습 곡선(train/val)과 테스트 성능을 확인하고 샘플 예측 결과를 비교함

## 2. 핵심 개념 정리

### 2-1. CNN 입력 형태와 전처리
- 원본 MNIST 이미지는 `(N, 28, 28)` 형태임
- Conv2D는 채널 축이 필요하므로 `(N, 28, 28, 1)`로 변환해야 함
- 픽셀을 `255`로 나눠 `0~1` 범위로 정규화해 학습 안정성을 높임
- shape 변화
  - 변환 전: `x_train (60000, 28, 28)`, `x_test (10000, 28, 28)`
  - 변환 후: `x_train (60000, 28, 28, 1)`, `x_test (10000, 28, 28, 1)`

### 2-2. CNN 블록의 역할 (Conv2D + MaxPooling)
- Conv2D는 이미지에서 모서리/획 같은 지역 특징을 추출함
- 첫 블록: `Conv2D(32, 3x3, same, relu)` + `MaxPooling2D(2x2)`
- 두 번째 블록: `Conv2D(64, 3x3, same, relu)` + `MaxPooling2D(2x2)`
- 공간 크기 변화: `28x28 -> 14x14 -> 7x7`
- 채널 변화: `1 -> 32 -> 64`

### 2-3. 분류 헤드와 과적합 완화
- `Flatten`으로 feature map을 1차원 벡터(3136)로 펼침
- `Dense(128, relu)`로 고수준 표현 학습
- `Dropout(0.3)`로 학습 중 일부 뉴런을 비활성화해 과적합을 줄임
- 출력층 `Dense(10, softmax)`로 숫자 0~9의 클래스 확률을 출력함

## 3. 코드 흐름 요약

### 3-1. 데이터 준비
- 데이터 로드: `datasets.mnist.load_data()`
- 레이블 one-hot: `utils.to_categorical(y_train)`, `utils.to_categorical(y_test)`
- 검증 분리: `train_test_split(..., test_size=0.2, random_state=11)`
- 최종 shape
  - train: `(48000, 28, 28, 1)`, `(48000, 10)`
  - val: `(12000, 28, 28, 1)`, `(12000, 10)`
  - test: `(10000, 28, 28, 1)`, `(10000, 10)`

### 3-2. 모델 정의/학습
- 모델: `Conv(32)-Pool-Conv(64)-Pool-Flatten-Dense(128)-Dropout(0.3)-Dense(10)`
- 컴파일: `loss='categorical_crossentropy'`, `optimizer='adam'`, `metrics=['acc']`
- 학습: `epochs=10`, `validation_data=(x_val, y_val_oh)`
- 학습 기록은 `history.history`로 `acc/loss/val_acc/val_loss`를 확인함

### 3-3. 평가/예측
- 테스트 평가: `model.evaluate(x_test, y_test_oh)`
- 샘플 예측: `model.predict(x_test[0:10])`
- 예측 클래스: `np.argmax(..., axis=1)`
- 정답과 비교: `np.argmax(y_test_oh[:10], axis=1)`

## 4. 실행 결과 핵심
- CNN 블록만 구성 시 파라미터: `18,816`
- 최종 모델 총 파라미터: `421,642`
- 마지막 epoch(10) 학습 지표: `acc: 0.9961`, `loss: 0.0112`
- 마지막 epoch(10) 검증 지표: `val_acc: 0.9905`, `val_loss: 0.0384`
- 테스트셋 지표: `acc: 0.9906`, `loss: 0.0323094017803669`
- 첫 10개 예측 클래스: `[7 2 1 0 4 1 4 9 5 9]`
- 첫 10개 실제 클래스: `[7 2 1 0 4 1 4 9 5 9]`

해석
- CNN 기반 모델이 MLP보다 공간적 특징을 더 잘 활용해 매우 높은 정확도(약 99.1%)를 보임
- train/val 성능이 모두 높고 차이가 작아 일반화가 안정적인 편임
- Dropout을 포함해도 성능이 유지되어 과적합 완화에 도움을 준 것으로 해석 가능함

## 5. 공부 포인트
- Conv 파라미터 공식 `(커널높이*커널너비*입력채널 + bias) * 필터수` 직접 계산해보기
- `same` 패딩과 풀링이 feature map 크기에 주는 영향(28->14->7) 익히기
- `history` 곡선에서 과적합 징후(train↑, val 정체/하락)를 읽는 습관 만들기
- MLP(6번)와 CNN(7번)의 정확도/파라미터/학습시간 차이를 표로 비교해보기

## 6. 다음 복습 추천
1. Dropout 비율을 `0.0`, `0.3`, `0.5`로 바꿔 val/test 성능 변화 비교하기
2. Conv 필터 수를 `(32,64)`에서 `(16,32)` 또는 `(64,128)`로 바꿔 성능-속도 트레이드오프 확인하기
3. 오답 샘플을 시각화해 어떤 숫자 쌍에서 혼동이 많은지 분석하기
4. EarlyStopping을 추가해 최적 epoch를 자동으로 찾고 성능을 비교하기

## 한 줄 정리
이 노트북은 MNIST를 CNN으로 학습해 합성곱 기반 특징 추출이 숫자 분류 성능을 크게 높인다는 점을 실습으로 확인한 내용이다.
