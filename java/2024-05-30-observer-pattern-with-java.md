# 옵저버 패턴 — Java로 이해하기

> **TL;DR**
> 상태가 바뀐 객체(Subject)가 관심 있는 객체들(Observer)에게 자동으로 알림을 보내는 구조가 옵저버 패턴이다.

---

## 옵저버 패턴을 왜 쓰는지 감 잡기

어떤 객체의 상태 변화를 다른 여러 객체가 알아야 할 때, 가장 단순한 방법은 변화가 생길 때마다 관련 객체들을 직접 호출하는 것이다. 그러나 호출 대상이 늘어날수록 Subject 코드가 Observer 목록을 하드코딩하게 되고, 결합도가 높아진다. 옵저버 패턴은 Subject가 Observer 목록을 동적으로 관리하게 해서 이 결합을 끊어낸다.

실제로 쓰이는 곳: 이벤트 시스템(버튼 클릭 리스너), 주식 가격 구독, 채팅방 메시지 브로드캐스트.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Observer 등록 → Subject 상태 변경 → notifyObservers() → 각 Observer.update() 호출`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Subject | 상태를 갖고 변화를 통보하는 쪽. Observer 목록을 관리한다 |
| Observer | 통보를 받아 반응하는 쪽. `update()` 메서드 하나를 구현한다 |
| register / remove | Subject에 Observer를 추가·제거하는 동작. 언제든 동적으로 가능 |
| notifyObservers | Subject가 자신의 Observer 전체에게 변화를 알리는 메서드 |
| 느슨한 결합 | Subject는 Observer의 구체 타입을 몰라도 된다 — 인터페이스만 알면 충분 |

## 예를 들어 설명하면

잡지 발행사가 새 호를 내면 구독자 전원에게 자동으로 알린다.

```java
// Observer 인터페이스
public interface Observer {
    void update(String message);
}

// Subject 구현체
public class MagazinePublisher {
    private List<Observer> observers = new ArrayList<>();

    public void register(Observer o)   { observers.add(o); }
    public void unregister(Observer o) { observers.remove(o); }

    public void newIssue(String issue) {
        observers.forEach(o -> o.update(issue)); // 전체 통보
    }
}

// Observer 구현체
public class Subscriber implements Observer {
    private final String name;
    public Subscriber(String name) { this.name = name; }

    @Override
    public void update(String message) {
        System.out.println(name + " 수신: " + message);
    }
}

// 사용
MagazinePublisher pub = new MagazinePublisher();
pub.register(new Subscriber("Alice"));
pub.register(new Subscriber("Bob"));
pub.newIssue("2024년 6월호"); // Alice, Bob 모두 출력
```

## 이 단계에서 중요한 판단 기준

Observer가 Subject의 상태를 직접 pull해야 한다면 `update()` 파라미터로 전체 상태를 넘기기보다 Subject 참조를 넘기는 방식을 고려한다 — 통보만 하고 필요한 것을 Observer가 가져가게 하면 결합도를 더 낮출 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**옵저버 패턴은 "누가 봐야 하는지"를 Subject가 아닌 Observer 스스로 결정하게 만드는 구조다.**

## 나중에 더 깊게 들어가면

- Java 표준 라이브러리 `java.util.Observable` / `Observer`의 한계와 대안
- 동기(sync) 통보 vs 비동기(async) 통보 처리 방법
- Spring의 `ApplicationEvent` / `@EventListener`로 프레임워크 수준 옵저버 구현

---

**원본:** [Observer Pattern with Java](https://memoryhub.tistory.com/122)
