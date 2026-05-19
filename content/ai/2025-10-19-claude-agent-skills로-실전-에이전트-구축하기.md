+++
title = "Claude Agent Skills로 실전 에이전트 구축하기"
date = "2025-10-19"
description = "Agent Skills는 SKILL.md 파일 기반의 폴더 구조로, 범용 Claude 에이전트에게 도메인 특화 지식을 동적으로 주입하는 설계 패턴이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Agent Skills는 SKILL.md 파일 기반의 폴더 구조로, 범용 Claude 에이전트에게 도메인 특화 지식을 동적으로 주입하는 설계 패턴이다.

---

## Agent Skills를 왜 쓰는지 감 잡기

Claude는 범용적으로 강력하지만, 실제 조직의 업무에는 절차적 지식이 필요하다. 예를 들어 "PDF 양식을 채워줘"라고 하면 Claude는 PDF를 이해하지만, 특정 양식의 필드 구조나 채우는 방식까지 저절로 알지는 못한다.

Agent Skills는 이런 도메인별 절차 지식을 파일로 구조화해, Claude가 작업을 감지했을 때 필요한 만큼만 불러오게 한다. 단편적인 맞춤 에이전트를 새로 만들 필요 없이, 기존 범용 에이전트에 스킬을 덧붙여 특화시키는 방식이다.

`핵심 흐름: SKILL.md 작성 → 에이전트 시작 시 메타데이터 로드 → 관련 작업 감지 → 전체 스킬 로드 → 필요한 하위 파일 탐색`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SKILL.md | 스킬의 진입점 파일 — YAML 프론트매터에 이름과 설명, 본문에 지침을 담는다 |
| 점진적 공개(Progressive Disclosure) | 모든 내용을 한 번에 컨텍스트에 넣지 않고, Claude가 필요하다고 판단할 때만 하위 파일을 읽는 구조 |
| 번들 파일 | SKILL.md가 참조하는 추가 파일들 (예: forms.md, reference.md) — 3번째 세부 수준에 해당 |
| 메타데이터 트리거 | 시작 시 시스템 프롬프트에 로드되는 스킬 이름/설명 — Claude가 이걸 보고 스킬 사용 여부를 결정 |
| 결정론적 실행 | 스킬 내 스크립트를 Claude가 직접 실행해 예측 가능한 결과를 얻는 것 — 토큰 생성보다 빠르고 정확 |

## 예를 들어 설명하면

PDF 양식 자동 작성 스킬의 실제 구조:

```
pdf-skill/
  SKILL.md         ← 이름, 설명, 핵심 지침
  reference.md     ← PDF 관련 참고 정보
  forms.md         ← 양식 작성 전용 지침 (양식 작업 시에만 로드)
  fill_form.py     ← 양식 필드를 추출하는 Python 스크립트
```

Claude가 "이 PDF 양식을 채워줘"를 받으면:
1. 메타데이터에서 pdf-skill을 감지
2. SKILL.md 전체를 읽음
3. 양식 작업이므로 forms.md를 추가로 읽음
4. fill_form.py를 실행해 필드 목록을 추출 (스크립트 자체는 컨텍스트에 없어도 됨)

## 이 단계에서 중요한 판단 기준

스킬을 단일 SKILL.md로 유지할지 하위 파일로 분할할지 고민된다면 — "이 내용이 항상 필요한가, 아니면 특정 시나리오에서만 필요한가?"를 기준으로 나눠라.

## 한 줄 요약 — 이것만 기억하면 된다

**Agent Skills는 범용 에이전트에 도메인 지식을 조합 가능한 단위로 붙이는 방법이며, 잘 정리된 SKILL.md 하나가 전체 스킬의 품질을 결정한다.**

## 나중에 더 깊게 들어가면

- 스킬 이름과 설명을 어떻게 써야 Claude가 올바르게 트리거하는지 (메타데이터 튜닝)
- 스킬 내 코드와 문서를 분리하는 기준
- 스킬이 MCP 서버와 어떻게 상호보완적으로 작동하는지

---

**원본:** [Claude Agent Skills로 실전 에이전트 구축하기 — https://memoryhub.tistory.com/860](https://memoryhub.tistory.com/860)
