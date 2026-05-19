+++
title = "Linux — 운영체제의 뼈대를 이해하는 첫걸음"
date = "2024-05-28"
description = "Linux는 오픈소스 운영체제 커널로, 서버부터 스마트폰까지 현대 컴퓨팅 인프라의 대부분을 지탱하는 기반이다."
tags = ["linux"]
categories = ["linux"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Linux는 오픈소스 운영체제 커널로, 서버부터 스마트폰까지 현대 컴퓨팅 인프라의 대부분을 지탱하는 기반이다.

---

## Linux를 왜 쓰는지 감 잡기

운영체제는 하드웨어와 소프트웨어 사이에서 자원을 중재하는 소프트웨어다. Linux는 그 운영체제의 핵심(커널)을 오픈소스로 공개한 프로젝트로, 1991년 Linus Torvalds가 시작했다. 지금은 웹 서버의 90% 이상, Android 기기 전체, AWS·GCP·Azure 클라우드 인프라가 Linux 위에서 동작한다. 무료이고 수정 가능하며 수십 년간 검증된 안정성이 이유다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 하드웨어 → 커널 → 쉘(CLI) → 사용자 애플리케이션`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 커널(Kernel) | OS의 핵심 엔진. 메모리·CPU·장치를 직접 관리하며 모든 프로그램이 이 위에서 돌아간다 |
| 배포판(Distro) | 커널에 패키지 관리자·설정 도구 등을 더해 완성된 OS로 만든 것. Ubuntu, Fedora, Debian 등이 있다 |
| CLI(Command Line Interface) | 마우스 없이 텍스트 명령으로 OS를 제어하는 방식. 자동화와 원격 접속에 필수다 |
| 파일시스템 계층 | Linux는 모든 것을 파일로 다룬다. `/`가 최상위이고 `/etc`(설정), `/home`(사용자 데이터) 등이 하위에 있다 |
| 패키지 관리자 | 소프트웨어 설치·업데이트·삭제를 자동화하는 도구. Ubuntu는 `apt`, Fedora는 `dnf`를 사용한다 |

## 예를 들어 설명하면

Ubuntu 서버에 웹 서버를 올리는 과정은 CLI 네 줄이면 충분하다.

```bash
sudo apt update && sudo apt upgrade -y   # 패키지 목록 갱신
sudo apt install apache2 -y              # Apache 웹 서버 설치
sudo systemctl start apache2             # 서비스 시작
sudo systemctl enable apache2            # 재부팅 후에도 자동 시작
```

이후 브라우저에서 서버 IP를 열면 Apache 기본 페이지가 나온다. GUI 없이 서버를 구성할 수 있다는 것이 Linux의 핵심 강점이다.

## 이 단계에서 중요한 판단 기준

배포판을 고를 때는 용도부터 정한다 — 학습용이면 Ubuntu, 엔터프라이즈 서버면 RHEL/Rocky Linux, 커스터마이징이 목적이면 Arch Linux가 적합하다.

## 한 줄 요약 — 이것만 기억하면 된다

**Linux는 커널 하나를 공유하는 오픈소스 OS 생태계이며, CLI와 패키지 관리자를 익히는 것이 Linux 활용의 시작점이다.**

## 나중에 더 깊게 들어가면

- 파일 권한(chmod, chown)과 사용자/그룹 관리
- systemd 서비스 관리와 로그 확인(journalctl)
- 컨테이너(Docker, LXC)와 Linux 네임스페이스의 관계

---

**원본:** [Linux: The Powerhouse of Modern Computing — https://memoryhub.tistory.com/106](https://memoryhub.tistory.com/106)
