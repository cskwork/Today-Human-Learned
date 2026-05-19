+++
title = "Google Calendar MCP 서버 코드 상세 분석"
date = "2025-03-21"
description = "Google Calendar MCP 서버는 AI 모델이 자연어 요청을 Calendar API 호출로 바꿀 수 있도록 OAuth2 인증, Zod 스키마 검증, MCP 도구 등록을 하나로 묶은 Node.js 서버다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Google Calendar MCP 서버는 AI 모델이 자연어 요청을 Calendar API 호출로 바꿀 수 있도록 OAuth2 인증, Zod 스키마 검증, MCP 도구 등록을 하나로 묶은 Node.js 서버다.

---

## 왜 쓰는지 감 잡기

사용자가 Claude에게 "내일 오후 3시에 팀 회의 잡아줘"라고 하면, Claude가 직접 Google Calendar에 이벤트를 만들어야 한다. 그러려면 Calendar API를 호출할 수 있는 중간 서버가 필요하다. 이 MCP 서버가 그 역할을 한다. AI 모델은 MCP 프로토콜로 서버에 요청을 보내고, 서버는 OAuth2로 인증한 뒤 Google Calendar API를 대신 호출한다.

핵심 흐름:

`Claude 요청 → MCP 서버 수신 → Zod 스키마 검증 → OAuth2 인증 → Calendar API 호출 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MCP (Model Context Protocol) | AI 모델과 외부 도구 사이의 통신 규격. 요청과 응답 형식을 표준화한다. |
| OAuth2 | "이 앱이 내 구글 계정에 접근해도 됩니까?" 같은 권한 위임 흐름을 처리하는 인증 프로토콜. |
| Refresh Token | 장기 접근 권한을 유지하는 토큰. 만료된 Access Token을 자동으로 갱신하는 데 쓴다. |
| Zod | TypeScript/JavaScript에서 런타임 데이터 구조를 검증하는 라이브러리. 잘못된 입력을 조기에 차단한다. |
| StdioServerTransport | stdin/stdout으로 메시지를 주고받는 MCP 통신 계층. 프로세스 간 JSON 메시지를 교환한다. |

## 예를 들어 설명하면

이벤트 생성 도구의 핵심 코드 흐름이다.

```typescript
// 1. 입력 스키마 정의 (Zod)
const CreateEventSchema = z.object({
    summary: z.string().describe("Event title"),
    start: z.object({
        dateTime: z.string().describe("Start time (ISO format)"),
        timeZone: z.string().optional(),
    }),
    end: z.object({
        dateTime: z.string().describe("End time (ISO format)"),
        timeZone: z.string().optional(),
    }),
});

// 2. 도구 호출 처리
case "create_event": {
    const validatedArgs = CreateEventSchema.parse(args); // 검증 실패 시 즉시 오류
    const response = await calendar.events.insert({
        calendarId: "primary",
        requestBody: validatedArgs,
    });
    return {
        content: [{ type: "text", text: `Event created: ${response.data.id}` }],
    };
}
```

## 인증 설정

환경변수 세 개로 OAuth2를 구성한다.

```
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
GOOGLE_REFRESH_TOKEN=...
```

Refresh Token을 쓰면 사용자가 매번 로그인하지 않아도 된다. 서버 시작 시 토큰을 자동으로 갱신한다.

## 이 단계에서 중요한 판단 기준

새 Calendar 기능을 추가할 때는 Zod 스키마를 먼저 정의하고, 그다음 switch 케이스에 도구 핸들러를 추가한다. 스키마 없이 입력을 그대로 API에 넘기면 런타임 오류가 디버깅하기 어려운 형태로 나온다.

## 전체 도구 목록

서버가 제공하는 MCP 도구는 다섯 가지다.

| 도구 이름 | 동작 |
|---|---|
| `create_event` | 새 이벤트 생성 |
| `get_event` | 이벤트 ID로 상세 정보 조회 |
| `update_event` | 변경된 필드만 부분 업데이트 (`patch`) |
| `delete_event` | 이벤트 삭제 |
| `list_events` | 시간 범위로 이벤트 목록 조회 |

## 한 줄 요약 — 이것만 기억하면 된다

**이 서버는 "Zod로 입력을 검증하고 → OAuth2로 인증한 뒤 → Calendar API를 호출하고 → MCP 형식으로 결과를 반환한다"는 패턴을 모든 도구에 일관되게 적용한 구조다.**

## 나중에 더 깊게 들어가면

- Google OAuth2 Refresh Token 발급 절차 (Google Cloud Console에서 동의 화면 설정)
- `zodToJsonSchema`로 Zod 스키마를 JSON Schema로 변환해 AI 모델에 도구 명세를 전달하는 방식
- 동일한 패턴으로 Gmail, Google Drive MCP 서버 확장하기

---

**원본:** [Google Calendar MCP 서버 코드 상세 분석](https://memoryhub.tistory.com/494)
