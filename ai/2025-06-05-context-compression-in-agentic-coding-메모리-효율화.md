# Context Compression — AI 코딩 도구가 긴 코드를 잊지 않는 방법

> **TL;DR**
> LLM은 컨텍스트 창이 가득 차면 중간 내용을 잊는다. Context Compression은 이 문제를 해결하기 위해 코드와 문서를 압축해서 핵심만 남기는 기술이다.

---

## Context Compression을 왜 쓰는지 감 잡기

AI 코딩 도구가 단순 자동완성을 넘어 파일 수십 개를 동시에 다루는 에이전트로 진화하면서 새로운 문제가 생겼다. LLM이 한 번에 처리할 수 있는 토큰 수에 한계가 있고, 컨텍스트가 길어질수록 중간에 있는 내용을 놓치는 현상이 발생한다.

이를 "Lost in the Middle" 현상이라 부른다. 컨텍스트의 60%를 넘어서면 모델 성능이 급격히 저하된다는 실험 결과가 있다. 대규모 코드베이스에서 에이전트가 갑자기 엉뚱한 코드를 생성하는 주요 원인이 여기에 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 원본 코드 수집 → 관련성 평가 → 불필요한 부분 제거 → 압축된 컨텍스트로 LLM 호출`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 컨텍스트 창 (Context Window) | LLM이 한 번에 읽을 수 있는 최대 텍스트 양. Claude는 200K, GPT-4는 128K 토큰이 한계다 |
| Lost in the Middle | 컨텍스트가 너무 길면 LLM이 중간 부분의 내용을 제대로 참조하지 못하는 현상 |
| Semantic Compression | 코드의 전체 소스를 그대로 넣는 대신, 함수 시그니처와 요약만 추출하는 방식 |
| RAG (Retrieval-Augmented Generation) | 방대한 문서 중 현재 질문과 관련된 부분만 검색해서 LLM에 전달하는 기법 |
| LLMLingua | 작은 언어 모델로 프롬프트의 중요도를 평가한 뒤 불필요한 토큰을 잘라내는 압축 도구 |

## 예를 들어 설명하면

Semantic Compression이 실제로 어떻게 동작하는지 보면 이해가 빠르다.

```
원본 코드 (1,000 토큰)
──────────────────────
import React from 'react'
import { useState, useEffect } from 'react'

// UserProfile 컴포넌트 — 100줄 전체 소스

압축된 컨텍스트 (100 토큰)
──────────────────────────
React 컴포넌트: UserProfile
- State: user (object), loading (boolean)
- useEffect: 마운트 시 /api/user/:id 호출
- 의존성: Avatar, StatusBadge 컴포넌트
```

에이전트가 이 컴포넌트를 수정해야 한다면, 100줄 전체가 아니라 위의 요약만 컨텍스트에 넣고 작업을 지시한다. 실제 수정이 필요할 때만 원본 파일을 읽도록 한다.

## 이 단계에서 중요한 판단 기준

압축률은 70~80%가 최적이다. 그 이상 압축하면 중요한 비즈니스 로직이 소실된다. 핵심 도메인 로직은 압축 대상에서 제외하고, 보일러플레이트와 임포트 구문을 우선 제거한다.

| 압축 기법 | 토큰 절감 | 특징 |
|---|---|---|
| LLMLingua | 최대 75% | 정확도 향상, 별도 모델 필요 |
| Semantic Compression | 50~80% | 구현 간단, 구조적 정보 보존 |
| Hierarchical Compression | 40~60% | 계층 구조 보존, 구현 복잡 |

## 한 줄 요약 — 이것만 기억하면 된다

**AI 에이전트에게 코드 전체를 던지지 말고, 관련된 요약만 골라서 전달하는 것이 Context Compression의 핵심이다.**

## 나중에 더 깊게 들어가면

- LLMLingua와 LongLLMLingua의 차이 및 적용 방법
- RAG 파이프라인에서 Contextual Compression Retriever 구성
- Claude Code의 CLAUDE.md를 활용한 프로젝트별 컨텍스트 최적화 전략

---

**원본:** [Context Compression in Agentic Coding — https://memoryhub.tistory.com/650](https://memoryhub.tistory.com/650)
