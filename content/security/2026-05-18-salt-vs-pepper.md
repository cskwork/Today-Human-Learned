+++
title = "Salt vs Pepper — 비밀번호 해시에서 둘이 다른 이유 (2026-05-18)"
date = "2026-05-18"
description = "- **Salt**: 사용자마다 다른 랜덤값. **DB에 같이 저장**해도 됨. 목적은 \"같은 비밀번호여도 다른 hash가 나오게 하는 것\". - **Pepper**: 시스템 전체가 공유하는 비밀값. **DB 밖**(Vault, Secret Manager, env)에 둠. 목적은 \"DB만 털려도 hash 깨기 어렵게 만드는 것\". - 둘은 **방어하는 위협이 다르다.** Salt는 rainbow table / 같은 비밀번호 식별 방어, Pepper는 DB 유출 단독 시나리오 방어. 그래서 둘 다"
tags = ["security"]
categories = ["security"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> - **Salt**: 사용자마다 다른 랜덤값. **DB에 같이 저장**해도 됨. 목적은 "같은 비밀번호여도 다른 hash가 나오게 하는 것".
> - **Pepper**: 시스템 전체가 공유하는 비밀값. **DB 밖**(Vault, Secret Manager, env)에 둠. 목적은 "DB만 털려도 hash 깨기 어렵게 만드는 것".
> - 둘은 **방어하는 위협이 다르다.** Salt는 rainbow table / 같은 비밀번호 식별 방어, Pepper는 DB 유출 단독 시나리오 방어. 그래서 둘 다 쓸 수 있고, 보통 Salt는 필수 / Pepper는 옵션.

---

## 1. 왜 그냥 해시하면 안 되나

비밀번호 `1234`를 그냥 해시:

```text
1234 → e807f1fcf82d132f9bb018ca6738a19f
```

문제 두 가지:

1. **같은 비밀번호 → 같은 hash.** DB가 털리면 공격자가 hash만 봐도 "이 두 사용자는 같은 비밀번호를 쓴다"를 안다.
2. **Rainbow table 공격.** 흔한 비밀번호 + 사전 계산된 hash 매핑이 인터넷에 널려 있다. 그냥 해시한 비밀번호는 lookup 한 번에 깨진다.

## 2. Salt — "같은 입력을 다르게 보이게"

사용자마다 다른 랜덤값을 붙여 해시:

```text
사용자 A: 1234 + 랜덤값A → hashA
사용자 B: 1234 + 랜덤값B → hashB
```

비밀번호가 같아도 Salt가 다르면 hash가 달라진다. 결과:

- **같은 비밀번호 식별 불가** — A와 B가 같은 비밀번호를 쓰는지 알 수 없다.
- **Rainbow table 무력화** — 공격자가 미리 계산하려면 *Salt마다 따로* 계산해야 한다. 비현실적.

### Salt는 비밀이 아니다

핵심 포인트. **Salt는 공개돼도 괜찮다.** DB에 hash와 같이 저장하는 게 표준.

```text
DB:
- user_id
- salt        ← 평문 저장 OK
- password_hash
```

왜 괜찮은가? Salt의 목적은 "각 입력을 다르게 만들기"이지 "숨기기"가 아니다. 공격자가 Salt를 알아도 여전히 비밀번호 자체는 모르고, 사전 계산 공격도 사용자마다 따로 해야 한다.

## 3. Pepper — "DB 밖의 비밀값"

시스템이 따로 가지고 있는 비밀값. Vault, AWS Secrets Manager, 환경변수 등에 저장.

```text
비밀번호 + Salt + Pepper → hash
```

DB에는 `salt`와 `hash`만 들어가고, **Pepper는 DB에 없다.**

### 어떤 위협을 방어하나

시나리오: **DB만 단독으로 유출**되는 경우 (가장 흔한 사고 유형).

- Salt만 있을 때: 공격자가 hash + salt를 다 봐서 brute force 시도 가능 (오래 걸리지만 가능).
- Pepper 추가 시: 공격자가 가진 정보로는 hash를 재현할 수 없다. **Pepper도 같이 털려야 깰 수 있음.**

즉 Pepper는 **"DB 단독 유출"이라는 부분 침해 시나리오에 대한 추가 방어선**이다.

## 4. 비교

| 구분 | Salt | Pepper |
|---|---|---|
| 비유 | 사용자마다 다른 소금 | 서버만 아는 비밀 후추 |
| 저장 위치 | DB에 같이 | DB 밖 (Vault / Secret Manager / env) |
| 비밀 여부 | 비밀 아님 | **비밀이어야 함** |
| 값의 개수 | 데이터(사용자)마다 다름 | 보통 시스템 공통 1개 |
| 주된 방어 대상 | Rainbow table, 같은 비밀번호 식별 | DB 단독 유출 |
| 실무 위상 | **거의 필수** | 추가 보안 옵션 |

## 5. 실무 권장

### bcrypt / argon2 / scrypt를 써라

직접 `sha256(password + salt)` 같은 걸 구현하지 마라. **KDF(Key Derivation Function) 라이브러리**를 써라.

- **bcrypt** — 가장 보편적. Salt를 라이브러리가 알아서 처리하고 hash 문자열에 같이 인코딩한다.
- **argon2** — 현대 권장 (PHC 우승자). 메모리 hard.
- **scrypt** — 메모리 hard, argon2 이전 표준.

`bcrypt.hash("1234")` 한 줄이면 Salt 자동 생성/포함이다. 직접 다룰 일이 거의 없다.

### Pepper는 추가하려면 별도 단계로

bcrypt 같은 KDF는 Salt를 알아서 처리하지만 Pepper는 모른다. 적용 패턴:

```text
hash = bcrypt(HMAC_SHA256(password, pepper))
또는
hash = bcrypt(password + pepper)
```

HMAC 쪽이 더 안전 (단순 concat은 길이 확장 공격 등 미묘한 함정이 있음).

### Pepper 운영 함정

1. **하드코딩 금지.** 소스코드/Git에 들어가면 의미 없음. 환경변수, Vault, Secret Manager 사용.
2. **Pepper 교체는 어렵다.** 바꾸면 기존 모든 비밀번호 hash 검증이 깨진다. 교체 시 보통 다중 Pepper 지원(이전 것으로 검증되면 새 것으로 재저장) 구조가 필요. **이걸 미리 설계 안 하면 사실상 영구 고정값이 된다.**
3. **Pepper 유출은 치명적.** 공유 비밀이라 한 번 새면 모든 사용자 영향. Salt보다 운영 책임이 무겁다.

## 6. 한 줄 요약

> Salt는 **공개돼도 되는 "다양화 장치"**, Pepper는 **숨겨야 하는 "추가 자물쇠"**.
> 둘은 보완재고 대체재가 아니다. **Salt 없이 Pepper만 쓰는 건 의미가 없다.**

## 7. 흔한 오해

| 오해 | 사실 |
|---|---|
| Salt를 숨겨야 한다 | 아니다. 공개돼도 무방. DB에 같이 저장하는 게 표준. |
| Salt 하나를 모든 사용자에 공유해도 된다 | 안 된다. 같은 비밀번호 식별이 다시 가능해진다. **사용자마다 달라야 함.** |
| bcrypt 쓰면 Pepper도 자동 처리 | 아니다. bcrypt는 Salt만 처리. Pepper는 호출 코드가 직접 결합해야 함. |
| Pepper만 있어도 Salt 없어도 된다 | 안전성 저하. 같은 비밀번호 사용자 식별이 가능. **둘은 다른 위협을 막는다.** |
| 한 번 정한 Pepper는 영원 | 교체 가능하지만 다중 Pepper 지원 설계가 미리 필요. 사후 추가는 매우 비싸다. |

---

**참고**
- OWASP Password Storage Cheat Sheet — salt/pepper 권고 사항
- RFC 5869 (HKDF), Argon2 paper — KDF 동작 원리
- bcrypt / argon2 / scrypt 공식 문서
