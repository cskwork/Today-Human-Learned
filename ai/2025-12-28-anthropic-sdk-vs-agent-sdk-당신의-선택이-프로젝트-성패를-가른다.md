# Anthropic SDK vs Agent SDK — 당신의 선택이 프로젝트 성패를 가른다

> **TL;DR**
> 추론 과정 노출이나 세밀한 스트리밍 제어가 필요하면 Direct SDK, 도구 기반 루프를 빠르게 구축하려면 Agent SDK, 둘 다 필요하면 조합하라.

---

## 두 SDK를 왜 구분해야 하는지 감 잡기

Claude로 AI 에이전트를 만들려고 npm을 열면 두 패키지가 눈에 들어온다. `@anthropic-ai/sdk`와 `@anthropic-ai/claude-agent-sdk`. "Agent가 붙었으니 더 좋은 거겠지?"라고 생각하면 며칠 밤을 허비하게 된다.

두 SDK는 상위/하위 관계가 아니다. 목적이 다른 도구다. Direct SDK는 Claude Messages API와 1:1로 통신하는 저수준 클라이언트다. Agent SDK는 그 위에 도구 실행 루프, 상태 관리, 재시도 로직을 얹은 고수준 프레임워크다.

비유하자면 Direct SDK는 수동 변속기, Agent SDK는 자동 변속기다. 수동은 세밀한 제어가 가능하지만 직접 처리할 것이 많다. 자동은 편하지만 특정 상황에서 원하는 대로 제어하기 어렵다.

`핵심 흐름: 요청 → [Direct SDK: 개발자가 직접 루프 처리] / [Agent SDK: SDK가 루프 자동 처리] → Claude 모델`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Direct SDK | Claude API에 직접 요청을 보내는 저수준 클라이언트 (`@anthropic-ai/sdk`) |
| Agent SDK | 도구 실행, 상태 관리, 재시도를 자동화한 고수준 프레임워크 (`@anthropic-ai/claude-agent-sdk`) |
| Extended Thinking | Claude가 답하기 전에 내부적으로 추론하는 과정 — Direct SDK에서만 접근 가능 |
| 도구 루프 | 도구 호출 결과를 Claude에 다시 전달하며 최종 답변이 날 때까지 반복하는 구조 |
| MCP | Model Context Protocol — Agent SDK에 내장된 외부 서버 연동 규격 |

## 예를 들어 설명하면

추론 과정을 사용자에게 보여줘야 하는 분석 도구를 만든다고 가정하자.

```typescript
// Direct SDK: thinking_delta를 직접 수신
const stream = client.messages.stream({
  model: "claude-sonnet-4-5-20250929",
  thinking: { type: "enabled", budget_tokens: 10000 },
  messages: [{ role: "user", content: "이 계약서의 위험 조항을 분석해줘" }]
});

for await (const event of stream) {
  if (event.delta?.type === "thinking_delta") {
    emitToClient("thinking", event.delta.thinking); // 추론 과정 실시간 표시
  } else if (event.delta?.type === "text_delta") {
    emitToClient("response", event.delta.text);
  }
}
```

반면 파일을 읽고 수정하는 에이전트가 필요하다면:

```typescript
// Agent SDK: 도구 루프를 자동 처리
const agent = new Agent({
  model: "claude-sonnet-4-5-20250929",
  tools: [readFileTool, writeFileTool],
});
const result = await agent.run({
  messages: [{ role: "user", content: "src/main.ts의 버그를 찾아서 수정해줘" }]
});
// SDK가 자동으로: 호출 → 도구 실행 → 결과 반환 → 반복
```

## 이 단계에서 중요한 판단 기준

기능 선택표를 보고 결정한다.

| 필요한 기능 | 선택 |
|---|---|
| Extended Thinking 스트리밍 노출 | Direct SDK |
| 도구 없는 단순 대화 | Direct SDK |
| 토큰 단위 정밀 비용 관리 | Direct SDK |
| 표준 도구 기반 에이전트 | Agent SDK |
| 빠른 프로토타이핑 | Agent SDK |
| 프로덕션 안정성 (내장 재시도) | Agent SDK |
| 추론 노출 + 도구 모두 필요 | 두 SDK 조합 |

Agent SDK는 Extended Thinking 블록에 접근할 수 없다. 이는 버그가 아니라 의도된 설계다. 단순성과 실행 효율성을 우선하기 때문이다.

## 한 줄 요약 — 이것만 기억하면 된다

**추론 과정을 보여줘야 하거나 제어가 필요하면 Direct SDK, 도구 루프 자동화가 핵심이면 Agent SDK, 둘 다라면 라우터로 나눠 조합하라.**

## 나중에 더 깊게 들어가면

- Agent SDK의 대화 상태 관리 내부 구조와 멀티턴 처리 방식
- Extended Thinking 토큰 예산 최적화 전략
- MCP 서버 연동으로 Agent SDK 확장하기

---

**원본:** Anthropic SDK vs Agent SDK, 당신의 선택이 프로젝트 성패를 가른다 — [https://memoryhub.tistory.com/949](https://memoryhub.tistory.com/949)
