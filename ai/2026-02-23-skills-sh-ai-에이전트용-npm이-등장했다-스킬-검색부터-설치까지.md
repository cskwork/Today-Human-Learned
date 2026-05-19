# skills.sh: AI 에이전트용 npm이 등장했다

> **TL;DR**
> skills.sh는 Vercel이 만든 AI 에이전트용 패키지 매니저로, 마크다운으로 작성된 절차적 지식(스킬)을 검색·설치·공유할 수 있는 오픈 생태계다.

---

## skills.sh를 왜 쓰는지 감 잡기

AI 코딩 에이전트를 쓰다 보면 같은 지시를 반복하게 된다. "React 컴포넌트는 이 패턴으로 작성해줘", "코드 리뷰는 이 기준을 따라줘" 같은 문장을 매번 새 대화마다 입력하는 것은 비효율적이다. npm이 자바스크립트 패키지를 공유하는 표준 생태계가 된 것처럼, AI 에이전트의 "지식"에도 공유 생태계가 필요하다.

2026년 1월 Vercel이 출시한 skills.sh가 그 역할을 한다. 스킬은 프로그래밍 코드가 아니라 마크다운으로 작성된 자연어 지시문이다. 에이전트가 특정 작업을 수행할 때 참조할 컨텍스트 명령을 제공하는 방식으로, Claude Code, Cursor, GitHub Copilot, Gemini CLI 등 40개 이상의 에이전트에서 동일한 스킬을 사용할 수 있다.

`핵심 흐름: 스킬 검색(find) → 설치(add) → 에이전트가 자동 참조`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 스킬(Skill) | 마크다운으로 작성된 자연어 지시문. 에이전트가 특정 작업을 수행할 때 참조하는 컨텍스트 명령이다. |
| SKILL.md | 스킬의 핵심 파일. YAML 프론트매터에 이름과 설명, 본문에 자연어 지시사항을 작성한다. |
| MCP(Model Context Protocol) | 에이전트가 외부 도구(API, 데이터베이스)와 통신하는 표준 프로토콜. Skills와 보완 관계다. |
| 심볼릭 링크 | 파일 원본을 복사하지 않고 참조 경로만 만드는 방식. 스킬 업데이트 시 모든 에이전트에 자동 반영된다. |
| 프롬프트 인젝션 | 악성 스킬이 에이전트에게 의도치 않은 명령을 실행하게 만드는 보안 위협. |

## 예를 들어 설명하면

한국어 스킬을 설치하는 과정은 npm install과 거의 동일하다.

```bash
# 저장소에 어떤 스킬이 있는지 먼저 확인
$ npx skills add daleseo/korean-skills --list

# 특정 스킬만 설치 (humanizer: AI 텍스트를 자연스러운 한국어로 변환)
$ npx skills add daleseo/korean-skills@humanizer
```

설치 후 프로젝트 구조는 이렇게 된다.

```
프로젝트/
├── .agents/skills/humanizer/    ← 스킬 원본
│   └── SKILL.md
├── .claude/skills/              ← Claude Code용 심볼릭 링크
│   └── humanizer -> ../../.agents/skills/humanizer
└── .cursor/skills/              ← Cursor용 심볼릭 링크
    └── humanizer -> ../../.agents/skills/humanizer
```

MCP와의 차이를 한 줄로 정리하면: MCP는 에이전트에게 능력(ability)을 주고, Skills는 그 능력을 잘 사용하는 방법(how-to)을 알려준다. 둘은 경쟁이 아닌 보완 관계다.

## 이 단계에서 중요한 판단 기준

스킬은 에이전트의 전체 권한을 상속받는다. 파일 시스템, 환경 변수, API 키에 접근할 수 있다는 뜻이므로, Vercel, Anthropic, Apollo 등 공식 팀의 스킬을 우선 사용하고 출처 불분명한 스킬은 피해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**skills.sh는 AI 에이전트의 "작업 방식"을 패키지처럼 공유하는 생태계다. 믿을 수 있는 출처의 스킬부터 시작하고, 팀의 컨벤션을 스킬로 패키징해 재사용하는 것이 핵심 전략이다.**

## 나중에 더 깊게 들어가면

- SKILL.md 파일 작성 규칙과 팀 스킬 배포 방법
- `npx skills generate-lock`으로 팀 전체 스킬 버전 동기화하기
- Snyk 보안 스캔 결과로 본 안전한 스킬 선택 기준

---

**원본:** skills.sh: AI 에이전트용 npm이 등장했다 - 스킬 검색부터 설치까지 — [https://memoryhub.tistory.com/1039](https://memoryhub.tistory.com/1039)
