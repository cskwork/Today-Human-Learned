+++
title = "Tauri: 데스크톱 앱 프레임워크의 새로운 기준"
date = "2025-06-29"
description = "Tauri는 Electron에서 Chromium 엔진을 빼고 Rust 백엔드와 OS 기본 웹뷰로 대체해, 번들 크기를 90% 줄이고 메모리를 절반으로 낮춘 데스크톱 앱 프레임워크다."
tags = ["tool"]
categories = ["tool"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Tauri는 Electron에서 Chromium 엔진을 빼고 Rust 백엔드와 OS 기본 웹뷰로 대체해, 번들 크기를 90% 줄이고 메모리를 절반으로 낮춘 데스크톱 앱 프레임워크다.

---

## Tauri를 왜 쓰는지 감 잡기

데스크톱 앱을 웹 기술로 만들 때 가장 흔히 선택하는 도구가 Electron이다. Electron은 Chromium(Chrome의 핵심 엔진)을 앱 안에 통째로 묶어서 배포한다. 그래서 간단한 메모장 앱도 설치 파일이 85MB를 넘고, 실행하면 메모리를 수백 MB씩 잡아먹는다.

Tauri는 이 구조를 뒤집었다. Chromium을 내장하는 대신, 이미 운영체제에 설치된 웹뷰(Windows는 WebView2, macOS는 WebKit)를 빌려서 쓴다. 앱 로직은 Rust로 작성한다. 결과적으로 같은 기능의 앱이 2~10MB 안에 들어온다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: HTML/JS 화면 → Tauri IPC → Rust 백엔드 → OS 시스템 호출`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 웹뷰(WebView) | OS가 기본 제공하는 미니 브라우저 창. Tauri가 여기에 앱 화면을 띄운다. |
| IPC (Inter-Process Communication) | 화면(JS)과 백엔드(Rust)가 메시지를 주고받는 통로. |
| Command | 프론트엔드가 Rust 함수를 호출하는 방식. REST API와 비슷하게 생겼다. |
| WRY | Tauri 내부에서 플랫폼마다 다른 웹뷰를 하나의 인터페이스로 감싸주는 라이브러리. |
| 번들(Bundle) | 사용자에게 배포하는 설치 파일. Tauri는 NSIS/dmg/deb 등을 자동 생성한다. |

## 예를 들어 설명하면

파일 내용을 읽어서 화면에 보여주는 앱을 만든다고 가정하자. Rust 백엔드에 Command를 하나 정의하고, JS에서 호출한다.

```rust
#[tauri::command]
async fn read_file(path: String) -> Result<String, String> {
    tokio::fs::read_to_string(path).await.map_err(|e| e.to_string())
}
```

프론트엔드(React, Vue 등 무엇이든)에서는 `invoke('read_file', { path: '/tmp/data.txt' })`로 이 함수를 부른다. 파일 시스템 접근은 Rust가 담당하고, 화면 렌더링은 OS 웹뷰가 담당하는 역할 분리가 명확하다.

## 이 단계에서 중요한 판단 기준

Tauri를 선택하는 핵심 이유는 두 가지다. 배포 파일 크기가 제약이 되거나, 메모리 효율이 중요한 경우다. 반대로 Rust를 전혀 쓰지 않을 팀이라면 학습 비용을 먼저 따져야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**OS 웹뷰 + Rust 백엔드라는 조합으로, Electron 대비 90% 작고 절반의 메모리로 데스크톱 앱을 만들 수 있다.**

## 나중에 더 깊게 들어가면

- Tauri의 Capability 시스템과 ACL로 API 권한을 세밀하게 제어하는 법
- Tauri 2.0에서 추가된 iOS/Android 모바일 지원 방식
- CI/CD(GitHub Actions)에서 Windows/macOS/Linux 크로스 컴파일 자동화

---

**원본:** [Tauri: The Desktop App Framework Revolution — https://memoryhub.tistory.com/715](https://memoryhub.tistory.com/715)
