# Chrome 확장 프로그램 개발 — 실습 중심 입문

> **TL;DR**
> Chrome 확장 프로그램은 텍스트 에디터와 브라우저만으로 시작할 수 있고, "Hello, world!" 수준의 팝업을 10분 안에 만들 수 있다.

---

## Chrome 확장 프로그램을 왜 쓰는지 감 잡기

확장 프로그램은 Chrome이 제공하는 고수준 API 위에 올라타는 미니 웹앱이다. 일반 웹페이지와 달리 브라우저 탭, 북마크, 네트워크 요청, 시스템 알림까지 다룰 수 있다. 설치는 JavaScript·HTML·CSS 파일 몇 개로 충분하고, 외부 프레임워크 없이도 동작한다.

확장 프로그램을 구성하는 세 가지 영역을 구분하는 것이 핵심이다. 백그라운드(서비스 워커)는 브라우저 이벤트를 처리하고, 콘텐츠 스크립트는 웹페이지 안에서 실행되며, 팝업은 사용자와 직접 상호작용하는 UI다. 이 세 영역은 메시지 패싱으로 데이터를 주고받는다.

`핵심 흐름: manifest.json 작성 → 파일 생성 → chrome://extensions에서 로드 → DevTools로 디버그`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| manifest.json | 확장 프로그램이 무엇인지, 어떤 파일을 쓰는지 Chrome에 알리는 설정 파일. |
| 백그라운드 스크립트 | 페이지와 무관하게 브라우저 뒤에서 실행되는 이벤트 처리기. |
| 콘텐츠 스크립트 | 특정 웹페이지에 주입되어 그 페이지의 DOM을 읽고 수정하는 스크립트. |
| 메시지 패싱 | 콘텐츠 스크립트와 백그라운드 스크립트가 데이터를 주고받는 통신 방식. |
| DevTools | Chrome 내장 개발자 도구. 팝업, 서비스 워커, 콘텐츠 스크립트 각각 별도로 검사할 수 있다. |

## 예를 들어 설명하면

"Hello, world!" 확장 프로그램을 만드는 최소 파일 셋이다.

```json
// manifest.json
{
  "manifest_version": 3,
  "name": "My First Extension",
  "version": "1.0",
  "action": {
    "default_popup": "popup.html"
  }
}
```

```html
<!-- popup.html -->
<!DOCTYPE html>
<html>
  <body>
    <script src="popup.js"></script>
  </body>
</html>
```

```javascript
// popup.js
alert("Hello, world!");
```

세 파일을 한 폴더에 저장 → `chrome://extensions` → 개발자 모드 → "압축 해제된 확장 프로그램 로드" → 폴더 선택. 툴바 아이콘을 클릭하면 팝업이 열리면서 알림이 뜬다.

## 이 단계에서 중요한 판단 기준

콘텐츠 스크립트가 필요한 작업인지, 백그라운드에서 처리해야 하는 작업인지 먼저 구분한다 — 영역을 혼용하면 권한 오류와 디버그 어려움이 생긴다.

## 한 줄 요약 — 이것만 기억하면 된다

**확장 프로그램 개발은 manifest.json 세 줄로 시작하고, 세 가지 영역(백그라운드, 콘텐츠 스크립트, 팝업)의 역할을 분리하는 것이 설계의 핵심이다.**

## 나중에 더 깊게 들어가면

- `chrome.runtime.sendMessage` / `onMessage`로 영역 간 통신 구현
- React, Vue를 이용한 팝업 UI 개발
- Chrome 웹 스토어 게시 절차 및 심사 기준

---

**원본:** [Chrome Extension v2 — https://memoryhub.tistory.com/423](https://memoryhub.tistory.com/423)
