+++
title = "Ansible vs Terraform — 둘 중 하나만 골라야 할까"
date = "2026-04-26"
description = "Terraform은 인프라 생성·변경 관리(프로비저닝), Ansible은 서버 설정·운영(구성 관리)에 특화돼 있다. 실무에선 둘 다 쓰는 것이 가장 안정적인 출발선이다."
tags = ["tool", "devops", "terraform", "ansible", "iac"]
categories = ["tool"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Terraform은 인프라 **생성·변경 관리**(프로비저닝), Ansible은 서버 **설정·운영**(구성 관리)에 특화돼 있다. 둘은 경쟁이 아니라 보완 관계이고, **Terraform → Ansible 순으로 함께 쓰는 게 실무 기본형**이다.

---

## 왜 헷갈리는지

둘 다 "자동화 도구"라는 점에서 묶이지만 담당 영역이 다르다.

- **Terraform**: 인프라를 **만들고** 변경한다.
- **Ansible**: 만들어진 서버를 **설정하고** 운영한다.

## 한 표로 보기

| 구분 | Terraform | Ansible |
|------|-----------|---------|
| **핵심 목적** | 인프라 생성·변경 관리 | 서버 설정·배포·운영 자동화 |
| **주 사용 영역** | 클라우드 리소스, 네트워크, VM, DB, DNS | 패키지 설치, 설정 파일 배포, 서비스 재시작 |
| **접근 방식** | 선언형 중심 | 절차형 + 선언형 혼합 |
| **상태 관리** | state 파일로 리소스 추적 | 기본적으로 상태 파일 없음 |
| **파일 형식** | HCL | YAML |
| **실무 포지션** | Day 0 / Day 1 인프라 프로비저닝 | Day 1 / Day 2 구성 관리·운영 |

## 핵심 용어

| 용어 | 설명 |
|------|------|
| **프로비저닝** | 서버·네트워크·DB 같은 인프라 자원을 새로 만드는 일 |
| **구성 관리** | 만들어진 서버 안에 필요한 설정을 맞추는 일 |
| **IaC** | 인프라를 코드처럼 작성하고 버전 관리하는 방식 |
| **멱등성** | 여러 번 실행해도 결과가 안정적으로 같은 상태가 되는 성질 |

## 코드 한 줄로 차이 보기

### Terraform

```hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"
}
```

→ "이런 인프라 리소스를 만들겠다"는 **선언**.

### Ansible

```yaml
- name: Install nginx
  hosts: web
  become: true
  tasks:
    - name: Ensure nginx is installed
      ansible.builtin.package:
        name: nginx
        state: present
```

→ "서버 안의 상태를 이렇게 맞춘다"는 **절차**.

## 상황별 빠른 선택

| 상황 | 추천 |
|------|------|
| AWS에 VPC와 EC2를 만들고 싶다 | Terraform |
| 만든 EC2에 Nginx를 설치하고 싶다 | Ansible |
| 여러 서버에 동일한 보안 설정을 배포하고 싶다 | Ansible |
| 클라우드 리소스 변경 이력을 코드로 관리하고 싶다 | Terraform |
| 온프레미스 서버 설정을 표준화하고 싶다 | Ansible |
| 멀티 클라우드 인프라를 코드로 관리하고 싶다 | Terraform |

## 둘을 함께 쓰는 흐름

```
① Terraform으로 인프라 생성 (VPC, EC2, SG, LB...)
② Terraform output으로 서버 IP/인벤토리 확보
③ Ansible로 서버 설정 (Nginx, 런타임, 환경변수, 배포)
④ 앱 배포
⑤ 운영 중 설정 변경은 Ansible로 반복 적용
```

### 웹 서비스 배포 예시

- **Terraform**: VPC · 보안 그룹 · EC2 · 로드밸런서
- **Ansible**: Nginx 설치 · 앱 런타임 · 환경 변수 · 배포 파일 · 서비스 재시작

역할이 분리되니 코드 구조도 깨끗해지고 장애 원인 분석도 쉬워진다.

## 판단 기준

| 질문 | 추천 |
|------|------|
| 클라우드 인프라를 코드로 만들고 싶은가? | Terraform |
| 서버 설정을 반복 적용하고 싶은가? | Ansible |
| 운영 중 서버가 이미 많은가? | Ansible |
| 신규 클라우드 환경을 표준화해야 하는가? | Terraform |
| 배포·설정 변경까지 자동화하려는가? | Ansible |
| 처음부터 끝까지 인프라 자동화 체계를 만들고 싶은가? | Terraform + Ansible |

## 모범사례 비교

| 패턴 | 장점 | 주의점 |
|------|------|--------|
| Terraform 단독 | 클라우드 리소스 생성·변경 관리가 명확 | 서버 내부 설정까지 끌고 오면 코드 복잡 |
| Ansible 단독 | 기존 서버 운영 자동화에 빠르게 적용 | 클라우드 리소스 상태 추적은 Terraform보다 약함 |
| Terraform + Ansible | 인프라 생성과 서버 설정의 책임 분리 — 실무 표준 | 실행 순서·인벤토리 전달·권한 관리 기준 필요 |

## 주의할 점

| 항목 | 설명 |
|------|------|
| **상태 관리** | Terraform state는 팀 협업에 핵심. 원격 저장소 + 잠금 전략 필수 |
| **책임 분리** | Terraform으로 서버 내부 설정까지 끌고 오지 말 것 |
| **실행 순서** | Terraform → Ansible 순서를 파이프라인에서 명시 |
| **보안** | SSH 키·클라우드 자격증명·secret은 별도 보안 도구로 관리 |
| **라이선스** | Terraform 라이선스 변경 이슈가 있었으므로 조직 정책에 따라 OpenTofu도 검토 |

## 마치며

> "Terraform은 집을 짓는 도구, Ansible은 집 안을 세팅하는 도구."

둘 중 하나만 고르기보다 **"인프라 생성은 Terraform, 서버 설정은 Ansible"**로 역할을 나누는 것이 가장 안정적인 출발점이다.

---

## 핵심 요약

- **역할 분리**: Terraform = 프로비저닝 / Ansible = 구성 관리
- **상태 관리 차이**: Terraform은 state로 리소스 추적, Ansible은 멱등성에 의존
- **실무 모범사례**: 경쟁 아닌 보완. Terraform → Ansible 순으로 파이프라인 구축

## 참고

- 원문: [Ansible vs Terraform, 둘 중 하나만 골라야 할까요?](https://memoryhub.tistory.com/entry/%F0%9F%9A%80-Ansible-vs-Terraform-%EB%91%98-%EC%A4%91-%ED%95%98%EB%82%98%EB%A7%8C-%EA%B3%A8%EB%9D%BC%EC%95%BC-%ED%95%A0%EA%B9%8C%EC%9A%94)
