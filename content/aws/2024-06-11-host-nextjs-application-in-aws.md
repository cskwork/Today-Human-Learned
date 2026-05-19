+++
title = "Next.js 앱을 AWS에서 정적 호스팅하기 — S3 + CloudFront + Route 53"
date = "2024-06-11"
description = "Next.js 앱을 정적으로 빌드해 S3에 올리고, CloudFront로 전 세계에 빠르게 배포하고, Route 53으로 도메인을 연결하면 서버 없이 운영할 수 있다."
tags = ["aws"]
categories = ["aws"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Next.js 앱을 정적으로 빌드해 S3에 올리고, CloudFront로 전 세계에 빠르게 배포하고, Route 53으로 도메인을 연결하면 서버 없이 운영할 수 있다.

---

## 이 구조를 왜 쓰는지 감 잡기

Next.js는 서버가 필요한 SSR(서버 사이드 렌더링) 모드와 서버 없이 동작하는 정적 내보내기(static export) 모드 모두 지원한다. 정적 내보내기를 선택하면 모든 페이지가 HTML/CSS/JS 파일로 생성되므로 S3처럼 파일만 저장하는 저장소에서도 서비스할 수 있다. 서버를 따로 관리할 필요가 없어 운영 비용과 복잡도가 낮아진다.

S3는 파일을 저장하고, CloudFront는 세계 각지의 엣지 서버에 파일을 캐싱해 빠르게 응답하며, Route 53은 도메인 주소를 CloudFront로 연결하는 역할을 한다.

초보자는 처음에 이렇게 이해하면 된다.

`Next.js 정적 빌드 → S3 업로드 → CloudFront 배포 → Route 53 DNS 연결`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 정적 내보내기(Static Export) | Next.js가 모든 페이지를 미리 렌더링해 HTML 파일로 저장하는 빌드 방식. 서버 없이 배포 가능하다. |
| S3 (Simple Storage Service) | AWS 파일 저장소. 정적 웹사이트 호스팅 기능을 켜면 파일을 HTTP로 직접 서빙한다. |
| CloudFront | AWS의 CDN(Content Delivery Network). 세계 각지 엣지 서버에 파일을 캐싱해 응답 속도를 높인다. |
| Route 53 | AWS DNS 서비스. 도메인(example.com)을 CloudFront 주소로 연결하는 레코드를 관리한다. |
| Origin | CloudFront가 파일을 가져오는 원본 위치. 이 경우 S3 버킷이 origin이다. |

## 예를 들어 설명하면

**1단계: Next.js 정적 빌드 설정**

`next.config.js`에 `output: 'export'`를 추가하고 빌드한다.

```js
// next.config.js
module.exports = {
  output: 'export',
}
```

```bash
npm run build
# 빌드 결과물이 out/ 디렉터리에 생성된다
```

**2단계: S3에 업로드**

S3 콘솔에서 버킷을 생성하고 정적 웹사이트 호스팅을 활성화한 뒤, `out/` 디렉터리 내용을 업로드한다. 버킷 정책에서 퍼블릭 읽기를 허용하거나, CloudFront OAC(Origin Access Control)를 사용해 S3를 비공개로 유지하는 것이 보안상 더 좋다.

**3단계: CloudFront 배포 생성**

- Origin: S3 버킷 엔드포인트 지정
- Viewer Protocol Policy: HTTP를 HTTPS로 리다이렉트
- Default Root Object: `index.html`

**4단계: Route 53 DNS 연결**

Route 53에서 도메인의 Hosted Zone을 열고, A 레코드(Alias)를 CloudFront 배포 도메인으로 설정한다.

## 이 단계에서 중요한 판단 기준

Next.js 앱에 동적 라우팅(API Routes, 서버 컴포넌트)이 필요하다면 정적 내보내기가 불가능하므로, 그 경우에는 S3 대신 EC2, ECS, 또는 Amplify를 먼저 검토하라.

## 한 줄 요약 — 이것만 기억하면 된다

**Next.js 정적 빌드 결과물을 S3에 올리고 CloudFront로 캐싱하면, 서버 없이 글로벌 속도로 웹 서비스를 운영할 수 있다.**

## 나중에 더 깊게 들어가면

- CloudFront OAC 설정 — S3를 비공개로 유지하면서 CloudFront만 접근 허용하기
- Cache Invalidation — 배포 후 CloudFront 캐시를 즉시 갱신하는 방법
- CI/CD 연동 — GitHub Actions로 빌드·S3 업로드·캐시 무효화를 자동화하기

---

**원본:** [Host Nextjs application in AWS — https://memoryhub.tistory.com/263](https://memoryhub.tistory.com/263)
