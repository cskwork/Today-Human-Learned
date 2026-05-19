+++
title = "Kimi Linear: Full Attention을 넘어선 최초의 Linear Attention"
date = "2026-01-23"
description = "Kimi Linear는 \"채널별 선택적 망각\"과 \"Delta Rule 업데이트\"를 결합해 Linear Attention이 최초로 Full Attention 성능을 능가하면서 메모리는 75%, 추론 속도는 6배 개선한 모델이다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Kimi Linear는 "채널별 선택적 망각"과 "Delta Rule 업데이트"를 결합해 Linear Attention이 최초로 Full Attention 성능을 능가하면서 메모리는 75%, 추론 속도는 6배 개선한 모델이다.

---

## 이 주제를 왜 쓰는지 감 잡기

AI 언어 모델이 긴 문서를 처리할 때 가장 큰 병목은 연산량과 메모리다. 기존 Transformer의 Softmax Attention은 토큰 수 n에 대해 연산량이 n의 제곱으로 늘어난다. 100만 토큰이면 연산 횟수가 1조 번이 된다.

이 문제를 해결하려고 Linear Attention이 제안됐는데, 연산량은 줄었지만 정보가 뒤섞여 성능이 낮았다. 그 트레이드오프를 2025년 Moonshot AI가 Kimi Linear로 처음 돌파했다.

`핵심 흐름: Full Attention(정확하지만 느림) → Linear Attention(빠르지만 정보 손실) → Kimi Linear(빠르고 정확)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Softmax Attention | 모든 토큰이 서로를 참조하는 방식 — 정확하지만 길이가 늘수록 연산이 폭발적으로 증가한다 |
| Linear Attention | 연산 순서를 바꿔 O(n) 복잡도를 달성한 방식 — 빠르지만 과거 정보를 지우지 못해 상태가 포화된다 |
| KV Cache | 이전에 계산한 Key-Value를 저장해두는 메모리 공간 — 길수록 커진다 |
| Delta Rule | "예측과 실제의 차이(Delta)만큼만 수정"하는 원리 — 기존 값을 덮어쓰는 정밀 업데이트를 가능하게 한다 |
| KDA (Kimi Delta Attention) | Kimi Linear의 핵심 모듈 — 채널별 망각률과 Delta Rule을 결합한 Linear Attention |

## 예를 들어 설명하면

기존 Linear Attention의 문제를 호텔 메모장에 비유할 수 있다. 새 손님 정보가 들어올 때마다 메모장에 계속 덧쓰지만 지울 수가 없다. 시간이 지날수록 중요한 정보와 불필요한 정보가 뒤섞인다.

KDA는 두 가지로 이 문제를 푼다. 첫째, 새 정보가 들어오면 관련된 기존 정보를 먼저 지우고 덮어쓴다(Delta Rule). 둘째, "손님 이름은 빨리 잊어도 되지만 알레르기 정보는 오래 기억해야 한다"처럼 정보 종류별로 망각 속도를 다르게 적용한다(채널별 게이팅).

그 결과, KDA 레이어 3개와 기존 Full Attention(MLA) 레이어 1개를 교차 배치하는 3:1 하이브리드 구조가 최적 균형점으로 나타났다.

| 아키텍처 | KV Cache | 추론 속도 | 긴 문맥 성능 |
|---|---|---|---|
| Full Attention | 선형 증가 | 기준선 | 우수 |
| 기존 Linear Attention | 고정 | 3배+ | 저하 |
| Kimi Linear (3:1 혼합) | 75% 절감 | 6배+ | Full Attention 능가 |

## 이 단계에서 중요한 판단 기준

긴 문맥 처리(100K 토큰 이상)가 필요한 프로젝트에서 추론 비용이 병목이라면, Kimi Linear는 성능 타협 없이 자원을 줄일 수 있는 첫 번째 실용적 선택지다.

## 한 줄 요약 — 이것만 기억하면 된다

**Kimi Linear는 "선택적 망각"으로 Linear Attention의 고질적 한계를 돌파해, 효율과 정확도를 동시에 잡은 최초의 사례다.**

## 나중에 더 깊게 들어가면

- Flash Linear Attention 라이브러리와 KDA 커널의 구현 세부사항
- MLA(Multi-Head Latent Attention)와 DeepSeek-V2의 KV Cache 압축 방식
- Reinforcement Learning 학습 시 Kimi Linear의 수렴 속도 우위가 생기는 이유

---

**원본:** [Kimi Linear, Full Attention을 넘어선 최초의 Linear Attention이 등장했다](https://memoryhub.tistory.com/988)
