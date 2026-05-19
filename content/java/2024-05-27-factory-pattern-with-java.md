+++
title = "팩토리 패턴 — 객체 생성을 한 곳에 맡기기"
date = "2024-05-27"
description = "팩토리 패턴은 객체를 직접 `new`로 찍어내는 대신, 전담 생성자(팩토리)에게 위임해 호출부가 구체 클래스를 몰라도 되는 설계다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 팩토리 패턴은 객체를 직접 `new`로 찍어내는 대신, 전담 생성자(팩토리)에게 위임해 호출부가 구체 클래스를 몰라도 되는 설계다.

---

## 팩토리 패턴을 왜 쓰는지 감 잡기

코드 곳곳에 `new Car()`, `new Doll()` 같은 구체 클래스 이름이 박혀 있으면, 나중에 클래스 이름이 바뀌거나 종류가 늘어날 때 수십 군데를 고쳐야 한다. 팩토리 패턴은 생성 로직을 한 메서드에 모아두어 이 문제를 해결한다. 호출부는 "Car 하나 줘"라고만 말하고, 어떻게 만드는지는 신경 쓰지 않아도 된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 호출부 → 팩토리 메서드(type 전달) → 구체 객체 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Factory Method | 어떤 객체를 만들지 결정하는 전담 메서드 |
| Product | 팩토리가 만들어 내는 결과물(인터페이스) |
| ConcreteProduct | Product를 실제로 구현한 클래스 (Car, Doll 등) |
| Creator | 팩토리 메서드를 가진 클래스 |
| ConcreteCreator | Creator를 상속해 팩토리 메서드를 직접 구현한 클래스 |

## 예를 들어 설명하면

장난감 공장이 있다고 하자. 요청받은 종류에 따라 Car 또는 Doll을 내보낸다. 호출부는 Toy 인터페이스만 알면 되고, 구체 클래스는 몰라도 된다.

```java
public interface Toy {
    void play();
}

public class Car implements Toy {
    public void play() { System.out.println("Playing with a car!"); }
}

public class Doll implements Toy {
    public void play() { System.out.println("Playing with a doll!"); }
}

public class ToyFactory {
    public Toy createToy(String type) {
        if (type.equals("Car"))  return new Car();
        if (type.equals("Doll")) return new Doll();
        throw new IllegalArgumentException("Unknown toy type: " + type);
    }
}

// 사용
ToyFactory factory = new ToyFactory();
factory.createToy("Car").play();  // Playing with a car!
factory.createToy("Doll").play(); // Playing with a doll!
```

새 종류(Robot)를 추가할 때 ToyFactory 한 곳만 수정하면 된다.

## 이 단계에서 중요한 판단 기준

생성할 객체의 타입이 런타임에 결정되거나, 같은 인터페이스를 구현하는 클래스가 여럿이라면 팩토리 패턴을 먼저 고려한다.

## 한 줄 요약 — 이것만 기억하면 된다

**객체 생성 로직을 팩토리 메서드 한 곳에 모아두면, 호출부는 구체 클래스 이름을 몰라도 되고 변경에 강해진다.**

## 나중에 더 깊게 들어가면

- Abstract Factory 패턴: 관련 객체 군(群)을 묶어 생성하는 확장 형태
- 팩토리 메서드를 인터페이스로 선언하고 하위 클래스가 결정하는 GoF 원형 구조
- Spring의 BeanFactory — 스프링 컨테이너 자체가 대규모 팩토리 패턴의 구현체

---

**원본:** [Factory Pattern with Java — https://memoryhub.tistory.com/121](https://memoryhub.tistory.com/121)
