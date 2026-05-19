# Lettuce로 Redis 연동하기 — Java 비동기 클라이언트 입문

> **TL;DR**
> Lettuce는 스레드를 막지 않고 Redis와 통신하는 Java 클라이언트다. 요청을 보내고 결과를 기다리는 동안 다른 일을 할 수 있어, 대규모 실시간 시스템에서 기존 Jedis보다 처리량이 높다.

---

## Lettuce를 왜 쓰는지 감 잡기

Redis는 메모리 기반 저장소로, 채팅 메시지 큐나 실시간 알림처럼 속도가 중요한 곳에 쓰인다. 기존 Java 클라이언트(Jedis)는 요청 하나를 보내면 응답이 올 때까지 스레드가 멈춘다. 동시 접속자가 많아지면 스레드가 부족해진다.

Lettuce는 Netty 기반으로 동작하며 하나의 연결을 여러 스레드가 공유한다. 요청을 보낸 뒤 스레드는 즉시 다른 요청을 처리할 수 있다. 결과가 오면 콜백이나 `Mono`/`Flux`로 받는다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: RedisClient 생성 → Connection 획득 → reactive() API 호출 → Mono/Flux 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Lettuce | 비동기 방식으로 Redis와 통신하는 Java 클라이언트 라이브러리 |
| StatefulRedisConnection | Redis 서버와 유지되는 연결 객체. 일반 명령에 쓴다 |
| StatefulRedisPubSubConnection | Pub/Sub 전용 연결. 메시지 채널 구독에 쓴다 |
| Mono / Flux | 결과가 0-1개면 `Mono`, 여러 개면 `Flux`. 나중에 값을 받겠다는 약속 객체 |
| Pub/Sub | 발행자(Publisher)가 채널에 메시지를 던지면 구독자(Subscriber)가 받는 패턴 |

## 예를 들어 설명하면

채팅방 메시지를 Redis에 저장하고, 같은 채널을 구독 중인 클라이언트에게 즉시 전달하는 시나리오다.

```java
// 1. 연결 설정 (Spring Bean)
@Bean
public RedisClient redisClient() {
    return RedisClient.create("redis://localhost:6379");
}

@Bean
public StatefulRedisConnection<String, String> connection(RedisClient client) {
    return client.connect();
}

// 2. 메시지 발행 — Mono<Long>은 구독자 수를 비동기로 반환
public Mono<Long> publish(String channel, String message) {
    return connection.reactive().publish(channel, message);
}

// 3. 메시지 큐 (List 자료구조 활용)
public Mono<Long> enqueue(String message) {
    return connection.reactive().lpush("message_queue", message);
}

public Mono<String> dequeue() {
    return connection.reactive().rpop("message_queue");
}
```

`publish`를 호출하면 즉시 `Mono`가 반환된다. 실제 Redis 통신은 구독(`.subscribe()`)하는 시점에 실행된다.

## 이 단계에서 중요한 판단 기준

대규모 동시 접속이나 실시간 메시지 전달이 필요하다면 Lettuce의 reactive API를 선택하고, 단순한 캐시 읽기/쓰기라면 동기 API(`connection.sync()`)로 시작해도 충분하다.

## 한 줄 요약 — 이것만 기억하면 된다

**Lettuce의 `reactive()` API는 스레드를 낭비하지 않고 Redis를 다루는 방법이며, 결과는 `Mono`나 `Flux`로 받는다.**

## 나중에 더 깊게 들어가면

- Reactive 스트림의 backpressure — 소비자가 처리 가능한 속도로 데이터를 제어하는 방법
- Spring Data Redis와 Lettuce 통합 — `ReactiveRedisTemplate` 사용법
- Lettuce 클러스터 모드 — Redis Cluster 환경에서의 연결 관리

---

**원본:** [Implement Redis with Lettuce in Java](https://memoryhub.tistory.com/310)
