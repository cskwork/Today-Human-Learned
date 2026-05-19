# SSE ERR_INCOMPLETE_CHUNKED_ENCODING — 이 헤더 하나로 해결됩니다

> **TL;DR**
> Nginx 앞단을 거치는 SSE 연결이 끊어진다면, 응답 헤더에 `X-Accel-Buffering: no` 한 줄만 추가하면 된다.

---

## SSE를 왜 쓰는지 감 잡기

SSE(Server-Sent Events)는 서버가 클라이언트로 데이터를 실시간으로 밀어주는 기술이다. 채팅 알림, AI 응답 스트리밍, 실시간 대시보드처럼 "서버 → 클라이언트" 방향으로만 데이터가 흐르는 상황에 쓴다.

SSE는 HTTP 연결 하나를 계속 열어두고, 서버가 준비될 때마다 작은 데이터 조각(청크)을 보낸다. 응답 전체 크기를 미리 알 수 없기 때문에 `Transfer-Encoding: chunked` 방식을 쓴다.

로컬에서는 잘 되는데 운영 환경에서만 연결이 뚝뚝 끊긴다면, 십중팔구 중간에 Nginx가 끼어 있기 때문이다.

`핵심 흐름: 서버가 청크 생성 → Nginx가 버퍼에 쌓음 → 버퍼가 차야 클라이언트에 전달 → 연결 불안정`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SSE | 서버 → 클라이언트 단방향 실시간 통신 기술. HTTP 연결 하나를 오래 열어두고 데이터를 계속 흘려보낸다. |
| Chunked Transfer Encoding | 전체 응답 크기를 모를 때 데이터를 조각(청크)으로 나눠 보내는 HTTP 전송 방식 |
| Proxy Buffering | Nginx가 업스트림 서버의 응답을 버퍼에 모아두었다가 한꺼번에 클라이언트로 보내는 동작. 기본값이 켜져 있다. |
| X-Accel-Buffering | 응답 헤더에 `no`로 설정하면 해당 응답에 한해 Nginx의 버퍼링을 끄도록 지시하는 헤더 |
| ERR_INCOMPLETE_CHUNKED_ENCODING | Nginx가 청크를 다 모으지 못한 채 연결을 끊을 때 브라우저가 출력하는 에러 |

## 예를 들어 설명하면

Nginx의 `proxy_buffering`은 기본값이 `on`이다. 이 상태에서 SSE 스트림이 들어오면, Nginx는 청크들을 버퍼에 쌓다가 버퍼가 차거나 타임아웃이 되면 클라이언트에 보낸다. 실시간 전송이 깨지고, 연결이 예고 없이 끊긴다.

해결책은 SSE 엔드포인트의 응답 헤더에 `X-Accel-Buffering: no`를 추가하는 것이다. Nginx는 이 헤더를 읽고 해당 응답에 한해서만 버퍼링을 비활성화한다. 다른 API에는 영향이 없다.

**Spring Boot 예시:**

```java
@GetMapping(value = "/stream", produces = "text/event-stream")
public SseEmitter streamEvents(HttpServletResponse response) {
    response.setHeader("Cache-Control", "no-cache");
    response.setHeader("X-Accel-Buffering", "no");  // 핵심

    SseEmitter emitter = new SseEmitter(Long.MAX_VALUE);
    // SSE 로직...
    return emitter;
}
```

**Node.js 예시:**

```js
app.get('/events', (req, res) => {
    res.writeHead(200, {
        'Content-Type': 'text/event-stream',
        'Cache-Control': 'no-cache',
        'Connection': 'keep-alive',
        'X-Accel-Buffering': 'no'  // 핵심
    });
});
```

브라우저 개발자 도구 Network 탭에서 응답 헤더에 `X-Accel-Buffering: no`가 보이고, Type이 `eventsource`로 표시되면 정상이다.

## 이 단계에서 중요한 판단 기준

`proxy_buffering off`를 Nginx 전역 설정에 추가하면 모든 API의 성능에 영향을 줄 수 있으므로, SSE 엔드포인트 응답 헤더에서 `X-Accel-Buffering: no`로 선택적으로 끄는 방식을 택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**SSE 응답 헤더에 `X-Accel-Buffering: no`를 추가하면 Nginx 버퍼링 문제는 끝난다.**

## 나중에 더 깊게 들어가면

- Nginx `proxy_read_timeout` 설정으로 장시간 연결 안정성 확보하기
- SSE ping/heartbeat 메커니즘으로 연결 유지 구현하기
- WebSocket과 SSE의 차이 및 용도별 선택 기준

---

**원본:** [SSE ERR_INCOMPLETE_CHUNKED_ENCODING, 이 헤더 하나로 해결됩니다](https://memoryhub.tistory.com/787)
