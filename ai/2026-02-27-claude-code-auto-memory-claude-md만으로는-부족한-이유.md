# Claude Code Auto Memory, CLAUDE.md만으로는 부족한 이유

> **TL;DR**
> CLAUDE.md는 개발자가 쓰는 지시서고, Auto Memory는 Claude가 작업하면서 스스로 채워가는 학습 노트다. 둘은 역할이 다르고, 함께 써야 세션 간 맥락이 제대로 유지된다.

---

## CLAUDE.md의 한계를 감 잡기

LLM은 상태를 갖지 않는다. 세션이 끝나면 대화에서 나온 모든 정보가 사라진다. 이 문제를 많은 Claude Code 사용자는 CLAUDE.md로 해결해왔다. 프로젝트 루트에 마크다운 파일을 두면 매 세션 시작 시 자동으로 로드되기 때문이다.

그런데 CLAUDE.md에는 구조적 한계가 있다. 내가 미리 알고 있는 것만 적을 수 있다. 작업 도중 발견한 디버깅 패턴, 코드베이스의 숨은 규칙 같은 것들은 그때그때 수동으로 기록해야 한다. 대부분 잊어버린다.

Auto Memory는 Claude가 작업 중 발견한 패턴과 인사이트를 자동으로 기록하는 영속 디렉토리다. Claude Code 2.1.32부터 기본 활성화되어 있다.

핵심 흐름: `작업 발견 → Claude가 자동 기록 → 다음 세션에서 자동 로드`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| CLAUDE.md | 개발자가 직접 쓰는 지시서. 팀 공유 가능하고 Git에 커밋한다. |
| Auto Memory | Claude가 작업하면서 스스로 채우는 학습 노트. 로컬 전용이다. |
| MEMORY.md | Auto Memory의 핵심 인덱스 파일. 매 세션 시작 시 첫 200줄만 자동 로드된다. |
| 토픽 파일 | MEMORY.md가 너무 길어지면 분리해두는 상세 노트. on-demand로만 읽힌다. |
| 200줄 제한 | MEMORY.md에서 세션 시작 시 자동 로드되는 최대 분량. 초과 시 경고가 뜬다. |

## 예를 들어 설명하면

프로젝트에서 pnpm 대신 npm을 쓰면 빌드가 깨진다고 하자. 이 사실을 Claude가 오늘 작업 중 발견했다면 MEMORY.md에 자동으로 기록된다. 내일 새 세션을 열면 Claude는 이미 그 규칙을 알고 있다.

```
~/.claude/projects/<project>/memory/
├── MEMORY.md           # 핵심 인덱스 (매 세션 자동 로드)
├── debugging.md        # 디버깅 패턴 상세 노트
└── api-conventions.md  # API 설계 결정사항
```

MEMORY.md가 200줄을 넘으면 Claude가 경고를 표시한다. 이때 상세 내용을 debugging.md 같은 토픽 파일로 옮기고, MEMORY.md는 간결한 인덱스로 유지하는 것이 올바른 구조다. 토픽 파일은 Claude가 필요할 때만 직접 열어 읽는다.

## 이 단계에서 중요한 판단 기준

CLAUDE.md에는 팀 전체가 따라야 할 규칙을, Auto Memory에는 Claude가 이 프로젝트를 작업하며 스스로 발견한 것들을 담는다. 두 파일의 내용이 충돌하면 더 구체적인 쪽이 이긴다.

## 한 줄 요약 — 이것만 기억하면 된다

**CLAUDE.md는 내가 Claude에게 쓰는 지시서고, Auto Memory는 Claude가 자기 자신을 위해 쓰는 학습 노트다. 둘은 보완 관계다.**

## 나중에 더 깊게 들어가면

- Git worktree 환경에서 Auto Memory가 분리되는 방식
- 메모리 계층 4단계(전역 / 프로젝트 / 모듈 규칙 / Auto Memory) 우선순위 충돌 처리
- CI 파이프라인에서 Auto Memory를 환경 변수로 비활성화하는 이유

---

**원본:** [Claude Code Auto Memory, CLAUDE.md만으로는 부족한 이유](https://memoryhub.tistory.com/1044)
