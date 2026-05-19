# Stripe와 Java 연동 — 결제 흐름을 코드로 연결하는 방법

> **TL;DR**
> Stripe Java SDK로 PaymentIntent를 만들고, 웹훅으로 결제 결과를 수신하면 안전하고 신뢰할 수 있는 결제 파이프라인이 완성된다.

---

## Stripe 연동을 왜 하는지 감 잡기

결제 처리를 직접 구현하면 카드 정보 보안(PCI DSS), 카드사 통신 프로토콜, 3D Secure 인증 등 복잡한 문제를 모두 직접 해결해야 한다. Stripe는 이 복잡성을 API 뒤로 숨기고, 개발자에게는 "결제 의도 생성 → 결과 수신"이라는 단순한 인터페이스만 노출한다.

Java 백엔드는 Stripe API와 통신해 결제를 시작하고, Stripe가 결제 완료 후 웹훅(HTTP POST)으로 결과를 알려주면 주문 상태를 업데이트한다.

`핵심 흐름: 클라이언트 결제 요청 → 백엔드에서 PaymentIntent 생성 → Stripe 결제 처리 → 웹훅 수신 → 주문 상태 갱신`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| PaymentIntent | Stripe에서 하나의 결제 시도를 추적하는 객체. 금액·통화·상태를 담는다 |
| API 키 | Stripe 계정과 내 서버를 연결하는 비밀 식별자. 절대 클라이언트에 노출하면 안 됨 |
| 웹훅(Webhook) | 결제가 완료되면 Stripe가 내 서버로 보내는 HTTP POST 알림 |
| 웹훅 서명 검증 | Stripe가 보낸 요청인지 확인하는 절차. `endpointSecret`으로 서명을 검증함 |
| StripeException | Stripe API 호출이 실패했을 때 발생하는 예외. 반드시 명시적으로 처리해야 함 |

## 예를 들어 설명하면

의존성 추가(Maven) 후 PaymentIntent를 생성하고, 웹훅 엔드포인트에서 서명을 검증하는 핵심 코드다.

```java
// PaymentIntent 생성
public PaymentIntent createPaymentIntent(long amount, String currency) throws StripeException {
    PaymentIntentCreateParams params = PaymentIntentCreateParams.builder()
        .setAmount(amount)   // 센트 단위 (2000 = $20.00)
        .setCurrency(currency)
        .build();
    return PaymentIntent.create(params);
}

// 웹훅 서명 검증 후 이벤트 처리
public void handleWebhook(String payload, String sigHeader) throws Exception {
    Event event = Webhook.constructEvent(payload, sigHeader, endpointSecret);
    if ("payment_intent.succeeded".equals(event.getType())) {
        // 주문 상태를 '결제 완료'로 업데이트
    }
}
```

API 키는 절대 코드에 하드코딩하지 않는다. 환경변수나 시크릿 매니저에서 읽어와야 한다.

## 이 단계에서 중요한 판단 기준

결제 완료 판단은 프론트엔드 응답이 아니라 반드시 Stripe 웹훅 이벤트를 기준으로 한다. 프론트엔드는 네트워크 오류나 브라우저 종료로 응답이 누락될 수 있기 때문이다.

## 한 줄 요약 — 이것만 기억하면 된다

**Stripe 연동의 핵심은 PaymentIntent로 결제를 시작하고, 웹훅 서명을 검증한 뒤 이벤트로 결제 결과를 처리하는 것이다.**

## 나중에 더 깊게 들어가면

- Stripe 테스트 모드와 테스트 카드 번호: 실제 결제 없이 연동을 검증하는 방법
- 환불(Refund) API: PaymentIntent 기반으로 전액·부분 환불을 처리하는 흐름
- 구독(Subscription): 반복 결제를 위한 Customer·Product·Price 객체 구조

---

**원본:** [Stripe and Java Integration Guide — https://memoryhub.tistory.com/192](https://memoryhub.tistory.com/192)
