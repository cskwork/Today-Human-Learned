+++
title = "Next.js API와 AWS RDS 연결 후 EC2에 배포하기"
date = "2024-06-12"
description = "Next.js의 API Route에서 RDS에 연결하고, Docker로 패키징해 EC2에 올리면 완전한 서버리스 없는 백엔드 스택이 완성된다."
tags = ["aws"]
categories = ["aws"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Next.js의 API Route에서 RDS에 연결하고, Docker로 패키징해 EC2에 올리면 완전한 서버리스 없는 백엔드 스택이 완성된다.

---

## 이 구조를 왜 쓰는가

Next.js는 프론트엔드 프레임워크지만 `pages/api` 또는 `app/api` 디렉터리를 통해 서버사이드 API를 함께 작성할 수 있다. 별도의 백엔드 서버 없이 Next.js 하나로 DB 조회 API까지 제공하고, 이를 EC2에 올려 직접 운영하는 구조다. 소규모 서비스나 프로토타입에서 인프라를 단순하게 유지하면서 RDS 같은 관리형 DB를 붙이고 싶을 때 선택한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Next.js 프로젝트 생성 → API Route 작성 → RDS 연결 → Docker 빌드 → EC2 배포`

---

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| API Route | Next.js에서 `pages/api/` 또는 `app/api/` 아래 파일을 만들면 자동으로 HTTP 엔드포인트가 된다. |
| AWS RDS | AWS가 관리해 주는 관계형 DB 서비스. MySQL, PostgreSQL 등을 직접 설치 없이 사용한다. |
| Connection Pool | DB 연결을 매 요청마다 새로 만들지 않고 미리 여러 개 열어두고 재사용하는 방식. 성능에 필수다. |
| .env.local | Next.js에서 환경 변수를 저장하는 파일. DB 접속 정보처럼 코드에 직접 쓰면 안 되는 값을 넣는다. |
| Docker | 앱과 실행 환경을 하나의 이미지로 묶어 어디서든 동일하게 실행하게 해 주는 컨테이너 도구. |

---

## 예를 들어 설명하면

아래는 MySQL RDS에 연결하는 핵심 코드다.

```js
// lib/db.js — 커넥션 풀 생성 (한 번만 초기화)
import mysql from 'mysql2/promise';

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
});

export default pool;

// pages/api/items.js — API Route에서 풀을 재사용
import pool from '../../lib/db';

export default async function handler(req, res) {
  const [rows] = await pool.query('SELECT * FROM items');
  res.status(200).json(rows);
}
```

`.env.local`에 DB 접속 정보를 넣고 코드에는 `process.env.DB_HOST` 형태로만 참조한다. 절대 접속 정보를 코드에 하드코딩하지 않는다.

EC2 배포는 Dockerfile을 빌드해 이미지를 만들고 EC2에서 컨테이너를 실행하는 흐름이다.

```
docker build -t my-api .
sudo docker run -d -p 80:3000 my-api
```

---

## 이 단계에서 중요한 판단 기준

RDS의 보안 그룹에서 EC2 인스턴스의 IP 또는 보안 그룹만 3306(MySQL) 포트로 허용해야 한다. 퍼블릭으로 열면 보안 위험이 크다.

---

## 한 줄 요약 — 이것만 기억하면 된다

**Next.js API Route + RDS 커넥션 풀 + Docker 패키징 + EC2 실행, 이 네 단계가 전부다.**

---

## 나중에 더 깊게 들어가면

- Next.js App Router의 Route Handlers와 Pages Router API Routes 차이
- RDS Proxy를 통한 커넥션 풀 관리 및 서버리스 환경 대응
- EC2 대신 ECS(Fargate)나 Elastic Beanstalk으로 배포하는 방법

---

**원본:** [Creating an API with Next.js that connects to an RDBMS in AWS — https://memoryhub.tistory.com/284](https://memoryhub.tistory.com/284)
