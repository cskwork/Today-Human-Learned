# Java에서 Jedis로 Redis 연동하기 — 실시간 메시징의 기반

> **TL;DR**
> Jedis는 Java에서 Redis를 쓰는 클라이언트 라이브러리다. Pub/Sub 패턴과 큐를 조합하면 실시간 메시징 시스템을 빠르게 구축할 수 있다.

---

## Redis + Jedis를 왜 쓰는지 감 잡기

실시간 채팅, 알림, 이벤트 스트리밍 같은 기능은 데이터베이스만으로 만들기 어렵다. 매번 DB를 폴링하면 지연이 생기고 부하가 크다. Redis는 메모리에서 동작하는 데이터 저장소로 초당 수십만 건의 읽기/쓰기를 처리한다. 그리고 메시지를 채널로 발행(publish)하고 구독(subscribe)하는 Pub/Sub 기능을 기본 제공한다.

Jedis는 이 Redis를 Java 코드에서 직접 호출할 수 있게 해주는 클라이언트다.

`핵심 흐름: Publisher가 채널에 메시지 발행 → Redis가 구독자에게 전달 → Subscriber가 수신 처리`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Jedis | Java용 Redis 클라이언트 라이브러리. Redis 명령어를 Java 메서드로 감싼다. |
| JedisPool | Jedis 연결을 재사용하는 풀. 매번 새 연결을 열지 않아 성능이 좋다. |
| Pub/Sub | Publisher가 채널에 메시지를 보내면 구독한 Subscriber가 즉시 받는 패턴. |
| lpush / rpop | 리스트의 왼쪽에 넣고 오른쪽에서 꺼내는 큐 구조. 작업 대기열에 쓴다. |
| hset / hgetAll | 해시(Hash) 자료구조에 키-값 쌍을 저장하고 전체를 가져오는 명령어. |

## 예를 들어 설명하면

Pub/Sub와 메시지 큐 두 가지 패턴을 나란히 보면 차이가 명확하다.

```java
// 연결 풀 — 한 번만 만들고 재사용한다
public class RedisConnection {
    private static JedisPool pool = new JedisPool("localhost", 6379);
    public static Jedis getConnection() { return pool.getResource(); }
}

// Pub/Sub: 실시간 알림 — 구독자가 연결돼 있을 때만 받는다
public class MessagePublisher {
    public void publish(String channel, String message) {
        try (Jedis jedis = RedisConnection.getConnection()) {
            jedis.publish(channel, message);
        }
    }
}

// 메시지 큐: 구독자가 오프라인이어도 메시지를 보존한다
public class MessageQueue {
    private static final String QUEUE_KEY = "message_queue";

    public void enqueue(String message) {
        try (Jedis jedis = RedisConnection.getConnection()) {
            jedis.lpush(QUEUE_KEY, message);
        }
    }

    public String dequeue() {
        try (Jedis jedis = RedisConnection.getConnection()) {
            return jedis.rpop(QUEUE_KEY);
        }
    }
}
```

Pub/Sub는 속도가 중요한 실시간 알림에 적합하다. 메시지 큐(`lpush`/`rpop`)는 수신자가 일시적으로 끊겨도 메시지를 잃지 않아야 할 때 쓴다. 두 패턴을 조합하면 실시간성과 내구성을 모두 챙길 수 있다.

## 이 단계에서 중요한 판단 기준

`try-with-resources`로 Jedis 연결을 반드시 반환해야 한다. 반환하지 않으면 풀이 고갈되어 서비스가 멈춘다.

## 한 줄 요약 — 이것만 기억하면 된다

**Jedis로 Redis Pub/Sub와 큐를 연결하면 실시간 메시징 시스템의 핵심을 수십 줄로 구현할 수 있다.**

## 나중에 더 깊게 들어가면

- Spring Data Redis로 Jedis 대신 추상화 레이어 사용하기
- Redis Streams — Pub/Sub보다 내구성이 높은 메시징 모델
- 오프라인 클라이언트를 위한 메시지 확인(acknowledgment) 패턴

---

**원본:** [Implement Redis with Jedis in Java — https://memoryhub.tistory.com/309](https://memoryhub.tistory.com/309)
