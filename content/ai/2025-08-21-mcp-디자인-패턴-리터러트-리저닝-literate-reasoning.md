+++
title = "MCP 디자인 패턴: 리터러트 리저닝(Literate Reasoning)"
date = "2025-08-21"
description = "AI 에이전트의 작업 과정을 주피터 노트북처럼 셀 단위로 기록하면, 블랙박스였던 에이전트 행동이 추적·재현 가능한 투명한 흐름으로 바뀐다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> AI 에이전트의 작업 과정을 주피터 노트북처럼 셀 단위로 기록하면, 블랙박스였던 에이전트 행동이 추적·재현 가능한 투명한 흐름으로 바뀐다.

---

## 리터러트 리저닝을 왜 쓰는지 감 잡기

AI 에이전트에게 복잡한 작업을 시키면 결과만 나온다. 어떤 판단을 거쳐 그 결과에 도달했는지는 알 수 없다. 디버깅도 어렵고, 같은 결과를 다시 재현하기도 힘들다.

리터러트 리저닝(Literate Reasoning)은 이 문제를 "노트북 방식"으로 푼다. 에이전트가 생각하고 실행하고 검증하는 각 단계를 셀(cell) 단위로 남긴다. MCP(Model Context Protocol) 위에서 이 패턴을 구현하면, 에이전트 행동이 감사(audit)·재현·온보딩에 활용 가능한 구조로 남는다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 마크다운 셀(목표 기술) → 코드 셀(실행) → 결과 셀(검증)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP (Model Context Protocol) | AI 앱과 외부 도구·데이터를 표준 방식으로 연결하는 규격. "AI의 USB-C"라고 불린다. |
| 리터러트 리저닝 | 에이전트의 사고·실행·결과를 셀 단위로 기록해 투명하게 만드는 MCP 설계 패턴. |
| 마크다운 셀 | 작업 목표, 맥락, 체크리스트를 사람이 읽을 수 있게 적는 셀. |
| 코드 셀 | 에이전트가 실제로 실행하는 명령·코드를 담는 셀. 결과가 바로 아래에 기록된다. |
| 노트북 프리셋 | 자주 쓰는 마크다운+코드 셀 묶음을 재사용 가능한 레시피로 만든 것. |

## 예를 들어 설명하면

MCP 서버가 UI 리소스를 생성하고, 클라이언트가 이를 렌더링해 인터랙티브 노트북을 만드는 방식이다.

```typescript
// 서버 측: UI 리소스(노트북 셀 뷰) 생성
import { createUIResource } from '@mcp-ui/server';

const htmlResource = createUIResource({
  uri: 'ui://notebook/cell-1',
  content: {
    type: 'rawHtml',
    htmlString: `
      <section>
        <h3>Step 1: 문제 정의</h3>
        <p>목표와 제약을 정리하세요.</p>
        <button id="go">다음 셀 실행</button>
        <script>
          document.getElementById('go').addEventListener('click', () => {
            window.parent.postMessage(
              { type: 'tool', payload: { toolName: 'runNextCell' } }, '*'
            );
          });
        </script>
      </section>
    `
  },
  encoding: 'text',
});
```

버튼 클릭이 MCP 툴 호출로 이어지는 구조다. 클라이언트는 `UIResourceRenderer`로 렌더링하고 `onUIAction` 핸들러에서 툴 실행 로직을 연결한다.

## 이 단계에서 중요한 판단 기준

에이전트 작업의 과정을 나중에 다시 볼 일이 있다면(디버깅, 감사, 재현) 리터러트 리저닝 패턴을 쓴다. 결과만 필요하다면 일반 프롬프트로 충분하다.

## 한 줄 요약 — 이것만 기억하면 된다

**에이전트 행동을 노트북 셀로 쪼개면, 블랙박스가 추적 가능한 흐름으로 바뀐다.**

## 나중에 더 깊게 들어가면

- mcp-ui 라이브러리의 전체 리소스 타입과 보안 정책
- 노트북 프리셋을 Git으로 버전 관리하는 체크포인트 전략
- 셀 실행 로그를 감사 추적(audit trail)으로 보관하는 방법

---

**원본:** MCP 디자인 패턴: "리터러트 리저닝(Literate Reasoning)" — [https://memoryhub.tistory.com/755](https://memoryhub.tistory.com/755)
