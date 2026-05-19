# Angular & OnsenUI — 모바일 앱을 만드는 두 가지 도구

> **TL;DR**
> Angular는 웹 앱의 구조를 잡아주는 프레임워크이고, OnsenUI는 그 위에서 모바일 UI 컴포넌트를 제공하는 라이브러리다.

---

## Angular & OnsenUI를 왜 쓰는지 감 잡기

웹 기술로 모바일 앱처럼 보이는 화면을 만들고 싶을 때 두 가지 고민이 생긴다. 첫째, 데이터와 화면을 어떻게 연결할 것인가. 둘째, 스마트폰 앱처럼 보이는 UI를 처음부터 직접 만들어야 하는가.

Angular는 첫 번째 문제를 해결한다. 데이터(Model)가 바뀌면 화면(View)이 자동으로 업데이트되는 구조를 제공한다. OnsenUI는 두 번째 문제를 해결한다. 네이티브 앱처럼 보이는 버튼, 툴바, 페이지 전환 애니메이션을 미리 만들어 제공한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 데이터(Scope) → Angular 처리 → 화면(Template) + OnsenUI 스타일`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Scope | 컨트롤러와 화면 사이의 데이터 공유 공간. 컨트롤러에서 값을 바꾸면 화면에 바로 반영된다. |
| Directive | `*ngFor`, `*ngIf` 같이 HTML에 붙이는 특별한 명령어. Angular가 이걸 읽고 화면을 조작한다. |
| Component | 화면 한 조각을 담당하는 단위. 자체 데이터와 로직을 가진다. |
| Interpolation | `{{ 변수명 }}`처럼 중괄호 두 개로 데이터를 HTML 텍스트 안에 삽입하는 문법. |
| Navigator (OnsenUI) | 페이지를 스택으로 관리하는 OnsenUI 컴포넌트. push로 새 페이지를 열고 pop으로 되돌아간다. |

## 예를 들어 설명하면

할일 목록 앱을 만든다고 가정한다. Angular는 할일 데이터를 관리하고, OnsenUI는 화면을 스마트폰 앱처럼 보이게 한다.

```html
<!-- OnsenUI 페이지 구조 -->
<ons-page>
  <ons-toolbar>
    <div class="center">할일 목록</div>
  </ons-toolbar>

  <!-- Angular directive로 목록 렌더링 -->
  <ul>
    <li *ngFor="let item of todos">{{ item }}</li>
  </ul>
</ons-page>
```

`*ngFor`는 Angular가 배열을 순회하며 `<li>`를 반복 생성하는 명령이다. `ons-page`와 `ons-toolbar`는 OnsenUI가 제공하는 모바일 UI 뼈대다.

## 이 단계에서 중요한 판단 기준

Angular와 OnsenUI를 함께 쓸 때는, 화면 로직(데이터 처리)은 Angular에, UI 외형(스타일, 애니메이션)은 OnsenUI에 맡기는 역할 분리를 유지하는 것이 핵심이다.

## 한 줄 요약 — 이것만 기억하면 된다

**Angular가 데이터를 화면에 연결하고, OnsenUI가 그 화면을 모바일 앱처럼 꾸며준다.**

## 나중에 더 깊게 들어가면

- Angular의 데이터 바인딩 방식 (단방향 vs 양방향)
- OnsenUI Navigator의 push/pop 전환 애니메이션 커스터마이징
- Angular 모듈 시스템과 컴포넌트 트리 구조

---

**원본:** [Angular & OnsenUI — https://memoryhub.tistory.com/31](https://memoryhub.tistory.com/31)
