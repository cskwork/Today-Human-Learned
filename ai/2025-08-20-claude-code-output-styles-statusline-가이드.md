# Claude Code Output Styles & Statusline 가이드

> **TL;DR**
> Claude Code의 출력 스타일(Output Style)과 상태 표시줄(Statusline)을 설정하면, AI 코딩 도구를 자신의 작업 방식에 맞게 조율할 수 있다.

---

## Output Styles를 왜 쓰는지 감 잡기

Claude Code는 코드를 생성할 때 "얼마나 설명할지", "어느 부분을 사용자가 직접 채울지"를 조절할 수 있다. 이 설정이 Output Style이다. 빠르게 결과만 필요한 상황과, 코드가 왜 이렇게 작성됐는지 배우고 싶은 상황은 다르다. 스타일을 바꾸면 같은 Claude Code가 전혀 다른 방식으로 응답한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 스타일 선택 → 프롬프트 입력 → 목적에 맞는 출력 형태`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Output Style | Claude Code가 응답을 구성하는 방식. "얼마나 설명할지"를 결정하는 설정. |
| Default | 설명 없이 코드만 빠르게 주는 기본 모드. 실무에서 주로 쓴다. |
| Explanatory | 코드 중간에 "왜 이렇게 했는지" 설명(Insights)을 끼워주는 모드. 코드 리뷰·학습용. |
| Learning | 일부 코드를 TODO로 비워두고 사용자가 직접 채우도록 유도하는 모드. 학습·협업용. |
| Statusline | Claude Code 화면 하단에 표시되는 정보 줄. 현재 모델, 디렉토리, Git 브랜치 등을 보여준다. |

## 예를 들어 설명하면

스타일 변경은 명령어 한 줄로 충분하다.

```bash
# 메뉴로 선택
/output-style

# 직접 지정
/output-style explanatory

# 커스텀 스타일 생성
/output-style:new I want an output style that ...
```

생성된 커스텀 스타일은 `~/.claude/output-styles/`에 저장되며 직접 편집할 수 있다. 프로젝트 단위로 고정하려면 `.claude/settings.local.json`에 기록한다.

Statusline은 셸 스크립트를 이용해 원하는 정보를 표시한다.

```bash
#!/bin/bash
input=$(cat)
MODEL=$(echo "$input" | jq -r '.model.display_name')
DIR=$(echo "$input" | jq -r '.workspace.current_dir')

if git rev-parse --git-dir > /dev/null 2>&1; then
    BRANCH=$(git branch --show-current 2>/dev/null)
    echo "[$MODEL] ${DIR##*/} | $BRANCH"
else
    echo "[$MODEL] ${DIR##*/}"
fi
```

`.claude/settings.json`에 다음과 같이 연결한다.

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh",
    "padding": 0
  }
}
```

## 이 단계에서 중요한 판단 기준

Output Style은 "지금 내가 결과를 빨리 받아야 하는가, 아니면 코드를 이해하며 배워야 하는가"로 고른다.

## 한 줄 요약 — 이것만 기억하면 된다

**출력 스타일은 Claude Code의 응답 밀도를 바꾸고, Statusline은 작업 맥락을 한눈에 보여준다.**

## 나중에 더 깊게 들어가면

- 커스텀 Output Style 파일의 구조와 직접 편집 방법
- Statusline에서 jq로 추출 가능한 전체 필드 목록
- claude-powerline 같은 외부 Statusline 프리셋 활용

---

**원본:** Claude Code Output Styles & Statusline 가이드 — [https://memoryhub.tistory.com/749](https://memoryhub.tistory.com/749)
