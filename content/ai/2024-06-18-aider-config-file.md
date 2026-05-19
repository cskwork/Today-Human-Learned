+++
title = "Aider 설정 파일 — .aider.conf.yaml 이해하기"
date = "2024-06-18"
description = "`.aider.conf.yaml`은 Aider가 어떤 모델을 쓸지, 어떻게 동작할지를 코드 없이 선언하는 설정 파일이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `.aider.conf.yaml`은 Aider가 어떤 모델을 쓸지, 어떻게 동작할지를 코드 없이 선언하는 설정 파일이다.

---

## Aider 설정 파일을 왜 쓰는지 감 잡기

Aider는 터미널에서 LLM과 대화하며 코드를 편집해 주는 도구다. 매번 실행할 때마다 `--model gpt-4o --no-auto-commits` 같은 긴 옵션을 타이핑하는 건 비효율적이다. `.aider.conf.yaml`은 이 옵션들을 파일로 저장해 두는 방법이다. 홈 디렉토리나 git 저장소 루트에 놓으면 Aider가 시작할 때 자동으로 읽는다.

초보자는 처음에 이렇게 이해하면 된다.

`설정 파일 작성 → Aider 실행 → 파일 읽어 옵션 적용 → 대화 시작`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| `.aider.conf.yaml` | Aider의 동작 방식을 미리 정해 두는 설정 파일 |
| `model` | 대화에 사용할 LLM 이름 (예: gpt-4o, claude-3-opus) |
| `auto-commits` | 코드 수정 후 git commit을 자동으로 할지 여부 |
| `aiderignore` | Aider가 건드리지 않을 파일 목록을 담은 파일 경로 |
| `weak-model` | commit 메시지 생성 등 가벼운 작업에 쓰는 보조 모델 |

## 예를 들어 설명하면

실제로 많이 쓰는 최소 설정 예시다. 자동 커밋을 끄고, gitignore 연동을 켜고, Claude Opus를 기본 모델로 지정한다.

```yaml
# .aider.conf.yaml — 저장소 루트에 위치
model: claude-3-opus-20240229
auto-commits: false
gitignore: true
aiderignore: .aiderignore
```

이 파일 하나로 매 실행마다 옵션을 반복할 필요가 없어진다. `auto-commits: false`로 설정하면 Aider가 코드를 바꿔도 git commit은 사람이 직접 한다.

## 이 단계에서 중요한 판단 기준

`auto-commits`를 켤지 끌지가 핵심이다. 자동 커밋은 편하지만 LLM이 만든 변경을 검토 없이 이력에 남기므로, 처음에는 끄고 직접 확인하는 습관을 들이는 편이 안전하다.

## 한 줄 요약 — 이것만 기억하면 된다

**`.aider.conf.yaml`에 모델과 커밋 정책을 선언해 두면, 터미널 옵션 없이도 Aider가 일관되게 동작한다.**

## 나중에 더 깊게 들어가면

- `weak-model`과 메인 모델을 분리해 비용을 줄이는 방법
- `lint-cmd`, `test-cmd`로 코드 수정 후 자동 검증 파이프라인 구성하기
- `.aiderignore` 파일로 민감한 파일이나 대용량 파일을 AI 컨텍스트에서 제외하는 방법

---

**원본:** [Aider Config File — https://memoryhub.tistory.com/301](https://memoryhub.tistory.com/301)
