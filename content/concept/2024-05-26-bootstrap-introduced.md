+++
title = "Bootstrap — 반응형 웹을 빠르게 만드는 CSS 프레임워크"
date = "2024-05-26"
description = "Bootstrap은 그리드 시스템, 미리 만들어진 UI 컴포넌트, 유틸리티 클래스를 제공해 CSS를 거의 안 짜도 반응형 웹을 만들 수 있게 해주는 프론트엔드 프레임워크다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Bootstrap은 그리드 시스템, 미리 만들어진 UI 컴포넌트, 유틸리티 클래스를 제공해 CSS를 거의 안 짜도 반응형 웹을 만들 수 있게 해주는 프론트엔드 프레임워크다.

---

## Bootstrap을 왜 쓰는지 감 잡기

웹 페이지를 처음부터 만들면 버튼 하나, 네비게이션 바 하나에도 CSS를 수십 줄 써야 한다. 모바일 화면 대응까지 고려하면 코드량은 더 늘어난다. Bootstrap은 이런 반복 작업을 HTML 클래스 이름 몇 개로 해결한다. 전 세계에서 가장 많이 쓰이는 CSS 프레임워크 중 하나로, CDN 링크 한 줄로 바로 시작할 수 있다.

모바일을 먼저 설계하고 화면이 넓어질수록 레이아웃을 확장하는 "모바일 퍼스트(Mobile First)" 방식을 기본으로 한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: CDN 링크 추가 → HTML에 Bootstrap 클래스 적용 → 자동으로 반응형 스타일 적용`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 그리드 시스템(Grid System) | 페이지를 12칸으로 나눠 레이아웃을 정하는 체계. 화면 크기별로 칸 수를 조절 가능 |
| 반응형 디자인(Responsive Design) | 화면 크기(모바일/태블릿/데스크톱)에 따라 레이아웃이 자동으로 바뀌는 것 |
| 컴포넌트(Component) | 버튼, 모달, 네비게이션 바처럼 Bootstrap이 미리 스타일을 입혀 놓은 UI 요소 |
| 유틸리티 클래스(Utility Class) | `mt-3`(위 마진), `text-center`(가운데 정렬)처럼 한 가지 스타일만 담당하는 클래스 |
| 미디어 쿼리(Media Query) | CSS에서 화면 크기 조건에 따라 다른 스타일을 적용하는 기법. Bootstrap이 내부적으로 사용 |

## 예를 들어 설명하면

12칸 그리드를 기준으로 열을 나누는 방식이다.

```html
<div class="container">
  <div class="row">
    <!-- 각 열이 12칸 중 4칸을 차지 → 3등분 -->
    <div class="col-sm-4">1열</div>
    <div class="col-sm-4">2열</div>
    <div class="col-sm-4">3열</div>
  </div>
</div>
```

`col-sm-4`에서 `sm`은 소형 화면 이상에서 적용한다는 뜻이고, `4`는 12칸 중 4칸을 차지한다는 뜻이다. 화면이 더 작으면 열이 자동으로 세로로 쌓인다.

컴포넌트는 클래스만 붙이면 된다.

```html
<!-- 기본 버튼 -->
<button class="btn btn-primary">저장</button>

<!-- 가운데 정렬 + 위쪽 여백 -->
<div class="text-center mt-3">내용</div>
```

## 이 단계에서 중요한 판단 기준

커스텀 디자인이 많이 필요한 프로젝트라면 Bootstrap의 기본 스타일이 오히려 방해가 될 수 있다. 빠른 프로토타이핑이나 관리 도구처럼 디자인보다 기능이 우선인 경우에 가장 적합하다.

## 한 줄 요약 — 이것만 기억하면 된다

**Bootstrap은 12칸 그리드와 미리 만들어진 컴포넌트로 반응형 웹을 빠르게 만드는 도구이며, HTML 클래스 이름만으로 대부분의 레이아웃 문제를 해결한다.**

## 나중에 더 깊게 들어가면

- Bootstrap 5의 변경점: jQuery 의존성 제거, 유틸리티 API 강화
- Sass 변수로 Bootstrap 기본 색상과 브레이크포인트 커스터마이징하기
- Bootstrap Icons 라이브러리를 함께 사용하는 방법

---

**원본:** [Bootstrap Introduced — https://memoryhub.tistory.com/90](https://memoryhub.tistory.com/90)
