# Aider sendchat.py 코드 해설 — LLM 호출을 안정적으로 만드는 방법

> **TL;DR**
> `sendchat.py`는 LLM API 호출 실패 시 자동으로 재시도하고, 같은 요청은 캐시에서 바로 반환해 속도와 비용을 아끼는 모듈이다.

---

## sendchat.py를 왜 쓰는지 감 잡기

LLM API는 언제든 실패할 수 있다. 네트워크가 끊기거나, 서버가 과부하 상태(429 Rate Limit)거나, 일시적인 연결 오류가 발생한다. 이럴 때 그냥 에러를 던지면 사용자 경험이 나빠진다. `sendchat.py`는 실패한 요청을 일정 시간 간격을 두고 자동으로 재시도하고, 반복 요청은 캐시에서 즉시 응답해 불필요한 API 비용을 줄인다.

초보자는 처음에 이렇게 이해하면 된다.

`API 호출 → 실패? → 지수 백오프로 재시도 → 성공? → 결과 캐시에 저장 → 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 지수 백오프(exponential backoff) | 재시도 간격을 1초 → 2초 → 4초처럼 두 배씩 늘리는 전략. 서버 폭주를 막는다 |
| `backoff.on_exception` | 특정 예외 발생 시 자동 재시도 로직을 함수에 붙여 주는 데코레이터 |
| `should_giveup` | "이 예외는 재시도해도 소용없다"고 판단해 즉시 포기할지 결정하는 함수 |
| 캐시(CACHE) | 같은 요청을 두 번 보내지 않도록 결과를 저장해 두는 임시 저장소 |
| `litellm` | OpenAI, Anthropic, 로컬 모델 등 여러 LLM을 단일 인터페이스로 호출하는 라이브러리 |

## 예를 들어 설명하면

핵심 함수 두 개가 어떻게 맞물리는지 코드로 보면 구조가 명확해진다.

```python
# 재시도 로직이 붙은 핵심 함수
@backoff.on_exception(
    backoff.expo,                       # 지수 백오프
    (httpx.ConnectError, litellm.exceptions.RateLimitError, ...),
    giveup=should_giveup,               # 포기 조건
    max_time=60,                        # 최대 60초간 재시도
)
def send_with_retries(model_name, messages, functions, stream, temperature=0):
    key = json.dumps(kwargs, sort_keys=True).encode()  # 캐시 키 생성
    if not stream and CACHE is not None and key in CACHE:
        return hash_object, CACHE[key]  # 캐시 히트 → 즉시 반환
    res = litellm.completion(**kwargs)  # 실제 API 호출
    if not stream and CACHE is not None:
        CACHE[key] = res                # 결과 캐시에 저장
    return hash_object, res

# 바깥에서 쓰는 단순화 인터페이스
def simple_send_with_retries(model_name, messages):
    _hash, response = send_with_retries(model_name, messages, None, False)
    return response.choices[0].message.content
```

`should_giveup`이 중요한 이유는 모든 에러를 재시도하면 안 되기 때문이다. 400 Bad Request(잘못된 요청)는 아무리 재시도해도 성공하지 않으므로 즉시 포기한다. 503 Service Unavailable(서버 과부하)은 잠시 기다리면 성공할 수 있으므로 재시도한다.

## 이 단계에서 중요한 판단 기준

스트리밍(`stream=True`) 응답은 캐시하지 않는다. 스트리밍은 토큰이 실시간으로 흘러오는 방식이라 전체 결과를 미리 저장할 수 없기 때문이다.

## 한 줄 요약 — 이것만 기억하면 된다

**`sendchat.py`는 지수 백오프 재시도와 결과 캐싱으로 LLM API 호출을 비용 효율적이고 장애에 강하게 만드는 모듈이다.**

## 나중에 더 깊게 들어가면

- `litellm`이 다양한 LLM 제공자를 단일 인터페이스로 추상화하는 방법
- 재시도 가능한 HTTP 상태 코드 목록 (`_should_retry` 내부 구현)
- 캐시를 디스크에 영속화하는 `CACHE_PATH` 설정 방법

---

**원본:** [Aider sendchat.py Code Explained — https://memoryhub.tistory.com/303](https://memoryhub.tistory.com/303)
