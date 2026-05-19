+++
title = "Claude Agent SDK: 코딩용 AI가 범용 에이전트로 진화한 이유"
date = "2025-09-30"
description = "Claude Agent SDK는 AI에게 파일 시스템과 터미널 접근권을 주면 코딩뿐 아니라 리서치, 메일 처리, 재무 분석까지 처리할 수 있다는 발견에서 출발한 범용 에이전트 개발 플랫폼이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Claude Agent SDK는 AI에게 파일 시스템과 터미널 접근권을 주면 코딩뿐 아니라 리서치, 메일 처리, 재무 분석까지 처리할 수 있다는 발견에서 출발한 범용 에이전트 개발 플랫폼이다.

---

## Claude Agent SDK를 왜 쓰는지 감 잡기

기존 AI 에이전트는 특정 작업에만 맞춰져 있거나, 여러 단계로 이어지는 작업에서 중간에 맥락을 잃는 문제가 있었다. Anthropic은 Claude Code를 만들면서 흥미로운 사실을 발견했다. 프로그래머가 매일 쓰는 도구(파일 검색, 편집, 코드 실행, 터미널)를 AI에게 주면, AI가 코드 작업뿐 아니라 CSV 분석, 웹 검색, 문서 생성 같은 범용 디지털 작업도 처리한다는 것이다.

2025년 9월 29일 Anthropic은 이 기반 기술을 Claude Agent SDK로 공개했다.

`맥락 수집 → 작업 실행 → 결과 검증 → 반복 (에이전트 루프)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 에이전트 루프 | 맥락 수집, 작업 실행, 검증을 반복하며 목표에 도달하는 AI의 작동 사이클 |
| Subagent | 큰 작업을 병렬로 나눠 처리하는 독립 하위 에이전트. 처리 속도를 높이고 컨텍스트를 절약함 |
| MCP (Model Context Protocol) | Slack, GitHub, Drive 같은 외부 서비스와 표준화된 방식으로 연결하는 프로토콜 |
| Agentic Search | RAG 파이프라인 없이 grep, find 같은 명령어로 필요한 정보를 동적으로 찾는 방식 |
| Compaction | 컨텍스트 한계에 가까워지면 이전 내용을 자동으로 요약해 처리 가능한 크기로 줄이는 기능 |

## 예를 들어 설명하면

이메일 처리 에이전트를 구축하는 기본 구조:

```python
import anyio
from claude_agent_sdk import query, ClaudeAgentOptions

options = ClaudeAgentOptions(
    allowed_tools=["Read", "Bash", "mcp__asana__get_tasks"],
    permission_mode='prompt'  # 파일 수정 시 사람에게 확인 요청
)

async def main():
    async for message in query(
        prompt="받은 메일함에서 미처리 항목을 분류하고 Asana 태스크로 등록해줘",
        options=options
    ):
        print(message)

anyio.run(main)
```

에이전트 루프의 세 단계가 실제로 동작하는 방식:

1. **맥락 수집**: `Conversations/` 폴더를 grep으로 검색하고, Subagent를 병렬로 실행해 핵심 정보만 오케스트레이터에 전달
2. **작업 실행**: MCP를 통해 Asana에 태스크 생성. OAuth 관리는 SDK가 처리
3. **검증**: 이메일 주소 유효성 규칙 + 별도 서브에이전트로 결과 품질 평가

## 이 단계에서 중요한 판단 기준

에이전트가 자꾸 실패하면 프롬프트보다 "올바른 도구를 갖췄는지"를 먼저 점검해야 한다. 도구가 없으면 아무리 좋은 모델도 작업을 완료할 수 없다.

## 한 줄 요약 — 이것만 기억하면 된다

**Claude Agent SDK는 AI에게 파일 시스템과 터미널을 주면 RAG 파이프라인 없이도 강력한 범용 에이전트를 만들 수 있다는 철학의 구현체다.**

## 나중에 더 깊게 들어가면

- Subagent 병렬화 설계와 오케스트레이터 간 컨텍스트 전달 최적화
- MCP 생태계에서 커스텀 서버를 직접 만들어 내부 시스템 연결하는 방법
- LLM-as-Judge 패턴으로 에이전트 출력 품질을 자동 평가하는 검증 루프 설계

---

**원본:** Claude Agent SDK, 코딩용 AI가 범용 에이전트로 진화한 이유 — https://memoryhub.tistory.com/808
