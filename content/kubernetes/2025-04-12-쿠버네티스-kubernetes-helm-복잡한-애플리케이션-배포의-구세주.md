+++
title = "Helm — 쿠버네티스 앱을 패키지로 관리하는 도구"
date = "2025-04-12"
description = "Helm은 쿠버네티스의 패키지 매니저다. 여러 YAML 파일을 하나의 차트로 묶어 설치·업그레이드·롤백을 한 명령으로 처리한다."
tags = ["kubernetes"]
categories = ["kubernetes"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Helm은 쿠버네티스의 패키지 매니저다. 여러 YAML 파일을 하나의 차트로 묶어 설치·업그레이드·롤백을 한 명령으로 처리한다.

---

## Helm을 왜 쓰는지 감 잡기

쿠버네티스에 앱 하나를 올리려면 Deployment, Service, ConfigMap, Secret 등 YAML 파일이 여러 개 필요하다. 환경이 개발·스테이징·운영으로 나뉘면 파일을 복사해서 값만 바꾸는 작업이 반복된다. 버전이 올라가거나 문제가 생겨 되돌려야 할 때도 수동 추적이 어렵다.

Helm은 이 문제를 해결한다. npm이 Node.js 패키지를 관리하듯, Helm은 쿠버네티스 애플리케이션 전체를 하나의 패키지(차트)로 묶어서 설치·업그레이드·삭제를 단일 명령으로 처리한다.

초보자는 처음에 이렇게 이해하면 된다.

`차트(패키지 정의) → helm install → 릴리스(실행 중인 인스턴스)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 차트(Chart) | 앱 배포에 필요한 모든 YAML 템플릿과 설정을 묶은 패키지. |
| values.yaml | 차트의 기본 설정값 파일. 환경별로 이 파일만 바꿔서 재사용한다. |
| 릴리스(Release) | 클러스터에 배포된 차트의 실행 인스턴스. 이름으로 구분하고 버전이 추적된다. |
| 리포지토리(Repository) | 차트를 저장하고 공유하는 저장소. Bitnami 같은 공개 저장소가 있다. |
| helm template | 실제 배포 없이 렌더링된 YAML을 미리 출력해보는 검증 명령. |

## 예를 들어 설명하면

Bitnami의 WordPress 차트로 WordPress와 MariaDB를 한 번에 배포하는 흐름이다.

```bash
# 저장소 추가 및 업데이트
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# WordPress 배포 (my-blog라는 릴리스 이름으로)
helm install my-blog bitnami/wordpress

# 배포된 릴리스 목록 확인
helm list

# 설정 변경 후 업그레이드
helm upgrade my-blog bitnami/wordpress --set replicaCount=2

# 이전 버전으로 롤백
helm rollback my-blog 1

# 삭제
helm uninstall my-blog
```

`helm install` 한 줄이 Deployment, Service, PVC 등 수십 줄의 YAML을 대신 처리한다.

## 이 단계에서 중요한 판단 기준

`helm install`이나 `helm upgrade` 전에 반드시 `helm template`으로 실제 생성될 YAML을 먼저 확인한다. 예상치 못한 리소스가 만들어지는 것을 배포 전에 잡을 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**Helm은 쿠버네티스 앱을 차트 단위로 패키징해서 설치·업그레이드·롤백을 명령 한 줄로 처리한다.**

## 나중에 더 깊게 들어가면

- Go 템플릿 문법을 활용한 커스텀 차트 작성
- values.yaml에서 민감 정보를 분리하는 방법 (Secret, Vault 연동)
- Helmfile이나 Argo CD를 이용한 GitOps 기반 릴리스 관리

---

**원본:** [쿠버네티스(Kubernetes) Helm - 복잡한 애플리케이션 배포의 구세주 — https://memoryhub.tistory.com/546](https://memoryhub.tistory.com/546)
