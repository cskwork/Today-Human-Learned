+++
title = "Ripgrep(rg) — grep보다 빠른 코드 검색 도구"
date = "2025-09-02"
description = "Ripgrep은 .gitignore를 자동으로 인식하고 grep보다 수십 배 빠른 검색 속도를 제공하는 커맨드라인 도구다."
tags = ["linux"]
categories = ["linux"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Ripgrep은 .gitignore를 자동으로 인식하고 grep보다 수십 배 빠른 검색 속도를 제공하는 커맨드라인 도구다.

---

## Ripgrep을 왜 쓰는지 감 잡기

grep은 40년 넘은 유닉스 도구다. 여전히 유효하지만 현대 대규모 코드베이스에서는 느리고 node_modules, .git, 빌드 결과물 같은 불필요한 디렉터리까지 뒤진다. Ripgrep(rg)은 Rust로 작성되어 병렬 처리와 메모리 맵 I/O를 활용하며, .gitignore 규칙을 자동으로 적용해 관련 없는 파일은 건너뛴다. 10만 줄 이상의 프로젝트에서 검색 속도 차이가 체감된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: rg "패턴" 실행 → .gitignore 파일 자동 로드 → 관련 파일만 병렬 탐색 → 결과 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 정규표현식(Regex) | 패턴으로 문자열을 찾는 문법. `rg "user.*id"` 처럼 복잡한 패턴 검색에 쓴다 |
| .gitignore 연동 | rg는 Git이 무시하는 파일을 자동으로 검색에서 제외한다. 별도 설정 불필요 |
| 파일 타입 필터(-t) | `-t java`, `-t py` 처럼 언어 기준으로 검색 범위를 좁히는 옵션 |
| 컨텍스트 출력(-C) | 매칭된 줄 위아래 N줄을 함께 보여준다. 코드 흐름 파악에 유용 |
| 단어 단위 검색(-w) | `id`를 찾을 때 `userId`, `identifier`는 제외하고 정확히 `id`만 매칭 |

## 예를 들어 설명하면

macOS에서 설치하고 바로 쓸 수 있는 핵심 명령어들이다.

```bash
# 설치
brew install ripgrep

# 기본 사용법
rg "TODO"                          # 현재 디렉터리 전체에서 TODO 검색
rg -n "SQLException"               # 라인 번호 포함 출력
rg -i "error"                      # 대소문자 구분 없이 검색
rg -t java "UserService"           # Java 파일만 검색
rg -g "*.xml" "datasource"         # 특정 확장자만 검색
rg -C 3 "NullPointerException"     # 매칭 줄 위아래 3줄 출력
rg -l "password"                   # 파일 이름만 출력 (내용 제외)
rg -w "id"                         # 단어 단위 매칭
rg --hidden ".env" "SECRET"        # 숨김 파일(.env 등)도 포함
rg --stats "BusinessException"     # 검색 후 파일 수·매치 수 통계 출력
```

## 이 단계에서 중요한 판단 기준

rg를 기본 검색 도구로 쓰되, .gitignore를 무시하고 전부 뒤져야 할 때만 `rg -uuu "패턴"`으로 전환하면 된다.

## 한 줄 요약 — 이것만 기억하면 된다

**Ripgrep은 .gitignore를 자동으로 적용하고 병렬 처리로 빠르게 동작하는 grep 대체 도구이며, `brew install ripgrep` 한 줄로 시작할 수 있다.**

## 나중에 더 깊게 들어가면

- `rg --json` 출력을 파싱해 IDE나 스크립트와 연동하기
- fzf와 rg를 조합한 퍼지 파일 검색 설정
- `.rgignore` 파일로 프로젝트별 검색 제외 규칙 관리하기

---

**원본:** [개발자 필수 툴, Ripgrep(rg) 10가지 활용법 + macOS 설치 방법 — https://memoryhub.tistory.com/772](https://memoryhub.tistory.com/772)
