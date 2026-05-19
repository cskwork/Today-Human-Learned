+++
title = "Docker 명령어 치트시트 — 현장에서 바로 쓰는 핵심 명령어"
date = "2024-05-25"
description = "Docker는 컨테이너를 빌드·실행·배포하는 도구이며, 명령어 구조는 `docker <동사> <대상>` 패턴으로 일관된다."
tags = ["kubernetes"]
categories = ["kubernetes"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Docker는 컨테이너를 빌드·실행·배포하는 도구이며, 명령어 구조는 `docker <동사> <대상>` 패턴으로 일관된다.

---

## Docker를 왜 쓰는지 감 잡기

Docker는 애플리케이션과 그 실행 환경을 하나의 이미지로 묶어서 어디서든 동일하게 실행할 수 있다. "내 컴퓨터에서는 되는데요"라는 상황을 없애기 위해 만들어졌다. 개발자가 작성한 코드를 테스트 서버, 운영 서버에서 동일한 방식으로 실행하는 것이 핵심 목적이다.

초보자는 처음에 이렇게 이해하면 된다.

`이미지(설계도) → 컨테이너(실행 중인 인스턴스) → 레지스트리(이미지 보관소)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 이미지(Image) | 컨테이너를 만드는 설계도. Dockerfile로 빌드하며 변경되지 않는다. |
| 컨테이너(Container) | 이미지를 실행한 상태. 프로세스처럼 시작하고 멈출 수 있다. |
| Dockerfile | 이미지를 어떻게 만들지 적어둔 레시피 파일. |
| 레지스트리(Registry) | 이미지를 저장하고 공유하는 창고. Docker Hub가 대표적이다. |
| Docker Compose | 여러 컨테이너를 하나의 설정 파일로 묶어서 함께 실행하는 도구. |

## 예를 들어 설명하면

웹 서버 이미지를 빌드하고, 실행하고, 레지스트리에 올리는 전형적인 흐름이다.

```bash
# 현재 디렉토리의 Dockerfile로 이미지 빌드
docker build -t myapp .

# 로컬 4000번 포트를 컨테이너 80번 포트에 연결해서 실행
docker run -d -p 4000:80 myapp

# 실행 중인 컨테이너 목록 확인
docker ps

# 컨테이너 내부 셸에 접속
docker exec -it <container-id> bash

# 이미지에 태그를 붙이고 레지스트리에 업로드
docker tag myapp username/myapp:v1
docker push username/myapp:v1
```

Docker Compose로 여러 서비스를 한 번에 실행할 때는 `docker-compose up -d`, 내릴 때는 `docker-compose down`으로 충분하다.

불필요한 리소스가 쌓였을 때는 `docker system prune`으로 한 번에 정리한다.

## 이 단계에서 중요한 판단 기준

컨테이너가 멈추거나 이상할 때는 `docker logs <container-id> -f`로 실시간 로그를 먼저 확인한다.

## 한 줄 요약 — 이것만 기억하면 된다

**`build`로 이미지를 만들고, `run`으로 실행하고, `push`로 공유한다.**

## 나중에 더 깊게 들어가면

- Docker 네트워크 모드 (bridge, host, overlay) 와 컨테이너 간 통신
- Docker Swarm으로 여러 호스트에 걸친 서비스 스케일링
- Docker 볼륨과 영구 스토리지 관리

---

**원본:** [Docker CheatSheet — https://memoryhub.tistory.com/60](https://memoryhub.tistory.com/60)
