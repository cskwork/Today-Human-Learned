+++
title = "OpenStack — 내 손으로 짓는 오픈소스 클라우드"
date = "2024-12-18"
description = "OpenStack은 AWS처럼 작동하는 클라우드를 자체 서버에 직접 구축할 수 있는 오픈소스 플랫폼이다."
tags = ["kubernetes"]
categories = ["kubernetes"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> OpenStack은 AWS처럼 작동하는 클라우드를 자체 서버에 직접 구축할 수 있는 오픈소스 플랫폼이다.

---

## OpenStack을 왜 쓰는지 감 잡기

AWS나 GCP 같은 퍼블릭 클라우드는 편리하지만, 사용량에 따라 요금이 변하고 데이터가 외부 업체 서버에 보관된다. 금융, 의료, 공공기관처럼 데이터를 외부에 올릴 수 없는 환경이거나, 고정 워크로드가 크고 장기 비용을 줄여야 하는 경우라면 자체 데이터센터에 클라우드 환경을 만드는 것이 합리적이다.

OpenStack은 바로 이 역할을 한다. 서버 수십 대를 묶어서 그 위에 가상 머신, 네트워크, 스토리지를 API로 제어할 수 있는 클라우드를 만든다.

초보자는 처음에 이렇게 이해하면 된다.

`물리 서버 여러 대 → OpenStack 설치 → API/대시보드로 VM 생성·관리`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Nova | 가상 머신을 만들고 실행하는 컴퓨팅 서비스. AWS의 EC2에 해당한다. |
| Neutron | 가상 네트워크를 구성하는 서비스. VM 간 통신 경로를 만든다. |
| Cinder | VM에 붙이는 영구 블록 스토리지 서비스. AWS의 EBS에 해당한다. |
| Keystone | 모든 서비스의 인증과 접근 제어를 담당하는 관문 서비스. |
| Glance | VM 이미지를 저장하고 제공하는 서비스. 부팅에 쓸 OS 이미지를 보관한다. |

## 예를 들어 설명하면

OpenStack CLI로 가상 머신을 만드는 기본 흐름이다.

```bash
# 인스턴스 생성: flavor(사양), image(OS), network, 보안그룹, 키 지정
openstack server create \
  --flavor m1.small \
  --image cirros \
  --network private \
  --security-group default \
  --key-name mykey \
  myserver

# 생성된 인스턴스 목록 확인
openstack server list
```

주요 컴포넌트와 역할을 한눈에 보면 다음과 같다.

| 컴포넌트 | 역할 |
|---|---|
| Nova | VM 생성·관리 |
| Glance | OS 이미지 보관 |
| Neutron | 가상 네트워크 |
| Cinder | 블록 스토리지 |
| Swift | 오브젝트 스토리지 |
| Keystone | 인증·권한 |
| Horizon | 웹 대시보드 |

## 이 단계에서 중요한 판단 기준

모든 컴포넌트를 한꺼번에 설치할 필요는 없다. 필요한 서비스만 골라 설치하되, Keystone과 Nova는 사실상 필수이고 나머지는 요구 사항에 따라 추가한다.

## 한 줄 요약 — 이것만 기억하면 된다

**OpenStack은 자체 서버로 AWS 같은 클라우드를 직접 구축하는 오픈소스 플랫폼이다.**

## 나중에 더 깊게 들어가면

- HA(고가용성) 구성: 컨트롤 플레인 노드 3중화와 Pacemaker/Corosync
- DevStack과 Packstack을 이용한 빠른 테스트 환경 구성
- OpenStack과 쿠버네티스 연동(Magnum)

---

**원본:** [OpenStack - 클라우드 컴퓨팅의 오픈소스 혁명 — https://memoryhub.tistory.com/417](https://memoryhub.tistory.com/417)
