# Redis Pub/Sub — 실시간 메시지 전달의 기초

> **TL;DR**
> Redis Pub/Sub은 발행자와 구독자를 채널로 연결해, 메시지를 실시간으로 전달하는 브로커 역할을 한다.

---

## Redis Pub/Sub을 왜 쓰는지 감 잡기

서버 A가 서버 B에게 "이벤트 발생했다"고 알려야 할 때, 둘을 직접 연결하면 의존성이 생긴다. 서버가 열 대로 늘어나면 연결도 열 배가 된다. Redis Pub/Sub은 중간에 채널을 두어 이 문제를 해결한다. 발행자는 채널에만 메시지를 보내고, 구독자는 채널만 바라보면 된다. 채팅 알림, 실시간 피드 갱신, 마이크로서비스 간 이벤트 전파에 이 방식을 쓴다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Publisher → Channel → Subscriber(s)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Channel | 메시지가 오가는 이름 붙은 통로. "chatroom"이라는 이름의 통로를 만들면 된다. |
| Publisher | 채널에 메시지를 던지는 쪽. 보내고 나면 누가 받는지 신경 쓰지 않는다. |
| Subscriber | 채널을 열어두고 메시지가 오면 바로 받는 쪽. |
| PUBLISH | 메시지를 채널에 보내는 Redis 명령어. |
| SUBSCRIBE | 특정 채널을 구독 상태로 만드는 Redis 명령어. |

## 예를 들어 설명하면

채팅방 "chatroom"에서 메시지를 주고받는 가장 단순한 예시다.

```python
# 구독자 측 (먼저 실행해두어야 메시지를 받을 수 있다)
import redis

r = redis.Redis(host='localhost', port=6379)
p = r.pubsub()
p.subscribe(**{'chatroom': lambda msg: print(msg['data'])})
p.run_in_thread(sleep_time=0.001)

# 발행자 측
r.publish('chatroom', 'Hello, World!')
```

구독자가 실행 중일 때 발행자가 PUBLISH하면 구독자는 즉시 메시지를 수신한다. 구독자가 없으면 메시지는 그냥 사라진다.

## 이 단계에서 중요한 판단 기준

Pub/Sub은 메시지를 저장하지 않으므로, 구독자가 오프라인 상태일 때 보낸 메시지는 영원히 잃는다 — 유실을 허용할 수 없는 용도라면 Redis Stream이나 메시지 큐를 고려한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Redis Pub/Sub은 채널을 매개로 발행자와 구독자를 분리해, 실시간으로 메시지를 전달하되 저장은 하지 않는다.**

## 나중에 더 깊게 들어가면

- Redis Stream: Pub/Sub과 달리 메시지를 저장하고 소비자 그룹을 지원한다
- 여러 채널을 패턴으로 구독하는 PSUBSCRIBE
- Spring Data Redis에서 MessageListenerContainer로 Pub/Sub 통합하기

---

**원본:** [Simple Redis Implementation — https://memoryhub.tistory.com/304](https://memoryhub.tistory.com/304)
