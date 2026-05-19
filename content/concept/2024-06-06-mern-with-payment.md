+++
title = "MERN 스택에 결제 연동하기"
date = "2024-06-06"
description = "MERN 앱에 결제를 붙이려면 프론트엔드에서 카드 정보를 토큰으로 바꾸고, 백엔드에서 그 토큰으로 결제사 API를 호출하면 된다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> MERN 앱에 결제를 붙이려면 프론트엔드에서 카드 정보를 토큰으로 바꾸고, 백엔드에서 그 토큰으로 결제사 API를 호출하면 된다.

---

## 결제 연동을 왜 이렇게 구성하는지 감 잡기

온라인 쇼핑몰이 카드 번호를 직접 받아 저장하면 법적 책임과 보안 부담이 막대하다. 그래서 Stripe, PayPal 같은 결제 대행사(Payment Gateway)에 실제 카드 처리를 위임한다. 프론트엔드는 카드 번호를 서버로 보내지 않고 결제사 SDK가 직접 받아 토큰으로 치환한다. 백엔드는 토큰만 받아 결제사 API를 호출한다. 카드 번호가 자체 서버를 거치지 않으므로 PCI DSS 규정 준수 부담이 크게 줄어든다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 사용자 카드 입력 → SDK가 토큰 발급 → 토큰을 백엔드 전송 → 백엔드가 결제사 API 호출 → 결제 완료`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 결제 게이트웨이 | 카드사·은행과 실제 통신을 대신해주는 중간 서비스. 개발자는 이 서비스의 API만 쓴다. |
| 토큰화 (Tokenization) | 카드 번호를 일회용 토큰 문자열로 교체하는 과정. 원본 카드 정보가 자체 서버에 닿지 않는다. |
| 공개 키 / 비밀 키 | 결제사가 발급하는 API 인증 수단. 공개 키는 프론트에, 비밀 키는 반드시 백엔드 환경변수에만 보관한다. |
| Charge | 결제사 API에서 실제 청구를 생성하는 동작. 금액·통화·토큰을 파라미터로 넘긴다. |
| Webhook | 결제 성공·실패 이벤트를 결제사가 서버로 직접 푸시해주는 HTTP 콜백. |

## 예를 들어 설명하면

React(프론트) + Express(백엔드) 조합에서 Stripe를 붙이는 최소 흐름이다.

**프론트엔드 — 카드 입력 후 토큰 요청**

```jsx
import { CardElement, useStripe, useElements } from '@stripe/react-stripe-js';

const handleSubmit = async (e) => {
    e.preventDefault();
    const { token, error } = await stripe.createToken(elements.getElement(CardElement));
    if (error) return console.error(error.message);

    await fetch('/pay', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ token: token.id, amount: 1000 }), // 금액 단위: 센트
    });
};
```

**백엔드 — 토큰으로 실제 청구 생성**

```javascript
app.post('/pay', async (req, res) => {
    const { token, amount } = req.body;
    const charge = await stripe.charges.create({
        amount, currency: 'usd', source: token, description: '주문 결제',
    });
    res.json(charge);
});
```

비밀 키(`STRIPE_SECRET_KEY`)는 환경변수로만 관리한다. 코드에 직접 쓰면 절대 안 된다.

## 이 단계에서 중요한 판단 기준

결제 로직은 반드시 백엔드에서 처리한다. 금액을 프론트에서 계산해 넘기면 클라이언트 조작으로 임의 금액이 청구될 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**카드 정보는 결제사 SDK가 토큰으로 치환하고, 백엔드는 토큰만 받아 API를 호출한다 — 카드 번호가 자체 서버를 지나지 않는 구조가 핵심이다.**

## 나중에 더 깊게 들어가면

- Stripe Webhook 검증 — 결제 이벤트를 안전하게 수신하는 방법
- Payment Intent API — 더 현대적인 Stripe 결제 흐름과 3D Secure 지원
- 환불·부분 취소(Refund) — 결제 이후 상태 관리

---

**원본:** [MERN with payment — https://memoryhub.tistory.com/198](https://memoryhub.tistory.com/198)
