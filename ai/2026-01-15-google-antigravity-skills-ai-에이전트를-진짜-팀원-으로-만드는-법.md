# Google Antigravity Agent Skills — AI 에이전트를 진짜 팀원으로 만드는 법

> **TL;DR**
> SKILL.md 파일 하나로 AI 에이전트에게 팀의 작업 규칙을 학습시키는 오픈 표준이다. 한 번 정의하면 Claude Code, Gemini CLI, Open Code에서 동일하게 작동한다.

---

## Agent Skills를 왜 쓰는지 감 잡기

AI 코딩 에이전트의 고질적인 문제는 기억력이다. 새 대화를 시작하는 순간 지난번에 설명한 배포 절차, 코드 리뷰 기준, 커밋 컨벤션을 모두 잊어버린다. 매번 같은 지시를 반복하거나 긴 시스템 프롬프트를 복사해 붙여야 했다.

Agent Skills는 이 문제를 "주문형 전문 지식 패키지" 방식으로 해결한다. 2025년 1월 Google Antigravity가 공식 채택했고, 그 원형은 Anthropic이 Claude Code에 먼저 도입한 포맷이다. 오픈 표준(agentskills.io)으로 공개되어 플랫폼을 가리지 않는다.

시스템 프롬프트는 항상 컨텍스트에 올라가 있어 토큰을 소비한다. Skills는 에이전트가 현재 작업과 관련 있다고 판단할 때만 전체 내용을 로드한다. 이 설계를 Progressive Disclosure(점진적 공개)라고 부른다. 수십 개 Skill을 등록해도 성능이 저하되지 않는 이유다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: SKILL.md 작성 → 에이전트가 이름·설명 스캔 → 관련 작업 감지 → 전체 내용 로드 → 지시 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SKILL.md | Skill의 필수 파일. YAML 프론트매터(이름, 설명)와 마크다운 본문(지시사항)으로 구성된다. |
| Progressive Disclosure | 에이전트가 필요한 Skill만 그때그때 로드하는 방식. 불필요한 토큰 낭비를 막는다. |
| description 필드 | 에이전트가 Skill을 언제 쓸지 판단하는 기준. 트리거 키워드를 명확히 포함해야 자동 활성화된다. |
| Workspace Scope | 프로젝트 폴더의 `.agent/skills/`에 두는 Skill. Git으로 팀 전체와 공유할 수 있다. |
| Global Scope | 홈 디렉터리의 `~/.gemini/antigravity/skills/`에 두는 Skill. 모든 프로젝트에서 개인이 쓴다. |

## 예를 들어 설명하면

커밋 메시지를 팀 컨벤션대로 강제하는 Skill을 만드는 흐름이다.

```
# 1. 폴더 생성
mkdir -p .agent/skills/git-commit-formatter

# 2. SKILL.md 작성
cat > .agent/skills/git-commit-formatter/SKILL.md << 'EOF'
---
name: git-commit-formatter
description: Formats git commit messages according to Conventional Commits.
  Use when user asks to commit changes or write a commit message.
---

## Format
<type>(<scope>): <description>

## Allowed Types
feat, fix, docs, style, refactor, test, chore

## Rules
- Type lowercase, description under 72 chars, no period at end
- feat(auth): add OAuth2 login support
- fix: resolve null pointer in user service
EOF
```

이후 에이전트에게 "커밋 메시지 작성해줘"라고 하면 Conventional Commits 형식을 자동으로 따른다.

## 이 단계에서 중요한 판단 기준

description 필드의 품질이 Skill 성패를 가른다 — AI가 읽기 쉽도록 트리거 상황을 명사·동사로 구체적으로 쓰고, "API 만들기 도움"처럼 모호하게 작성하면 자동 활성화가 제대로 되지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**SKILL.md 하나가 AI에게 팀 규칙을 가르치는 가장 작은 단위다.**

## 나중에 더 깊게 들어가면

- Rules, Workflows와 Skills의 로드 시점 차이 및 용도별 선택 기준
- 스크립트(deploy.sh 등)를 Skill에 포함해 원자적 자동화 구현하는 방법
- 여러 플랫폼(Gemini CLI, Open Code)에서 동일 Skill을 재사용하는 상호운용성 전략

---

**원본:** [Google Antigravity Skills, AI 에이전트를 '진짜 팀원'으로 만드는 법](https://memoryhub.tistory.com/970)
