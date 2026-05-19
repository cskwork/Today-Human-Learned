# iTerm2 세팅 가이드: Claude Code 터미널 환경 구축

> **TL;DR**
> iTerm2 + Oh My Zsh + 플러그인 3종으로 터미널을 제대로 세팅하면, AI 코딩 도구의 생산성이 체감상 두 배 가까이 올라간다.

---

## iTerm2를 왜 쓰는지 감 잡기

macOS 기본 Terminal.app은 간단한 명령 실행에는 충분하다. 하지만 Claude Code처럼 긴 작업을 돌리고, 여러 AI 세션을 동시에 띄우는 작업에는 금방 한계가 드러난다.

세 가지가 문제다. 작업이 끝나도 알림이 없어서 탭을 계속 확인해야 하고, 화면 분할이 불편해 병렬 세션 운영이 어렵고, 긴 출력을 스크롤하거나 과거 명령을 검색하는 기능이 빈약하다.

iTerm2는 이 세 문제를 모두 해결하는 오픈소스 터미널 에뮬레이터다.

`핵심 흐름: iTerm2 설치 → Oh My Zsh → Powerlevel10k → 플러그인 → Claude Code 전용 설정`

| 비교 항목 | Terminal.app | iTerm2 |
|---|---|---|
| 화면 분할 | 불가 | 수직/수평 자유 분할 |
| 시스템 알림 | 미지원 | 작업 완료 알림 지원 |
| 핫키 윈도우 | 없음 | 전역 단축키로 즉시 호출 |
| 스크롤백 | 제한적 | 무제한 설정 가능 |
| Shell Integration | 없음 | 명령어 추적, 디렉토리 기록 |

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 터미널 에뮬레이터 | 컴퓨터에게 명령을 텍스트로 전달하는 창 |
| Oh My Zsh | Zsh 셸의 설정을 쉽게 관리하는 프레임워크 |
| Powerlevel10k | 현재 Git 브랜치·오류 코드를 프롬프트에 실시간 표시하는 테마 |
| 플러그인 | 터미널에 기능을 추가하는 확장 모듈 |
| 핫키 윈도우 | 어떤 앱에서든 단축키 하나로 터미널을 즉시 불러오는 기능 |

## 예를 들어 설명하면

Claude Code로 백엔드와 프론트엔드를 동시에 개발할 때, iTerm2를 이렇게 쓴다.

- 탭 1: Claude Code로 백엔드 API 작성 중
- 탭 2: Claude Code로 프론트엔드 컴포넌트 작성 중
- 탭 3: 테스트 실행 모니터링

각 탭에서 Claude가 긴 작업을 끝내면 macOS 알림이 뜬다. 다른 작업을 하다가도 완료 시점을 정확히 알 수 있다.

설치 순서는 아래와 같다.

```bash
# 1. iTerm2 설치
brew install --cask iterm2

# 2. Oh My Zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 3. Powerlevel10k 설치
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git \
  ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

# 4. 필수 플러그인 3종
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
brew install autojump
```

`.zshrc`에서 플러그인을 활성화한다.

```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting autojump)
```

Claude Code 전용 설정으로 작업 완료 알림을 추가하려면 `.claude/settings.json`에 아래를 넣는다.

```json
{
  "hooks": [{
    "matcher": "Notification",
    "hooks": [{"type": "command",
      "command": "osascript -e 'display notification \"Task complete\" with title \"Claude Code\"'"}]
  }]
}
```

## 이 단계에서 중요한 판단 기준

설치보다 설정이 중요하다. 무제한 스크롤백, 시스템 알림, Option 키(Esc+ 모드), 핫키 윈도우 이 네 가지를 반드시 확인하라.

## 한 줄 요약 — 이것만 기억하면 된다

**iTerm2는 예쁜 터미널이 아니라 AI 코딩 도구의 성능을 극대화하는 인프라다.**

## 나중에 더 깊게 들어가면

- tmux 통합으로 SSH 세션 유지 및 원격 작업 환경 구성
- CLAUDE.md를 활용한 프로젝트별 AI 컨텍스트 관리
- fzf, bat, lsd 등 CLI 보조 도구로 터미널 경험 확장

---

**원본:** [iTerm2 완벽 세팅 가이드: Claude Code 터미널 환경 구축](https://memoryhub.tistory.com/1012)
