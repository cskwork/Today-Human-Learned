+++
title = "@Transactional — 스프링이 트랜잭션을 대신 관리하는 방법"
date = "2025-02-23"
description = "메서드에 `@Transactional`을 붙이면 스프링이 트랜잭션 시작/커밋/롤백을 자동으로 처리해, 데이터가 중간에 꼬이는 일을 방지한다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 메서드에 `@Transactional`을 붙이면 스프링이 트랜잭션 시작/커밋/롤백을 자동으로 처리해, 데이터가 중간에 꼬이는 일을 방지한다.

---

## @Transactional을 왜 쓰는지 감 잡기

은행 계좌이체를 생각해보자. A 계좌에서 돈이 빠져나가는 쿼리와 B 계좌에 돈이 들어오는 쿼리가 있다. 첫 번째는 성공했는데 두 번째에서 오류가 나면 돈이 허공으로 사라진다. 이런 상황을 막으려면 두 쿼리를 하나의 묶음(트랜잭션)으로 처리해야 한다 — 전부 성공하거나, 아무것도 반영하지 않거나.

직접 트랜잭션을 열고 닫는 코드를 매번 쓰면 비즈니스 로직이 지저분해진다. `@Transactional`은 이 반복 작업을 어노테이션 하나로 위임하는 방법이다.

`핵심 흐름: 메서드 진입 → 트랜잭션 시작 → DB 작업 → 정상 종료 시 commit / 예외 발생 시 rollback`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 트랜잭션 | 하나의 작업 단위 — 전부 반영되거나 전부 취소되어야 한다 |
| commit | 트랜잭션 내 모든 작업을 DB에 확정 저장하는 것 |
| rollback | 오류 발생 시 트랜잭션 안의 모든 변경을 취소하는 것 |
| ACID | 트랜잭션이 지켜야 할 4가지 원칙 (원자성·일관성·격리성·지속성) |
| 프록시(Proxy) | 스프링이 @Transactional을 구현하는 방식 — 메서드 호출을 가로채 트랜잭션을 자동 처리 |

## 예를 들어 설명하면

주문 저장과 결제 처리를 하나의 트랜잭션으로 묶는 경우다.

```java
@Service
public class OrderService {

    @Autowired private OrderRepository orderRepository;
    @Autowired private PaymentService paymentService;

    @Transactional
    public void processOrder(OrderRequest request) {
        Order order = new Order(request.getItemId(), request.getAmount());
        orderRepository.save(order);                        // 1. 주문 저장

        paymentService.pay(order.getId(), request.getAmount()); // 2. 결제
        // paymentService.pay()에서 예외가 나면 1번도 함께 롤백된다
    }
}
```

결제 실패 시 주문 레코드가 남지 않는다 — 데이터 정합성이 유지된다.

## 이 단계에서 중요한 판단 기준

같은 클래스 안에서 `this.someMethod()`처럼 내부 호출을 하면 프록시를 거치지 않아 `@Transactional`이 작동하지 않는다 — 트랜잭션이 필요한 메서드는 반드시 외부에서 호출되어야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**`@Transactional`은 메서드 단위로 트랜잭션을 선언적으로 적용하며, 예외가 나면 자동 롤백해 데이터 무결성을 보장한다.**

## 나중에 더 깊게 들어가면

- 체크 예외는 기본적으로 롤백되지 않는다 — `rollbackFor = Exception.class` 설정 이유
- `readOnly = true`가 JPA 성능 최적화에 미치는 영향
- 트랜잭션 전파 속성(Propagation) — REQUIRED, REQUIRES_NEW, NESTED 차이
- 대용량 처리에서 트랜잭션 범위를 나눠야 하는 이유

---

**원본:** [@Transactional 소개](https://memoryhub.tistory.com/453)
