+++
title = "Windows에서 Aider 설치하기"
date = "2024-06-11"
description = "Git과 Python이 설치된 Windows 환경에서 pip 명령 하나로 Aider를 설치하고, OpenAI 또는 Anthropic API 키를 넘겨주면 바로 쓸 수 있다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Git과 Python이 설치된 Windows 환경에서 pip 명령 하나로 Aider를 설치하고, OpenAI 또는 Anthropic API 키를 넘겨주면 바로 쓸 수 있다.

---

## Aider를 왜 쓰는지 감 잡기

Aider는 터미널에서 GPT/Claude와 대화하면서 실제 프로젝트 파일을 직접 수정해 주는 AI 코딩 도구다. IDE 플러그인 없이도 "이 함수 리팩토링해줘"라고 입력하면 모델이 코드를 수정하고 git commit까지 자동으로 처리한다.

Windows에서 설치는 세 단계다. 전제 조건(Git, Python) 확인 → API 키 준비 → pip 설치.

핵심 흐름: `Git 설치 확인 → API 키 발급 → pip install aider-chat → 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Aider | 터미널에서 LLM과 대화하며 코드 파일을 직접 수정해 주는 오픈소스 도구 |
| pip | Python 패키지 설치 관리자. `pip install <패키지명>`으로 도구를 받아온다 |
| API 키 | OpenAI나 Anthropic 서버에 요청할 때 신원을 증명하는 문자열. 유료 계정 필요 |
| aider-chat | pip에 등록된 Aider 패키지 이름 |
| --opus / --sonnet | Anthropic Claude 모델을 지정하는 Aider 실행 옵션 |

## 예를 들어 설명하면

아래는 Windows 커맨드 프롬프트(cmd)에서 실행하는 전체 흐름이다.

```bat
:: 1. Python 버전 확인 (3.9 이상 권장)
py --version

:: 2. Aider 설치
py -m pip install aider-chat

:: 3a. OpenAI 모델로 실행
aider --openai-api-key sk-xxxxxxxxxxxx

:: 3b. Anthropic Claude 모델로 실행
aider --anthropic-api-key sk-ant-xxxxxxxxxxxx --opus
```

실행 후 터미널에 프롬프트가 뜨면 파일 이름과 함께 자연어로 작업을 지시하면 된다.

## 이 단계에서 중요한 판단 기준

API 키는 유료 계정이 필요하다. 무료 티어로는 동작하지 않는다. 설치 전 OpenAI 또는 Anthropic 계정의 결제 수단 등록 여부를 먼저 확인한다.

## 한 줄 요약 — 이것만 기억하면 된다

**`py -m pip install aider-chat` 한 줄과 API 키 하나면 Windows에서 Aider를 바로 쓸 수 있다.**

## 나중에 더 깊게 들어가면

- Aider의 `/add`, `/drop` 명령으로 컨텍스트에 파일을 동적으로 추가/제거하는 방법
- `.aider.conf.yml`로 API 키와 모델을 환경별로 관리하는 방법
- WSL2 환경에서 Aider를 설치할 때 차이점

---

**원본:** [Install Aider GPT In Windows — https://memoryhub.tistory.com/262](https://memoryhub.tistory.com/262)
