# Claude Code Plugin: 팀 전체 AI 개발환경을 5분 만에 통일하는 법

> **TL;DR**
> Claude Code 플러그인 마켓플레이스는 슬래시 커맨드, 에이전트, MCP 서버를 JSON 파일 하나로 팀 전체에 배포하는 시스템이다.

---

## 플러그인 마켓플레이스를 왜 쓰는지 감 잡기

팀원마다 Claude Code 설정이 다르면 협업에서 혼란이 생긴다. 한 사람이 만든 유용한 슬래시 커맨드를 공유하려면 설정 파일을 복사하거나 문서로 안내해야 했다.

플러그인 마켓플레이스는 이 문제를 JSON 기반 카탈로그로 해결한다. 스마트폰 앱스토어처럼, 플러그인을 한 곳에 모아 `/plugin install` 명령 하나로 설치하고 팀 전체에 동일하게 적용할 수 있다. 2025년 10월 공개 베타 기준 커뮤니티 플러그인 243개 이상이 등록되어 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 마켓플레이스 JSON 작성 → Git 저장소 호스팅 → /plugin marketplace add → /plugin install`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 플러그인 | 슬래시 커맨드, 에이전트, MCP 서버, 훅을 묶은 설치 가능한 패키지 |
| 마켓플레이스 | 플러그인 목록과 설치 경로를 담은 JSON 파일. Git 저장소에 올려 공유한다. |
| MCP 서버 | Model Context Protocol 기반 외부 도구 연결 서버 |
| 훅 (Hook) | Claude Code 동작의 특정 시점(저장, 실행 전후 등)에 자동으로 실행되는 스크립트 |
| enabledPlugins | `.claude/settings.json`에 지정하면 팀원이 저장소를 열 때 자동으로 플러그인이 설치된다. |

## 예를 들어 설명하면

팀 공용 플러그인 마켓플레이스의 최소 구조는 다음과 같다.

```json
{
  "name": "team-tools",
  "owner": {
    "name": "DevOps Team",
    "email": "devops@example.com"
  },
  "plugins": [
    {
      "name": "code-formatter",
      "source": "./plugins/formatter",
      "description": "저장 시 자동 코드 포맷팅",
      "version": "2.1.0"
    }
  ]
}
```

이 파일을 Git 저장소에 올리면 팀원이 아래 명령 하나로 추가할 수 있다.

```
/plugin marketplace add your-org/claude-plugins
/plugin install code-formatter@team-tools
```

프로젝트 폴더에 자동 설치를 강제하려면 `.claude/settings.json`에 아래를 추가한다.

```json
{
  "extraKnownMarketplaces": {
    "team-tools": {
      "source": { "source": "github", "repo": "your-org/claude-plugins" }
    }
  },
  "enabledPlugins": ["code-formatter", "deployment-tools"]
}
```

## 이 단계에서 중요한 판단 기준

팀 표준화가 목적이라면 `settings.json`의 `enabledPlugins`를 저장소에 커밋하는 것이 가장 확실한 방법이다.

## 한 줄 요약 — 이것만 기억하면 된다

**마켓플레이스는 JSON 파일 하나로 팀 전체의 Claude Code 환경을 통일하는 가장 작은 단위의 표준화 도구다.**

## 나중에 더 깊게 들어가면

- 플러그인 내부 구조: `plugin.json` 스키마와 필드 정의
- MCP 서버 플러그인 작성 방법
- 비공개 Git 저장소에서 마켓플레이스를 호스팅할 때 접근 권한 설정

---

**원본:** Claude Code Plugin, 팀 전체 AI 개발환경을 5분 만에 통일하는 법 — https://memoryhub.tistory.com/924
