+++
title = "Express.js 템플릿 엔진 — 서버가 HTML을 만드는 방식"
date = "2024-06-11"
description = "템플릿 엔진은 서버에서 데이터와 HTML 골격을 합쳐 완성된 페이지를 만들어 브라우저에 보내는 도구다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 템플릿 엔진은 서버에서 데이터와 HTML 골격을 합쳐 완성된 페이지를 만들어 브라우저에 보내는 도구다.

---

## 템플릿 엔진을 왜 쓰는지 감 잡기

HTML 파일을 사용자마다 따로 만들 수는 없다. 회원 이름, 주문 목록, 날짜 같은 내용이 사람마다 다르기 때문이다. 템플릿 엔진은 이 문제를 해결한다. HTML에 빈칸(플레이스홀더)을 만들어 두고, 요청이 들어오면 서버가 실제 데이터를 채워 완성된 HTML을 보낸다.

비유하자면 계약서 양식과 같다. 서식은 하나지만 서명자 이름, 날짜, 금액만 바꿔서 수백 개를 찍어낼 수 있다.

`핵심 흐름: 요청 수신 → 서버에서 데이터 조회 → 템플릿에 데이터 삽입 → 완성된 HTML 전송`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 템플릿 엔진 | HTML 골격과 데이터를 합쳐 최종 HTML을 만드는 도구 |
| 플레이스홀더 | 실제 데이터가 들어갈 자리를 표시하는 기호 (EJS에서는 `<%= %>`) |
| 뷰(View) | 화면에 보여주는 HTML 파일. 템플릿 파일이 바로 뷰다 |
| res.render() | Express에서 템플릿 파일과 데이터를 합쳐 응답을 보내는 함수 |
| EJS | 가장 대중적인 템플릿 엔진. HTML 문법 안에 JS 코드를 끼워 넣는 방식 |

## 예를 들어 설명하면

EJS를 Express에 연결하고 사용자 이름을 페이지에 출력하는 예시다.

```js
// app.js
const express = require('express');
const app = express();

app.set('view engine', 'ejs');
app.set('views', './views');

app.get('/', (req, res) => {
  res.render('index', { title: 'Home Page', user: 'Alice' });
});
```

```html
<!-- views/index.ejs -->
<h1>Welcome, <%= user %>!</h1>
<title><%= title %></title>
```

요청이 오면 Express는 `index.ejs`를 읽고 `<%= user %>`를 `Alice`로, `<%= title %>`를 `Home Page`로 바꾼 뒤 완성된 HTML을 보낸다.

## 이 단계에서 중요한 판단 기준

템플릿 엔진은 서버가 HTML을 완성해서 보내기 때문에, 페이지 내용이 요청마다 달라지지만 브라우저 측에서 복잡한 상호작용이 필요 없는 경우에 적합하다.

## 한 줄 요약 — 이것만 기억하면 된다

**서버가 HTML 골격에 데이터를 채워 완성된 페이지를 브라우저로 보내는 것이 템플릿 엔진의 역할이다.**

## 나중에 더 깊게 들어가면

- Pug, Handlebars 같은 다른 템플릿 엔진과 EJS의 문법 차이
- 템플릿 엔진 방식과 React/Vue 같은 클라이언트 렌더링 방식의 성능 비교
- 서버 사이드 렌더링(SSR)이 SEO와 초기 로딩에 미치는 영향

---

**원본:** [What is the role of a templating engine in an Express.js application? — memoryhub.tistory.com/274](https://memoryhub.tistory.com/274)
