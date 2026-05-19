+++
title = "Microsoft Entra에서 Outlook API 인증 완벽 가이드: Single vs Multi-Tenant"
date = "2025-09-29"
description = "Client Secret을 생성하면 Value가 딱 한 번만 표시된다. 그 값을 즉시 저장하지 않으면 다시 볼 수 없고, Secret ID는 인증에 쓰이지 않는다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Client Secret을 생성하면 Value가 딱 한 번만 표시된다. 그 값을 즉시 저장하지 않으면 다시 볼 수 없고, Secret ID는 인증에 쓰이지 않는다.

---

## Microsoft Entra 앱 등록을 왜 쓰는지 감 잡기

Outlook API로 메일 자동화를 구현하려면 Microsoft Entra ID에 앱을 등록해야 한다. 앱 등록이란 "이 서비스가 누구의 메일함에 접근할 수 있는지"를 Microsoft에 선언하는 과정이다. 예전 Azure Portal과 UI가 달라지면서 Client ID와 Secret ID를 혼동하거나 어디서 값을 복사해야 할지 헷갈리는 경우가 많다.

Single-tenant 앱은 등록한 조직 내부에서만 동작한다. Multi-tenant 앱은 다른 조직의 사용자도 로그인할 수 있다.

`앱 등록 → Client ID 발급 → Client Secret 생성 → API 권한 설정 → OAuth 토큰 발급`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Application (Client) ID | 앱의 주민등록번호. 모든 API 호출에 필요한 고유 GUID |
| Client Secret Value | 앱의 비밀번호. 생성 직후 딱 한 번만 표시되며 이후 재확인 불가 |
| Secret ID | Secret을 식별하는 번호. 인증에는 쓰이지 않는다 |
| Single-tenant | 앱을 등록한 조직 하나에서만 로그인 가능한 설정 |
| Multi-tenant | 여러 조직의 사용자가 로그인할 수 있는 설정. OAuth 엔드포인트를 /common으로 변경해야 함 |

## 예를 들어 설명하면

**앱 등록 및 Secret 생성 순서:**

1. entra.microsoft.com → Identity > Applications > App registrations → New registration
2. Supported account types에서 Single/Multi-tenant 선택 후 Register
3. Overview 탭에서 Application (client) ID 복사 (Object ID가 아님)
4. Certificates & secrets → New client secret → Add
5. 생성 직후 Value 열의 값을 즉시 복사해서 안전한 곳에 저장

**Single → Multi-tenant 전환:**

```
# Azure CLI로 변경
az ad app update \
  --id <APPLICATION_CLIENT_ID> \
  --set signInAudience=AzureADandPersonalMicrosoftAccount
```

인증 엔드포인트도 함께 변경해야 한다.

| 상황 | 엔드포인트 |
|---|---|
| Single-tenant | https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/token |
| Multi-tenant (모든 사용자) | https://login.microsoftonline.com/common/oauth2/v2.0/token |
| Multi-tenant (조직만) | https://login.microsoftonline.com/organizations/oauth2/v2.0/token |

## 이 단계에서 중요한 판단 기준

Supported account types와 OAuth 엔드포인트를 함께 변경하지 않으면 AADSTS50194 오류가 발생한다. 두 설정은 항상 쌍으로 맞춰야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Secret은 Value를 즉시 저장하고, Multi-tenant 전환 시 Supported account types와 엔드포인트를 동시에 변경해야 한다.**

## 나중에 더 깊게 들어가면

- Application 권한(Mail.Send)과 위임 권한(Delegated)의 차이 및 관리자 동의 흐름
- Publisher Verification으로 다른 테넌트 사용자의 동의 화면에서 "Unverified" 경고 제거
- Azure Key Vault를 이용한 Client Secret 만료 자동 갱신

---

**원본:** Microsoft Entra에서 Outlook API 인증 완벽 가이드: Single vs Multi-Tenant — https://memoryhub.tistory.com/799
