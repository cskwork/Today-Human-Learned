+++
title = "AWS Elastic Beanstalk — 코드만 올리면 배포가 되는 서비스"
date = "2024-06-10"
description = "Elastic Beanstalk은 코드를 업로드하면 서버 프로비저닝, 로드밸런싱, 오토스케일링을 자동으로 처리해주는 AWS PaaS 서비스다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Elastic Beanstalk은 코드를 업로드하면 서버 프로비저닝, 로드밸런싱, 오토스케일링을 자동으로 처리해주는 AWS PaaS 서비스다.

---

## Elastic Beanstalk을 왜 쓰는지 감 잡기

웹 애플리케이션을 배포하려면 서버를 준비하고, 로드밸런서를 연결하고, 트래픽에 따라 서버 수를 조절하는 설정이 필요하다. 이 인프라 작업은 애플리케이션 로직과 무관하지만 시간이 많이 든다.

Elastic Beanstalk은 이 과정을 추상화한다. 개발자는 코드를 올리고 런타임(Python, Node.js, Java 등)을 선택하기만 하면 된다. 나머지는 Elastic Beanstalk이 EC2 인스턴스, 로드밸런서, 오토스케일링 그룹을 자동으로 구성하고 관리한다. 작은 팀이 인프라 엔지니어 없이 빠르게 서비스를 올릴 때 적합하다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 코드 업로드 → 런타임 감지 → 인프라 자동 생성 → 배포 → 모니터링 및 오토스케일`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 애플리케이션(Application) | Elastic Beanstalk에서 관리하는 코드와 설정의 단위. |
| 환경(Environment) | 애플리케이션이 실제로 실행되는 AWS 자원의 집합(EC2, 로드밸런서 등). |
| 배포(Deployment) | 새 버전의 코드를 환경에 반영하는 과정. |
| 오토스케일링(Auto Scaling) | 트래픽 증가 시 자동으로 서버를 추가하고, 줄어들면 다시 줄이는 기능. |
| 플랫폼(Platform) | 애플리케이션이 실행될 런타임 환경. Python, Node.js, Java, Docker 등을 선택한다. |

## 예를 들어 설명하면

Django 웹 애플리케이션을 배포하는 흐름이다.

1. 코드를 ZIP으로 묶어 Elastic Beanstalk 콘솔에 업로드한다.
2. Elastic Beanstalk이 Python 런타임이 필요함을 감지하고 EC2 인스턴스를 생성한다.
3. 환경 설정에서 CPU 사용률 70% 초과 시 인스턴스를 추가하도록 오토스케일링 규칙을 지정한다.
4. 배포가 완료되면 접속 가능한 URL이 자동으로 생성된다.
5. 트래픽이 급증하면 Elastic Beanstalk이 자동으로 인스턴스를 추가하고, 줄어들면 반납한다.

인프라를 직접 다룰 필요 없이, 코드와 설정 파일(`.ebextensions`)만 관리하면 된다.

## 이 단계에서 중요한 판단 기준

인프라 세부 제어가 필요 없고 빠른 배포가 목표라면 Elastic Beanstalk을, 세밀한 인프라 제어가 필요하다면 ECS나 직접 EC2를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Elastic Beanstalk은 코드를 올리면 서버를 알아서 만들고 관리해주는 AWS의 자동화 배포 플랫폼이다.**

## 나중에 더 깊게 들어가면

- `.ebextensions` 설정 파일로 환경을 세밀하게 제어하는 방법
- Blue/Green 배포로 다운타임 없이 업데이트하는 방법
- Elastic Beanstalk vs ECS(컨테이너 기반) 선택 기준

---

**원본:** [Elastic Beanstalk Introduced — https://memoryhub.tistory.com/246](https://memoryhub.tistory.com/246)
