+++
title = "Hugging Face — NLP 모델을 쉽게 쓰게 해주는 플랫폼"
date = "2024-05-27"
description = "Hugging Face는 수천 개의 사전 학습 모델을 코드 몇 줄로 불러다 쓸 수 있게 해주는 오픈소스 플랫폼이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Hugging Face는 수천 개의 사전 학습 모델을 코드 몇 줄로 불러다 쓸 수 있게 해주는 오픈소스 플랫폼이다.

---

## Hugging Face를 왜 쓰는지 감 잡기

자연어 처리(NLP) 모델을 처음부터 학습시키려면 수백만 달러의 컴퓨팅 비용과 수개월의 시간이 필요하다. Hugging Face는 이미 학습된 모델을 공개 저장소(Model Hub)에 올려두고, 누구든 몇 줄의 코드로 가져다 쓸 수 있게 한다. 감성 분석, 번역, 요약, 질의응답 같은 작업을 처음부터 구현하는 대신, 검증된 모델을 내려받아 바로 사용하거나 내 데이터에 맞게 미세 조정(fine-tuning)하면 된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 모델 선택 → 토크나이저로 텍스트 변환 → 모델 추론 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 트랜스포머(Transformer) | 문장 안의 단어 사이 관계를 한꺼번에 계산하는 모델 구조. BERT, GPT 등이 여기 속한다. |
| 사전 학습 모델(Pre-trained model) | 이미 방대한 텍스트로 학습을 마친 모델. 바로 쓰거나 추가 학습만 하면 된다. |
| 토크나이저(Tokenizer) | 사람이 쓴 문장을 모델이 읽을 수 있는 숫자 덩어리(토큰)로 쪼개는 도구. |
| 미세 조정(Fine-tuning) | 이미 학습된 모델을 내 데이터로 추가 학습시켜 특정 작업 성능을 올리는 과정. |
| Model Hub | Hugging Face가 운영하는 모델 공개 저장소. 수만 개의 모델을 검색하고 내려받을 수 있다. |

## 예를 들어 설명하면

고객 리뷰가 긍정인지 부정인지 판별하는 기능이 필요하다고 가정하자. 모델을 직접 만들 필요 없이 아래처럼 쓰면 된다.

```python
from transformers import pipeline

classifier = pipeline("sentiment-analysis")
result = classifier("The delivery was fast and the product quality is excellent.")
print(result)
# [{'label': 'POSITIVE', 'score': 0.9998}]
```

`pipeline`은 모델 로드, 토크나이징, 추론을 한 번에 처리한다. 내부적으로 BERT 계열 모델이 동작하지만, 사용자는 세부 구조를 몰라도 된다.

## 이 단계에서 중요한 판단 기준

Hugging Face를 선택하기 전에 먼저 확인할 것은 "Model Hub에 내 작업에 맞는 사전 학습 모델이 이미 있는가"이다. 있다면 미세 조정만으로 충분하고, 없다면 직접 학습이 필요하다.

## 한 줄 요약 — 이것만 기억하면 된다

**Hugging Face는 NLP 모델을 처음부터 만들지 않아도 되게 해주는 도구 모음이다.**

## 나중에 더 깊게 들어가면

- 미세 조정(fine-tuning) 절차와 학습률, 에포크 설정
- BERT vs GPT 계열의 구조 차이와 용도 선택 기준
- 양자화(quantization)와 ONNX 변환으로 모델 배포 최적화

---

**원본:** [Hugging Face Introduced — https://memoryhub.tistory.com/137](https://memoryhub.tistory.com/137)
