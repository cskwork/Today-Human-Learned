+++
title = "Transformer — LLM이 문장을 이해하는 방식"
date = "2024-05-26"
description = "Transformer는 문장 안의 모든 단어가 서로 얼마나 관련 있는지를 동시에 계산해서 문맥을 파악하는 구조다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Transformer는 문장 안의 모든 단어가 서로 얼마나 관련 있는지를 동시에 계산해서 문맥을 파악하는 구조다.

---

## Transformer를 왜 쓰는지 감 잡기

이전 AI 모델들은 문장을 앞에서 뒤로 순서대로 읽었다. 문장이 길어지면 앞의 내용을 잊어버리는 문제가 생겼다.
Transformer는 이 한계를 돌파하기 위해 2017년 Google이 발표한 구조다.
핵심 아이디어는 단순하다. 문장 전체를 한 번에 보면서 단어들 사이의 관계를 계산한다.

덕분에 "나는 사과를 샀는데 그것이 맛있었다"에서 "그것"이 "사과"를 가리킨다는 것을
긴 문장에서도 정확히 잡아낼 수 있다.
ChatGPT, Claude, Gemini 같은 대형 언어 모델(LLM)이 모두 이 구조를 기반으로 한다.

`핵심 흐름: 입력 텍스트 -> 위치 인코딩 -> Self-Attention -> Feedforward -> 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Self-Attention | 한 단어가 같은 문장 안의 다른 단어들과 얼마나 연관 있는지 점수를 매기는 계산 |
| Query / Key / Value | Self-Attention 계산에 사용하는 세 가지 벡터. "무엇을 찾는지(Q)", "무엇이 있는지(K)", "실제 내용(V)"로 이해하면 된다 |
| Positional Encoding | 단어의 순서 정보를 숫자로 표현해 모델에 알려주는 방법. 없으면 모델이 어순을 모른다 |
| Multi-Head Attention | Self-Attention을 여러 개 병렬로 실행해서 문장의 여러 측면(문법, 의미, 지시 관계 등)을 동시에 파악하는 구조 |
| Feedforward Network | Attention 결과를 받아 더 복잡한 패턴을 추출하는 일반적인 신경망 레이어 |

## 예를 들어 설명하면

"The quick brown fox jumps over the lazy dog" 문장을 처리하는 흐름이다.

1. **위치 인코딩**: 각 단어에 위치 번호(1번=The, 2번=quick...)를 숫자로 더해준다.
2. **Self-Attention**: "fox"는 "jumps"와 높은 점수, "lazy"와 낮은 점수를 받는다. "dog"는 "lazy"와 강하게 연결된다.
3. **Multi-Head Attention**: 한 헤드는 주어-동사 관계에 집중하고, 다른 헤드는 형용사-명사 관계에 집중한다.
4. **Feedforward**: 각 단어의 최종 표현을 만든다.

이 과정이 수십 개 레이어에 걸쳐 반복되면서 모델은 점점 추상적인 의미를 이해하게 된다.

## 이 단계에서 중요한 판단 기준

Transformer가 강력한 이유는 병렬 처리다. 순차 처리 모델과 달리 문장 전체를 동시에 계산하므로 GPU를 효율적으로 쓸 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**Transformer는 문장 내 모든 단어 쌍의 관계를 동시에 계산하는 Self-Attention 덕분에 긴 문맥을 잃지 않고 이해한다.**

## 나중에 더 깊게 들어가면

- Encoder-Decoder 구조: BERT(인코더 전용)와 GPT(디코더 전용)가 각각 어떻게 다른지
- Attention Score 계산: Query, Key의 내적과 softmax가 수식으로 어떻게 동작하는지
- Scaling 문제: 문장이 길어질수록 Attention 계산량이 제곱으로 늘어나는 문제와 해결 방법

---

**원본:** [Transformers in LLMs Introduced — https://memoryhub.tistory.com/89](https://memoryhub.tistory.com/89)
