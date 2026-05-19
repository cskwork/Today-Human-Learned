+++
title = "macOS에서 Python SSL 인증서 문제 해결하기"
date = "2025-03-18"
description = "macOS의 Python은 시스템 Keychain을 참조하지 않아서 SSL 검증이 실패한다. `Install Certificates.command`를 실행하거나 certifi 패키지를 쓰면 해결된다."
tags = ["network"]
categories = ["network"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> macOS의 Python은 시스템 Keychain을 참조하지 않아서 SSL 검증이 실패한다. `Install Certificates.command`를 실행하거나 certifi 패키지를 쓰면 해결된다.

---

## 이 문제를 왜 겪는지 감 잡기

HTTPS로 데이터를 주고받을 때 Python은 서버 인증서가 신뢰할 수 있는 기관(CA)이 서명한 것인지 확인한다. macOS는 자체 인증서 저장소(Keychain Access)를 쓰는데, python.org 인스톨러로 설치한 Python은 이 저장소를 기본으로 참조하지 않는다. 그래서 `CERTIFICATE_VERIFY_FAILED` 오류가 발생한다. 특히 NLTK나 requests처럼 HTTPS 다운로드가 필요한 패키지에서 자주 마주친다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Python HTTPS 요청 → SSL 인증서 검증 시도 → 시스템 CA 저장소 참조 실패 → 오류`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SSL/TLS | 인터넷 통신을 암호화하는 프로토콜. HTTPS의 S가 바로 이것이다. |
| CA(인증 기관) | 서버 인증서가 진짜인지 보증해 주는 기관. DigiCert, Let's Encrypt 등이 있다. |
| certifi | Python에서 신뢰할 수 있는 CA 목록을 제공하는 패키지. OS에 의존하지 않는다. |
| Keychain Access | macOS의 인증서·비밀번호 저장소. Python은 여기에 접근하지 못하는 것이 문제의 원인이다. |
| Install Certificates.command | Python.org 인스톨러가 함께 설치하는 스크립트. 실행하면 certifi를 이용해 인증서를 연결해 준다. |

## 예를 들어 설명하면

**권장 방법: Install Certificates.command 실행**

```bash
# Python 3.10 기준 (버전 숫자를 실제 설치 버전으로 바꾼다)
/Applications/Python\ 3.10/Install\ Certificates.command
```

**대안: certifi 패키지 직접 사용**

```python
import certifi
import ssl
import nltk

nltk.download('punkt', ssl_context=ssl.create_default_context(cafile=certifi.where()))
```

SSL 검증을 끄는 방법(`ssl._create_unverified_context`)은 임시 디버깅용으로만 허용하고, 프로덕션이나 반복 사용 코드에는 절대 남겨두지 않는다.

## 이 단계에서 중요한 판단 기준

Python을 새로 설치하면 `Install Certificates.command`를 즉시 실행하고, 가상 환경을 만들면 `pip install --upgrade certifi`를 습관적으로 추가한다.

## 한 줄 요약 — 이것만 기억하면 된다

**macOS Python SSL 오류는 `Install Certificates.command` 실행 또는 certifi 패키지 설치로 해결하며, SSL 검증 비활성화는 임시 수단에 불과하다.**

## 나중에 더 깊게 들어가면

- Python 가상 환경에서의 인증서 상속 문제와 `pip install --upgrade certifi` 타이밍
- 기업 네트워크의 인터셉트 프록시(MITM 인증서) 환경에서의 추가 CA 설정
- `REQUESTS_CA_BUNDLE` 환경변수를 이용한 전역 인증서 경로 지정

---

**원본:** [macOS에서 Python SSL 인증서 문제 해결하기 — https://memoryhub.tistory.com/478](https://memoryhub.tistory.com/478)
