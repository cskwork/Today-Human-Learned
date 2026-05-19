+++
title = "원격 함수 호출의 정석 — RPC Interface 가이드"
date = "2025-11-11"
description = "RPC는 다른 컴퓨터에 있는 함수를 마치 내 코드 안의 함수처럼 호출하게 해주는 통신 방식이다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> RPC는 다른 컴퓨터에 있는 함수를 마치 내 코드 안의 함수처럼 호출하게 해주는 통신 방식이다.

---

## RPC를 왜 쓰는지 감 잡기

마이크로서비스 환경에서는 결제 서비스, 재고 서비스, 알림 서비스가 각각 별도 서버에서 돌아간다. 이들이 서로 데이터를 주고받을 때 소켓을 직접 열고, 데이터를 직렬화하고, 오류를 처리하는 코드를 매번 짜는 건 비효율적이다. RPC는 이 반복 작업을 프레임워크 레벨에서 감춘다. 호출하는 쪽은 그냥 함수 이름과 인자를 넘기면 된다.

핵심 흐름은 이렇다.

`클라이언트 함수 호출 → Stub이 직렬화 → 네트워크 전송 → 서버 Stub 역직렬화 → 실제 함수 실행 → 역순으로 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| RPC | Remote Procedure Call, 원격 서버의 함수를 로컬 함수처럼 호출하는 방식 |
| Stub | 클라이언트/서버 양쪽에서 네트워크 통신을 대신 처리하는 자동 생성 코드 |
| IDL | Interface Definition Language, 서비스 인터페이스를 언어 중립적으로 기술하는 파일 (.proto 등) |
| Marshalling | 함수 인자를 네트워크로 전송 가능한 바이트 형태로 변환하는 과정 |
| gRPC | Google이 만든 현대적 RPC 프레임워크, HTTP/2와 Protocol Buffers를 사용 |

## 예를 들어 설명하면

gRPC로 계산기 서비스를 만들 때, 먼저 `.proto` 파일로 인터페이스를 정의한다.

```protobuf
syntax = "proto3";

service Calculator {
  rpc Add(CalculateRequest) returns (CalculateResponse) {}
}

message CalculateRequest {
  int32 a = 1;
  int32 b = 2;
}

message CalculateResponse {
  int32 result = 1;
}
```

이 파일 하나로 클라이언트 Stub과 서버 Stub 코드가 자동 생성된다. 클라이언트는 `stub.Add(a=10, b=20)`을 호출하고, 서버는 `Add` 메서드를 구현하면 된다. 네트워크 통신 코드는 프레임워크가 모두 처리한다.

## 이 단계에서 중요한 판단 기준

외부에 노출하는 공개 API라면 REST, 내부 마이크로서비스 간 통신이라면 gRPC가 일반적으로 유리하다 — gRPC는 바이너리 직렬화와 HTTP/2 멀티플렉싱으로 REST 대비 3~10배 빠른 처리량을 낸다.

## 한 줄 요약 — 이것만 기억하면 된다

**RPC는 네트워크를 함수 호출처럼 보이게 만들고, gRPC는 그 구현 중 가장 실용적인 선택이다.**

## 나중에 더 깊게 들어가면

- gRPC 스트리밍 (단방향, 양방향) 패턴
- .proto 파일 버전 관리와 하위 호환성 유지 전략
- gRPC-Web 게이트웨이를 통한 브라우저 연동 방법

---

**원본:** [원격 함수 호출의 정석, RPC Interface 가이드](https://memoryhub.tistory.com/905)
