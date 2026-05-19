# 싱글톤 패턴 — 인스턴스를 딱 하나만 유지하기

> **TL;DR**
> 싱글톤 패턴은 클래스의 인스턴스가 JVM 안에서 단 하나만 존재하도록 강제하고, 어디서든 같은 그 인스턴스를 쓸 수 있게 해주는 설계다.

---

## 싱글톤 패턴을 왜 쓰는지 감 잡기

DB 커넥션 풀, 설정 관리자, 로그 핸들러처럼 앱 전역에서 하나만 있어야 하는 자원이 있다. 여러 곳에서 제각각 `new`로 생성하면 인스턴스마다 상태가 달라져 충돌이 생긴다. 싱글톤 패턴은 생성자를 외부에서 호출 못 하게 막고, 최초 한 번 만들어진 인스턴스를 재사용하게 한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 최초 호출 → 인스턴스 없으면 생성 → 이후 호출 → 기존 인스턴스 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Private Constructor | 외부에서 `new`로 객체를 만들지 못하게 막는 생성자 |
| Static Instance | 클래스 수준에서 하나만 유지되는 인스턴스 변수 |
| Lazy Initialization | 처음 필요한 순간에만 인스턴스를 생성하는 방식 |
| getInstance() | 싱글톤 인스턴스를 외부에 반환하는 전용 메서드 |
| Thread-safety | 멀티스레드 환경에서도 인스턴스가 하나만 생성됨을 보장하는 속성 |

## 예를 들어 설명하면

가장 단순한 싱글톤 구현이다. 처음 `getInstance()`를 호출할 때만 객체를 만들고, 그 이후로는 같은 객체를 돌려준다.

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {} // 외부 new 차단

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

// 사용
Singleton a = Singleton.getInstance();
Singleton b = Singleton.getInstance();
System.out.println(a == b); // true — 같은 인스턴스
```

멀티스레드 환경에서는 위 코드가 인스턴스를 두 개 만들 수 있다. 그 문제는 "나중에 배울 내용"에서 다룬다.

## 이 단계에서 중요한 판단 기준

상태를 공유해야 하거나 생성 비용이 높은 자원(DB 커넥션, 설정 객체)이라면 싱글톤을 고려하되, 테스트 시 교체가 어려운 단점이 있으므로 DI 프레임워크(Spring의 `@Bean`)로 대신할 수 있는지 먼저 확인한다.

## 한 줄 요약 — 이것만 기억하면 된다

**싱글톤 패턴은 생성자를 private으로 막고 static 메서드로 단일 인스턴스를 반환해, 앱 전역에서 같은 객체를 안전하게 공유한다.**

## 나중에 더 깊게 들어가면

- `synchronized` 또는 DCL(Double-Checked Locking)로 멀티스레드 안전성 확보하기
- Enum으로 싱글톤 구현하기 — 직렬화와 리플렉션 공격을 원천 차단하는 가장 안전한 방식
- Spring `@Bean`의 기본 스코프(singleton): DI 컨테이너가 싱글톤을 대신 관리하는 이유

---

**원본:** [Singleton Pattern with Java — https://memoryhub.tistory.com/128](https://memoryhub.tistory.com/128)
