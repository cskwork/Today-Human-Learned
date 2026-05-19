+++
title = "RAG 기술 지형도: LightRAG vs 엔터프라이즈 프로덕션 시스템"
date = "2025-06-29"
description = "RAG는 AI가 답하기 전에 관련 문서를 먼저 찾아보는 기술이다. LightRAG는 비용을 99% 이상 줄인 신기술이지만, 실무 배포는 여전히 검증된 GraphRAG나 벡터 RAG가 주류다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> RAG는 AI가 답하기 전에 관련 문서를 먼저 찾아보는 기술이다. LightRAG는 비용을 99% 이상 줄인 신기술이지만, 실무 배포는 여전히 검증된 GraphRAG나 벡터 RAG가 주류다.

---

## RAG를 왜 쓰는지 감 잡기

LLM(대형 언어 모델)은 학습 당시의 지식만 갖고 있다. 최신 정보나 사내 문서를 모른다. RAG(Retrieval-Augmented Generation)는 이 문제를 해결한다. 질문이 들어오면 먼저 관련 문서를 검색해서 AI에게 건네준다. AI는 그 자료를 보고 답변을 생성한다.

2024-2025년 기준으로 기업의 절반 이상이 생성 AI 사용 사례의 30-60%에 RAG를 적용하고 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 질문 → 문서 검색 → AI에게 문서 + 질문 전달 → 답변 생성`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| RAG | 답하기 전에 관련 자료를 먼저 찾아보는 AI 기법 |
| 벡터 DB | 텍스트를 수치 벡터로 변환해 의미 기반 검색을 가능하게 하는 데이터베이스 |
| GraphRAG | 문서를 지식 그래프로 구성해 연관 정보 간 추론이 가능한 Microsoft의 RAG 방식 |
| LightRAG | 그래프 기반 검색을 유지하면서 토큰 소비를 GraphRAG의 1/6000 수준으로 줄인 신기법 |
| 멀티에이전트 RAG | 여러 전문 에이전트가 각각 다른 데이터 소스를 담당하고 중앙에서 조율하는 구조 |

## 예를 들어 설명하면

**LightRAG vs GraphRAG 토큰 소비 비교**

| 항목 | LightRAG | GraphRAG |
|---|---|---|
| 쿼리당 토큰 | 100개 미만 | 610,000개 이상 |
| 응답 시간 | 약 80ms | 약 120ms |
| 법률 데이터 검색 정확도 | 80% 이상 | 60-70% |
| 증분 업데이트 속도 | 기존 대비 50% 빠름 | 전체 재인덱싱 필요 |

비용 민감한 대용량 서비스에서는 LightRAG가 매력적이다. 반면 복잡한 멀티홉 추론(여러 문서를 연결해 답을 찾는 작업)에는 GraphRAG가 여전히 우세하다.

## 이 단계에서 중요한 판단 기준

정확성과 안정성이 최우선이면 Azure AI Search나 Amazon Bedrock 같은 관리형 서비스를 쓰고, 비용과 속도가 핵심이라면 LightRAG를 파일럿으로 검토하라.

## 한 줄 요약 — 이것만 기억하면 된다

**RAG 기법 선택의 핵심은 기술 우위가 아니라 use case의 정확도·비용·안정성 요구사항과의 매칭이다.**

## 나중에 더 깊게 들어가면

- Self-RAG: 답변 품질을 스스로 평가하고 재검색하는 반성적 RAG
- RAPTOR: 계층적 클러스터링으로 긴 문서를 처리하는 기법
- 멀티모달 RAG: 텍스트, 이미지, 영상을 통합해 검색하는 방향

---

**원본:** [RAG Technology Landscape: LightRAG vs Enterprise Production Systems](https://memoryhub.tistory.com/713)
