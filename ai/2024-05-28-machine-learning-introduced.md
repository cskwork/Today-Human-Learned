# 머신러닝 — 데이터에서 스스로 배우는 시스템

> **TL;DR**
> 머신러닝은 사람이 규칙을 일일이 짜지 않아도, 데이터로부터 패턴을 찾아 스스로 예측하는 기술이다.

---

## 머신러닝을 왜 쓰는지 감 잡기

기존 프로그래밍은 "비가 오면 우산을 챙겨라"처럼 규칙을 직접 코드에 적는다. 그런데 스팸 필터링이나 얼굴 인식처럼 규칙이 수천 가지인 문제에서는 이 방식이 무너진다. 머신러닝은 접근법을 뒤집는다. 규칙을 쓰는 대신 예시 데이터를 많이 보여주면, 시스템이 스스로 패턴을 추출한다.

실생활에서는 넷플릭스 추천, 신용카드 부정거래 감지, 번역 앱이 전부 머신러닝으로 작동한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 데이터 수집 → 알고리즘으로 학습 → 모델 생성 → 새 데이터에 예측`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 데이터 | 모델이 배울 재료. 양이 많고 품질이 좋을수록 결과가 좋다. |
| 알고리즘 | 데이터에서 패턴을 찾는 수학적 방법. 요리 레시피에 해당한다. |
| 학습(Training) | 알고리즘이 데이터를 반복해서 보며 내부 값을 조정하는 과정. |
| 모델 | 학습이 끝난 결과물. 새 입력을 받아 예측값을 돌려준다. |
| 지도학습 | 정답이 붙은 데이터로 학습하는 방식. 예: 집값 예측, 스팸 분류. |

## 예를 들어 설명하면

집값 예측 모델을 만드는 가장 짧은 예시다. 면적, 방 수, 건축 연도를 입력하면 가격을 예측한다.

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression

data = pd.read_csv('house_prices.csv').dropna()
X = data[['square_feet', 'num_rooms', 'age']]
y = data['price']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

model = LinearRegression()
model.fit(X_train, y_train)          # 학습
print(model.predict(X_test[:3]))     # 새 데이터에 예측
```

`model.fit()`이 학습 단계이고, `model.predict()`가 결과를 내는 단계다.

## 이 단계에서 중요한 판단 기준

데이터의 양과 품질이 알고리즘 선택보다 중요하다. 좋은 데이터 1,000개가 나쁜 데이터 100,000개보다 낫다.

## 한 줄 요약 — 이것만 기억하면 된다

**머신러닝은 "규칙을 직접 쓰는 대신, 데이터로 규칙을 학습시키는" 패러다임 전환이다.**

## 나중에 더 깊게 들어가면

- 비지도학습(Unsupervised Learning): 정답 없이 군집을 찾는 방법
- 강화학습(Reinforcement Learning): 시행착오로 최적 행동을 찾는 방법
- 과적합(Overfitting): 학습 데이터에만 맞고 새 데이터에서 틀리는 문제

---

**원본:** [Machine Learning Introduced — memoryhub.tistory.com/126](https://memoryhub.tistory.com/126)
