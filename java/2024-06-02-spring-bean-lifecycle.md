# Spring Bean 생명주기 이해하기

> **TL;DR**
> Spring IoC 컨테이너는 Bean을 생성하고 의존성을 주입한 뒤 초기화 콜백을 호출하고, 컨텍스트가 닫힐 때 소멸 콜백을 호출한다.

---

## Spring Bean 생명주기를 왜 알아야 하는지 감 잡기

Spring에서 객체를 `new`로 직접 만들지 않고 컨테이너에 맡기는 이유는 의존성 주입, 프록시 생성, 스코프 관리를 프레임워크가 일관되게 처리하게 하기 위해서다. 생명주기를 알면 "Bean이 사용 가능해진 직후에 DB 연결을 열거나, 컨테이너가 내려가기 전에 자원을 해제"하는 시점을 정확히 제어할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 인스턴스 생성 → 의존성 주입 → 초기화 콜백 → (사용) → 소멸 콜백`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| IoC 컨테이너 | Bean의 생성·의존성 주입·소멸을 관리하는 Spring의 핵심 엔진 |
| @PostConstruct | 의존성 주입이 끝난 직후 호출되는 초기화 메서드에 붙이는 어노테이션 |
| @PreDestroy | 컨테이너가 Bean을 소멸시키기 직전에 호출되는 메서드에 붙이는 어노테이션 |
| Singleton 스코프 | 컨테이너당 Bean 인스턴스를 하나만 만든다. 기본값 |
| Prototype 스코프 | 요청할 때마다 새 인스턴스를 만든다. 소멸 콜백은 컨테이너가 호출하지 않는다 |

## 예를 들어 설명하면

`@PostConstruct`와 커스텀 `init-method`가 같이 있을 때 호출 순서를 보여주는 예시다.

```java
@Configuration
public class AppConfig {
    @Bean(initMethod = "customInit", destroyMethod = "customDestroy")
    public MyBean myBean() { return new MyBean(); }
}

public class MyBean {
    public MyBean() { System.out.println("1. 인스턴스 생성"); }

    @PostConstruct
    public void init() { System.out.println("2. @PostConstruct"); }

    public void customInit() { System.out.println("3. customInit (init-method)"); }

    @PreDestroy
    public void preDestroy() { System.out.println("4. @PreDestroy"); }

    public void customDestroy() { System.out.println("5. customDestroy (destroy-method)"); }
}
```

실행 순서: 인스턴스 생성 → `@PostConstruct` → `customInit` → (Bean 사용) → `@PreDestroy` → `customDestroy`.

`@PostConstruct`가 항상 커스텀 `init-method`보다 먼저 호출된다는 점을 기억한다.

## 이 단계에서 중요한 판단 기준

자원 초기화/해제 로직은 `@PostConstruct` / `@PreDestroy`를 우선 사용한다 — 이 두 어노테이션은 Spring 없이도 동작하는 표준(Jakarta EE)이라 이식성이 높다. `InitializingBean`이나 `DisposableBean` 인터페이스는 Spring에 강하게 결합되므로 피한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Bean 초기화 직후 할 일은 `@PostConstruct`, 컨테이너 종료 전 해제할 자원은 `@PreDestroy`에 둔다.**

## 나중에 더 깊게 들어가면

- `BeanPostProcessor`로 모든 Bean의 초기화 전후에 공통 처리 끼워 넣기
- Prototype 스코프 Bean의 소멸 처리 방법 (컨테이너가 자동으로 하지 않음)
- `@Lazy`와 생명주기의 관계 — 첫 요청 시 초기화가 일어나는 시점 이해

---

**원본:** [Spring Bean Lifecycle](https://memoryhub.tistory.com/179)
