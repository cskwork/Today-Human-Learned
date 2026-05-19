+++
title = "AWS ECR — Docker 이미지를 저장하고 배포하는 컨테이너 레지스트리"
date = "2024-06-10"
description = "Amazon ECR은 Docker 이미지를 AWS에 안전하게 저장하고, ECS나 EKS에서 바로 꺼내 쓸 수 있는 완전 관리형 컨테이너 레지스트리다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Amazon ECR은 Docker 이미지를 AWS에 안전하게 저장하고, ECS나 EKS에서 바로 꺼내 쓸 수 있는 완전 관리형 컨테이너 레지스트리다.

---

## ECR을 왜 쓰는지 감 잡기

Docker로 애플리케이션을 패키징하면 이미지를 어딘가에 올려두어야 서버가 그것을 받아 실행할 수 있다. Docker Hub가 가장 유명한 공개 레지스트리지만, 사내 서비스나 민감한 코드는 공개 저장소에 올릴 수 없다.

Amazon ECR은 AWS 계정 안에 격리된 프라이빗 레지스트리를 제공한다. IAM으로 접근 권한을 제어하고, 이미지는 암호화해서 저장한다. ECS, EKS, Lambda 같은 AWS 서비스와 바로 연동되어, 배포 파이프라인에서 이미지를 가져오는 인증 과정이 간단하다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Docker 이미지 빌드 → ECR에 push → ECS/EKS에서 pull → 컨테이너 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Docker 이미지(Image) | 애플리케이션 실행에 필요한 코드, 라이브러리, 설정을 하나로 묶은 패키지. |
| 레지스트리(Registry) | Docker 이미지를 저장하고 배포하는 중앙 저장소. ECR이 이 역할을 한다. |
| 리포지토리(Repository) | 레지스트리 안에서 하나의 애플리케이션 이미지들을 버전별로 모아두는 공간. |
| 태그(Tag) | 이미지 버전을 구분하는 레이블. 예: `my-app:latest`, `my-app:v1.2.3` |
| Push / Pull | Push는 이미지를 레지스트리에 올리는 것, Pull은 레지스트리에서 이미지를 받아오는 것. |

## 예를 들어 설명하면

웹 애플리케이션 이미지를 ECR에 push하는 전체 흐름이다.

```bash
# 1. Docker 이미지 빌드
docker build -t my-web-app .

# 2. ECR 인증 (AWS 자격증명으로 Docker 로그인)
aws ecr get-login-password --region us-east-1 \
  | docker login --username AWS --password-stdin \
    123456789.dkr.ecr.us-east-1.amazonaws.com

# 3. 이미지에 ECR 주소 태그 지정
docker tag my-web-app:latest \
  123456789.dkr.ecr.us-east-1.amazonaws.com/my-web-app:latest

# 4. ECR에 push
docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/my-web-app:latest
```

이후 ECS 태스크 정의에 이 이미지 URI를 지정하면, ECS가 자동으로 ECR에서 이미지를 받아 컨테이너를 실행한다.

## 이 단계에서 중요한 판단 기준

AWS 기반 컨테이너 워크로드라면 ECR을 기본 선택으로 쓴다. IAM 통합이 자연스럽고, 같은 리전 내 ECS/EKS와의 이미지 전송에 데이터 전송 비용이 들지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**ECR은 AWS 안에 두는 프라이빗 Docker 이미지 저장소로, 빌드한 이미지를 안전하게 보관하고 배포 파이프라인에 공급한다.**

## 나중에 더 깊게 들어가면

- ECR 수명 주기 정책(Lifecycle Policy)으로 오래된 이미지를 자동 삭제하는 방법
- ECR 이미지 취약점 스캔(Image Scanning) 기능 활용
- GitHub Actions에서 ECR로 자동 push하는 CI/CD 파이프라인 구성

---

**원본:** [Elastic Container Registry Introduced — https://memoryhub.tistory.com/248](https://memoryhub.tistory.com/248)
