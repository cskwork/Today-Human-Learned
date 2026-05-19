+++
title = "AI로 결제 연동을 쉽게 — 토스페이먼츠 MCP 서버 구현기"
date = "2025-10-24"
description = "AI 코딩 도구에 정확한 문서 맥락을 제공하는 로컬 MCP 서버를 BM25 기반 청크 검색으로 구현하면, AI가 생성하는 결제 연동 코드의 정확도가 크게 올라간다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> AI 코딩 도구에 정확한 문서 맥락을 제공하는 로컬 MCP 서버를 BM25 기반 청크 검색으로 구현하면, AI가 생성하는 결제 연동 코드의 정확도가 크게 올라간다.

---

## MCP 서버를 왜 쓰는지 감 잡기

AI 코딩 도구(Cursor 등)에 "토스페이먼츠 결제 위젯을 붙여줘"라고 하면 코드를 생성한다. 그런데 SDK 주소가 틀리고, 보안상 절대 클라이언트에 두면 안 되는 Secret Key가 프론트엔드 코드에 노출되는 식의 오류가 종종 나온다. AI가 최신 공식 문서를 모르기 때문이다.

MCP(Model Context Protocol)는 AI 모델이 외부 데이터 소스와 표준 방식으로 연결하도록 Anthropic이 제안한 규격이다. 결제 회사가 MCP 서버를 운영하면, AI 도구는 그 서버를 통해 항상 최신 공식 문서를 참조하며 코드를 생성할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 공식 문서 CDN → MCP 서버(BM25 검색) → AI 코딩 도구 → 정확한 코드`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP (Model Context Protocol) | AI 모델이 외부 도구·문서와 표준 방식으로 통신하는 규격. USB처럼 "연결 표준"이다 |
| llms.txt | LLM이 웹사이트 문서를 잘 이해하도록 안내하는 표준 파일 — 사이트맵의 AI 버전 |
| BM25 | 검색 엔진이 쿼리와 문서의 관련도를 수치로 계산하는 알고리즘. 단어 빈도와 문서 길이를 함께 고려한다 |
| 청크(Chunk) | 긴 문서를 의미 단위(헤더 기준)로 잘라낸 조각. LLM이 한 번에 소화할 수 있는 크기로 나눈다 |
| STDIO Transport | 프로세스 간 표준 입출력으로 통신하는 MCP 전송 방식. 서버 없이 로컬에서 동작하므로 비용이 없다 |

## 예를 들어 설명하면

문서를 Markdown 헤더 기준으로 청크로 자르고, 너무 짧은 조각은 합치는 코드다.

```typescript
export function joinShortChunks(chunks: string[], minWords = 30): string[] {
  const result: string[] = [];
  let buffer = "";

  for (const chunk of chunks) {
    const wc = chunk.split(/\s+/).length;
    if (wc < minWords) {
      buffer += (buffer ? "\n\n" : "") + chunk;
      continue;
    }
    if (buffer) {
      result.push(buffer.trim());
      buffer = "";
    }
    result.push(chunk.trim());
  }
  if (buffer) result.push(buffer.trim());
  return result;
}
```

BM25 점수를 계산할 때는 LLM이 사용자 쿼리를 먼저 의미 단위 토큰으로 분해한 뒤, 그 토큰을 정규식으로 변환해 점수를 매긴다. 한국어 형태소 분석 라이브러리 없이도 동작한다는 점이 핵심이다.

## 이 단계에서 중요한 판단 기준

로컬 STDIO MCP 서버는 서버 비용 없이 문서 버전을 CDN에 위임할 수 있지만, 시작 시 전체 문서를 메모리에 올리므로 문서 양이 많아질수록 초기 로딩 비용이 커진다는 트레이드오프를 고려해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**MCP 서버로 공식 문서를 AI에게 직접 전달하면, AI가 생성하는 코드의 정확도와 보안 수준이 함께 올라간다.**

## 나중에 더 깊게 들어가면

- Vector DB 기반 시맨틱 검색과 BM25 키워드 검색의 하이브리드 전략
- 특정 키워드(예: Secret Key)에 가중치를 부여해 BM25 점수를 조정하는 방법
- 기술 문서와 블로그 포스트를 별도 도구로 분리해 검색 범위를 좁히는 설계

---

**원본:** [Making Payment Integration Easier with AI: Building Toss Payments MCP Server](https://memoryhub.tistory.com/868)
