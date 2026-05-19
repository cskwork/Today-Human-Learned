# Docker 컨테이너에서 로컬 Ollama 연결, 왜 host.docker.internal이 답일까

> **TL;DR**
> Docker 컨테이너 안에서 `localhost`는 컨테이너 자신을 가리키므로 호스트의 Ollama에 닿지 않는다. `host.docker.internal`이라는 특수 DNS 이름을 쓰면 컨테이너에서 호스트 서비스에 접근할 수 있다.

---

## host.docker.internal을 왜 쓰는지 감 잡기

Docker 컨테이너는 격리된 네트워크 환경을 갖는다. 컨테이너 내부에서 `localhost`를 입력하면 그 컨테이너 자신을 가리킨다. 호스트(내 노트북/서버) 위에서 실행 중인 Ollama는 컨테이너 입장에서 "외부"다. 포트 포워딩(호스트 포트 → 컨테이너 포트 방향)은 이 역방향 문제를 해결하지 못한다.

Docker는 이 문제를 위해 `host.docker.internal`이라는 특수 DNS 이름을 제공한다. 이 이름으로 요청을 보내면 Docker가 호스트 머신의 IP로 라우팅해준다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 컨테이너 앱 → host.docker.internal:11434 → 호스트의 Ollama`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Ollama | 로컬에서 LLM을 실행하는 도구. 기본적으로 포트 11434를 쓴다. |
| host.docker.internal | 컨테이너 안에서 호스트(내 컴퓨터)의 localhost에 접근할 때 쓰는 특수 도메인 이름. |
| Docker Bridge Network | 같은 호스트의 컨테이너들이 서로 통신할 수 있게 해주는 기본 네트워크 모드. |
| extra_hosts | docker-compose.yml에서 컨테이너에 커스텀 DNS 항목을 추가하는 설정 키. |
| host-gateway | Linux에서 `host.docker.internal`이 가리킬 호스트 IP를 Docker에게 알려주는 특수 값. |

## 예를 들어 설명하면

Mac/Windows는 기본적으로 `host.docker.internal`이 동작한다. Linux는 수동 설정이 필요하다.

```yaml
# docker-compose.yml — Linux 포함 크로스 플랫폼 설정
version: '3.8'
services:
  my-app:
    image: my-app
    ports:
      - "8080:8080"
    extra_hosts:
      - "host.docker.internal:host-gateway"   # Linux에서 필요
    environment:
      - OLLAMA_BASE_URL=http://host.docker.internal:11434
```

컨테이너를 직접 실행할 때는 다음과 같다.

```bash
# Linux
docker run -d \
  --add-host=host.docker.internal:host-gateway \
  -e OLLAMA_API_BASE_URL=http://host.docker.internal:11434 \
  -p 8080:8080 \
  your-app:latest

# 연결 확인
docker exec -it your-container curl http://host.docker.internal:11434/api/tags
```

## 이 단계에서 중요한 판단 기준

`host.docker.internal`은 개발·로컬 환경에서 쓴다. 스테이징·프로덕션에서는 Ollama를 별도 컨테이너로 분리하거나 전용 서비스 엔드포인트를 사용하는 것이 안전하다.

## 한 줄 요약 — 이것만 기억하면 된다

**컨테이너에서 호스트 서비스에 접근할 때는 `localhost` 대신 `host.docker.internal`을 쓴다. Linux는 `--add-host=host.docker.internal:host-gateway`를 추가해야 한다.**

## 나중에 더 깊게 들어가면

- Docker 네트워크 브릿지 모드와 host 모드의 성능·보안 차이
- 스테이징 환경에서 Ollama 컨테이너를 별도로 운영하는 구성
- Ollama가 `0.0.0.0`에서 리스닝하도록 설정하는 방법과 보안 주의사항

---

**원본:** Docker 컨테이너에서 로컬 Ollama 연결, 왜 host.docker.internal이 답일까? — [https://memoryhub.tistory.com/779](https://memoryhub.tistory.com/779)
