# Trunk-Based Development — 머지 지옥에서 벗어나는 법

> **TL;DR**
> 브랜치를 하루 이내에 main에 합치고, 미완성 기능은 Feature Flag로 숨기고, CI/CD로 자동 검증하면 "머지 지옥"이 사라진다.

---

## TBD를 왜 쓰는지 감 잡기

2주간 작업한 feature 브랜치를 main에 합치려는데 충돌이 200개다. 리뷰어는 500줄 변경 사항을 보며 맥락을 잡지 못한다. 이 상황이 반복되는 이유는 브랜치가 오래 살아있었기 때문이다.

Trunk-Based Development(TBD)는 이 구조 자체를 바꾼다. 모든 개발자가 하나의 main 브랜치(trunk)에 하루 이내의 짧은 브랜치로 자주 합친다. 브랜치 수명이 짧을수록 충돌 범위도 좁아진다.

Google의 DORA 연구에 따르면 배포 빈도가 높고 변경 리드 타임이 짧은 팀이 안정성도 높다. TBD는 그 팀들의 공통 작업 방식이다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 작은 브랜치 생성(몇 시간) → Feature Flag로 미완성 기능 숨김 → main 머지 → CI/CD 자동 검증 → 배포`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Trunk | 모든 개발자가 공유하는 단일 메인 브랜치(main 또는 master). TBD의 중심축. |
| Feature Flag | 코드 안에 if-else 스위치를 넣어 기능을 켜고 끌 수 있게 하는 기법. 미완성 코드를 배포해도 사용자에게 보이지 않게 한다. |
| CI (Continuous Integration) | 코드를 main에 합칠 때마다 자동으로 테스트와 빌드를 실행해 문제를 즉시 발견하는 자동화 체계. |
| CD (Continuous Deployment) | CI를 통과하면 자동으로 운영 환경에 배포하는 체계. 사람이 버튼을 누르지 않아도 된다. |
| 점진적 롤아웃 | 새 기능을 전체 사용자에게 한 번에 열지 않고 1% → 10% → 50% → 100% 순서로 단계적으로 확대하는 배포 방식. |

## 예를 들어 설명하면

새 결제 기능을 TBD 방식으로 개발하는 흐름이다.

```typescript
// Feature Flag로 미완성 기능을 감싸서 main에 안전하게 머지
export async function processPayment(order: Order): Promise<PaymentResult> {
  const useNewGateway = await isFeatureEnabled('new-payment-gateway', {
    kind: 'user',
    key: order.userId
  });

  if (useNewGateway) {
    return newPaymentGateway.process(order); // 개발 중 — 사용자에게 안 보임
  }
  return legacyPaymentGateway.process(order); // 기존 로직 유지
}
```

기능이 완성되면 Feature Flag 플랫폼에서 점진적으로 활성화한다.

```
1일차: 내부 QA 팀만 (1%)
3일차: 베타 사용자 (5%)
7일차: 전체의 25%
14일차: 100% 활성화
21일차: Flag 코드 자체 제거 — 기술 부채 청산
```

Flag를 제거하지 않으면 코드에 if-else가 쌓여 복잡도가 기하급수적으로 늘어난다.

## 이 단계에서 중요한 판단 기준

Feature Flag는 기능이 안정화된 후 2~4주 내에 제거해야 한다 — 오래 남긴 Flag는 테스트 조합과 코드 복잡도를 폭발적으로 늘리는 기술 부채가 된다.

## 한 줄 요약 — 이것만 기억하면 된다

**TBD의 핵심은 "하루 이내 머지 + Feature Flag로 배포와 릴리스 분리"이며, 이것이 갖춰지면 큰 충돌 없이 매일 배포할 수 있다.**

## 나중에 더 깊게 들어가면

- Feature Flag 전문 플랫폼 비교: LaunchDarkly, Unleash, Flagsmith
- Canary 배포와 Blue-Green 배포의 차이 및 적합한 상황
- GitHub Actions로 CI/CD 파이프라인 구성 시 Feature Flag 조합 테스트 매트릭스 설계

---

**원본:** [Trunk-Based Development 완벽 가이드, Feature Flag와 CI/CD](https://memoryhub.tistory.com/984)
