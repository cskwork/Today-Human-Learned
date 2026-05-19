# Claude Advanced Tool Use — 에이전트 개발의 패러다임이 바뀐다

> **TL;DR**
> Anthropic이 공개한 세 가지 베타 기능(Tool Search Tool, Programmatic Tool Calling, Tool Use Examples)으로 수천 개 도구를 토큰 낭비 없이 정확하게 사용할 수 있게 됐다.

---

## Advanced Tool Use를 왜 쓰는지 감 잡기

AI 에이전트에 MCP 서버를 5개만 연결해도 도구 정의만으로 55,000 토큰이 사라진다. GitHub 35개 도구에 26K, Slack 11개 도구에 21K. Anthropic 내부에서는 최적화 전 134K 토큰이 도구 정의에만 소비된 사례가 있었다.

토큰 낭비보다 더 큰 문제는 정확도다. `notification-send-user`와 `notification-send-channel`처럼 이름이 비슷한 도구가 많아지면 AI가 잘못된 도구를 선택한다. 중간 결과가 모두 컨텍스트에 쌓이면서 긴 작업일수록 실수가 늘어난다.

Anthropic이 이 문제를 세 방향으로 동시에 해결했다. 도구를 필요할 때 찾고(Tool Search Tool), 코드로 실행하며(Programmatic Tool Calling), 예시로 학습하는(Tool Use Examples) 방식이다. 세 기능 모두 2025년 11월 기준 베타 상태다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 도구 검색 → 필요한 것만 로드 → 코드로 오케스트레이션 → 결과만 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Tool Search Tool | AI가 모든 도구를 미리 외우지 않고, 필요할 때 검색해서 가져오는 기능 |
| defer_loading | 도구 정의를 처음부터 컨텍스트에 올리지 않고 나중에 불러오도록 설정하는 옵션 |
| Programmatic Tool Calling | AI가 여러 도구를 코드로 한 번에 묶어 실행하는 방식 — 중간 결과가 컨텍스트를 채우지 않음 |
| Tool Use Examples | 도구 정의에 실제 사용 예시를 포함시켜 AI가 날짜 형식, ID 컨벤션 등을 학습하게 하는 방법 |
| 베타 헤더 | 베타 기능을 API에서 활성화하기 위해 요청에 포함하는 특수 식별자 |

## 예를 들어 설명하면

세 기능 모두 API 호출 시 베타 헤더를 추가해야 한다.

```python
client.beta.messages.create(
    betas=["advanced-tool-use-2025-11-20"],
    model="claude-sonnet-4-5-20250929",
    max_tokens=4096,
    tools=[...]
)
```

Tool Search Tool은 도구 정의에 `defer_loading: true`를 추가하는 것만으로 적용된다. 내부 테스트에서 Opus 4.5의 정확도가 79.5%에서 88.1%로, 토큰 사용량은 77K에서 8.7K로 약 85% 감소했다.

Tool Use Examples는 도구 스키마에 실제 데이터를 담은 예시를 포함한다. "2024-11-06" 같은 구체적인 날짜 형식을 넣으면 AI가 패턴을 학습한다. 복잡한 파라미터 처리 정확도가 72%에서 90%로 향상됐다.

## 이 단계에서 중요한 판단 기준

도구 정의 토큰이 10K를 넘거나 도구 수가 10개를 초과한다면 Tool Search Tool 도입을 먼저 검토하고, 대용량 데이터 집계가 필요하면 Programmatic Tool Calling을 추가한다.

## 한 줄 요약 — 이것만 기억하면 된다

**도구를 미리 올리는 대신 필요할 때 찾고, 코드로 실행하면 토큰과 정확도 두 가지를 동시에 개선할 수 있다.**

## 나중에 더 깊게 들어가면

- `allowed_callers` 설정으로 Programmatic Tool Calling의 실행 범위를 제어하는 방법
- Tool Search Tool의 검색 알고리즘이 어떻게 관련 도구를 매칭하는지
- 베타 기능이 정식 출시될 때 API 인터페이스가 바뀔 가능성 대비 방법

---

**원본:** [Claude Advanced Tool Use 에이전트 개발의 패러다임이 바뀐다](https://memoryhub.tistory.com/914)
