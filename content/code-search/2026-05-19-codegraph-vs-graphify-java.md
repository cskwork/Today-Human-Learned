+++
title = "codegraph vs graphify — Java 코드베이스에서 측정해본 결과 (2026-05-19)"
date = "2026-05-19"
description = "- 사내 Java/Spring(Lombok + DI) 5개 서비스에서 두 코드 인덱싱 도구를 비교했다. - **codegraph v0.6.8은 호출 그래프(callers/callees) 추출이 사실상 비어 있다.** Java unresolved refs 22,441건, 대표 메서드 콜러 0건 (실제 grep 결과 8건). graphify도 동일 테스트에서 0건이라 **양쪽 다 콜러 식별은 실패**. - 그럼에도 **속도 우위(MCP 네이티브 + 9배 빠른 탐색)** 때문에 codegraph로 전면"
tags = ["code-search"]
categories = ["code-search"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> - 사내 Java/Spring(Lombok + DI) 5개 서비스에서 두 코드 인덱싱 도구를 비교했다.
> - **codegraph v0.6.8은 호출 그래프(callers/callees) 추출이 사실상 비어 있다.** Java unresolved refs 22,441건, 대표 메서드 콜러 0건 (실제 grep 결과 8건). graphify도 동일 테스트에서 0건이라 **양쪽 다 콜러 식별은 실패**.
> - 그럼에도 **속도 우위(MCP 네이티브 + 9배 빠른 탐색)** 때문에 codegraph로 전면 교체하기로 했다. 단, **grep 폴백 빈도와 토큰 사용량을 모니터링**해 임계 초과 시 롤백.
> - 정적 분석 도구를 도입할 때 "벤치마크 숫자"보다 **"내가 매일 던지는 질문에 답하는가"를 직접 측정**해보는 게 결정적이다.

---

## 1. 왜 비교했나

LLM 코딩 에이전트(Claude Code, Cursor 등)가 다중 파일 리팩토링이나 "이 메서드 누가 호출하나?" 같은 질문을 받을 때, 매번 `grep -rn` 으로 풀리는 것보다 **사전 인덱스된 호출 그래프**가 있는 편이 토큰·시간 모두 유리하다.

후보 도구:
- **codegraph** — Rust 기반, tree-sitter 파서, MCP 서버 내장
- **graphify** — Node 기반, JavaParser/TypeScript compiler API 사용, CLI hook으로 통합

둘 다 "코드 → 노드/엣지 그래프"를 만들지만 **언어 파싱 깊이와 호출 해석 전략이 다르다.** 이 차이가 Java + Spring DI 환경에서 어떻게 드러나는지가 관전 포인트.

## 2. 측정 셋업

대상: **백엔드 5개 마이크로서비스** (Spring Boot 3.x, Java 17, Lombok 사용, MyBatis + JPA 혼용)
- 총 ~1,900 파일, ~20,000 노드 규모
- 가장 큰 서비스: 982 파일 / 11,282 노드

태스크 2종:
- **TASK-A: "JWT 인증 흐름 탐색"** — 시맨틱 컨텍스트 질의 (`codegraph context "JWT 인증 흐름"` 또는 graphify 동등 명령)
- **TASK-B: "메서드 X의 호출자 식별"** — Spring controller에서 호출되는 인증 유틸 메서드 콜러 추적

각 태스크를 LLM 에이전트 + CLI 양쪽으로 1회 실행, 시간과 정답률을 기록.

> 참고: 표본 N=1이라 절대값은 약하지만, **차수(order of magnitude) 비교**로는 충분하다는 판단.

## 3. 결과

| 항목 | codegraph | graphify | 우위 |
|---|---|---|---|
| TASK-A 탐색 시간 | 1.39s | 11.96s | codegraph 9배 |
| TASK-A 정확도 (관련 파일 회수) | ~2/7 | ~2.5/7 | 거의 동등 |
| **TASK-B 콜러 식별** | **0/8** | **0/8** | **양쪽 실패** |
| 인덱스 크기 (대표 서비스) | 25.89 MB | 8.4 MB | graphify 3배 작음 |
| 크로스 서비스 질의 | per-project DB | merged-graph.json 통합 | graphify |
| LLM 통합 방식 | MCP 네이티브 | CLI hook | codegraph |

**핵심 관찰**:
- **속도는 codegraph 압승.** MCP로 묶이면 LLM이 tool call 한 번에 답을 받는다.
- **콜러 식별은 양쪽 다 0.** 사용자 입장에서 가장 자주 필요한 질문에서 둘 다 못 답한다.
- **graphify는 크로스 서비스 머지**가 강점이지만, 그 그래프 자체가 부실하면 의미가 적다.

## 4. 결정적 발견 — codegraph의 Java 콜러 추출 결함

codegraph 인덱싱 후 산출되는 상태 메트릭:

```text
codegraph 0.6.8 Java 추출:
├─ unresolved_refs (java): 22,441건
├─ calls 엣지 비율: 3,834 / 14,209 (~27%)
├─ JwtAuthFilter callers: 0건 (실제 grep: 다수)
├─ getAuthUser callers: 0건 (실제 grep: 8건)
└─ getAuthUser callees: 1건 (실제 더 많음)
```

`unresolved_refs`가 2만 건 넘게 적체된다는 건 **파서가 `obj.method()` 형태의 invocation 대상을 거의 못 묶고 있다**는 신호다.

## 5. 왜 이렇게 동작하는가 — 가설 세 가지

확정은 아니다. 각 가설은 v0.7.x에서 재시험할 예정.

**가설 1: Lombok delombok 미적용.**
`@RequiredArgsConstructor` + `private final SomeService someService` 패턴에서, tree-sitter는 컴파일러가 아니라 **순수 텍스트 파서**다. 생성자가 텍스트로 존재하지 않으면 `someService.method()` 의 `someService` 타입을 해석할 단서가 없다. → 콜 해석 불가.

**가설 2: 빌드 산출물(classpath) 미주입.**
정통 호출 그래프 분석 도구는 보통 컴파일된 `.class` 또는 jdeps 산출물을 같이 본다. codegraph는 소스만 보는 것으로 보임. **타입 정보 없이 메서드 디스패치를 푸는 건 근본적으로 어렵다.**

**가설 3: tree-sitter Java 쿼리 미완성.**
v0.6.8은 호출 노드 캡처 규칙이 단순한 `MethodInvocation` 만 잡고, `MemberAccess → method` 체이닝이나 `this::` reference 등을 놓칠 가능성. 0.7.x changelog에 관련 작업이 보이면 가설 강화.

**왜 graphify도 0건?** 별도 가설이 필요한데, graphify는 JavaParser(컴파일러 수준) 기반이라 다를 줄 알았다. 추정: 인덱싱 시 의존성 jar를 같이 줘야 type resolution이 되는데, 그 단계를 생략한 채 돌렸을 가능성. (재시험 항목)

## 6. 그럼에도 codegraph 채택한 이유

보고서의 권고는 "graphify 유지"였지만, 실사용 판단으로 codegraph로 갔다. 근거:

| 결정 인자 | 비중 | 판단 |
|---|---|---|
| LLM tool call 수 (토큰 비용) | 높음 | MCP 네이티브 → ~94% 적은 tool call |
| 일상 질의 속도 (탐색) | 높음 | 9배 빠름 → 컨텍스트 윈도 보호 |
| 호출 그래프 정확도 | 높음 | **양쪽 다 0** → 결정 인자 아님 |
| 크로스 서비스 머지 | 중간 | per-project로 충분, 필요 시 union 가능 |
| 인덱스 크기 | 낮음 | 25MB도 작음 |
| 향후 개선 여지 | 중간 | codegraph는 활발히 릴리스 중 (v0.7 예정) |

**즉, "둘 다 못 답하는 질문이 있다면, 그건 grep 폴백으로 풀고 나머지에서 빠른 쪽을 쓴다"** 가 결론이었다.

## 7. 안전망 — 모니터링과 롤백 트리거

전면 교체는 위험하므로 **명시적 롤백 트리거**를 정해뒀다:

1. **호출 그래프 grep 폴백이 주 50회 초과** — 에이전트가 매번 우회한다면 도구가 무용지물
2. **LLM 월간 토큰 사용량이 baseline 대비 30% 이상 증가** — 속도 우위가 실제 비용 절감으로 안 이어진다는 신호
3. **rename 리팩토링 정확도 < 80%** — 콜러 0건의 실제 사고 비용

이 중 하나라도 발생하면 graphify 데이터(30일 보존 중)를 재활성화한다.

롤백 절차도 미리 적어두는 게 핵심이다:

```bash
# 1. Claude Code 세션 종료
# 2. 인덱스/CLI 제거
npm uninstall -g <codegraph-pkg>
rm -rf .codegraph/

# 3. MCP entry 제거 (~/.claude.json)
# 4. graphify 재활성화 (보존된 merged-graph.json 복구)
```

문서화한 복구 시간 추정: **10분**.

## 8. 보편 교훈

이번 비교에서 도구 외적으로 배운 것:

1. **벤치마크는 본인 워크플로우로 직접 만들어라.** 두 도구의 공식 README 벤치마크에는 "Java 호출 그래프 정확도"가 없다. 내 일상 질의(TASK-A/B)로 직접 측정해야 진짜 비교가 된다.

2. **"둘 다 못 함"도 결정 인자다.** 양쪽이 동일 항목에서 실패하면 그 항목은 선택 기준에서 제외되고, 다른 인자(속도, 통합 방식)의 가중치가 자동으로 올라간다.

3. **Lombok + DI는 정적 분석 도구의 천적이다.** 컴파일러 수준 파서가 아니면 거의 풀지 못한다. 도구 선택 시 "Lombok/DI를 어떻게 처리하나"를 먼저 묻자.

4. **결정과 롤백을 같이 적어라.** "채택" 만 적으면 사고 시 회복 불가. **트리거 + 보존 기간 + 복구 절차**까지 세트로 가야 안전한 결정이다.

5. **N=1 실험도 의사결정에는 충분할 수 있다.** 차수 차이가 크고, 트리거 모니터링이 붙어 있다면 정밀 측정 없이도 빠르게 움직일 수 있다.

## 9. 후속 검증 항목

- v0.7.x 출시 후 동일 메트릭 재측정 (`unresolved_refs`, callers 회수율)
- delombok 전처리 후 인덱싱했을 때의 차이
- 빌드 산출물(`.class`, jdeps 출력) 주입 옵션 검토
- 프런트엔드(TypeScript) 영역에서의 비교 — Java 결함이 TS에는 무관할 수 있다

---

**참고**
- codegraph: <https://github.com/colbymchenry/codegraph> (정확한 위치는 직접 검색)
- graphify: 동등한 OSS 도구군 (이름이 같은 패키지 다수, 본인 환경 확인 필요)
- tree-sitter Java grammar 한계에 대한 일반 논의: tree-sitter 공식 repo의 issue tracker
