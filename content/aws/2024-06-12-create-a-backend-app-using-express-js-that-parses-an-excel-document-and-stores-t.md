+++
title = "Express.js로 Excel 파싱 후 AWS MySQL에 저장하기"
date = "2024-06-12"
description = "multer로 파일을 받고, xlsx로 파싱하고, mysql2로 RDS에 삽입하는 세 라이브러리 조합이 핵심이다."
tags = ["aws"]
categories = ["aws"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> multer로 파일을 받고, xlsx로 파싱하고, mysql2로 RDS에 삽입하는 세 라이브러리 조합이 핵심이다.

---

## 이 구조를 왜 쓰는가

업무 현장에서 Excel 파일로 데이터를 주고받는 경우는 많다. 이 데이터를 DB에 자동으로 적재하려면 파일 업로드를 받아주는 서버, Excel을 읽는 파서, DB에 삽입하는 클라이언트가 필요하다. Express.js는 이 세 가지를 가볍게 묶어주는 Node.js 웹 프레임워크다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 클라이언트가 Excel 업로드 → multer가 파일 저장 → xlsx가 파싱 → mysql2가 RDS에 삽입`

---

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| multer | Express에서 파일 업로드를 처리하는 미들웨어. 파일을 서버 디스크에 임시 저장한다. |
| xlsx | Excel 파일(.xlsx/.xls)을 읽어 JSON 배열로 변환해 주는 파싱 라이브러리. |
| mysql2 | Node.js에서 MySQL에 연결하는 드라이버. Promise 기반 API를 지원한다. |
| AWS RDS | 관리형 MySQL 호스팅 서비스. 직접 서버를 설치하지 않아도 MySQL을 사용할 수 있다. |
| INSERT INTO ... SET ? | mysql2가 지원하는 편의 문법으로, 객체를 그대로 컬럼-값 쌍으로 삽입한다. |

---

## 예를 들어 설명하면

아래는 핵심 흐름을 담은 서버 코드다.

```js
const express = require('express');
const multer  = require('multer');
const xlsx    = require('xlsx');
const mysql   = require('mysql2');

const app    = express();
const upload = multer({ dest: 'uploads/' });

const connection = mysql.createConnection({
  host:     process.env.DB_HOST,
  user:     process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
});
connection.connect();

app.post('/upload', upload.single('file'), (req, res) => {
  const workbook = xlsx.readFile(req.file.path);
  const sheet    = workbook.Sheets[workbook.SheetNames[0]];
  const rows     = xlsx.utils.sheet_to_json(sheet);

  rows.forEach(row => {
    connection.query('INSERT INTO your_table SET ?', row, err => {
      if (err) console.error('삽입 실패:', err);
    });
  });

  res.send('업로드 및 저장 완료');
});

app.listen(3000);
```

DB 접속 정보는 반드시 환경 변수로 관리한다. 코드에 직접 쓰지 않는다.

RDS에서 테이블을 미리 생성해 두어야 한다.

```sql
CREATE TABLE your_table (
  id      INT AUTO_INCREMENT PRIMARY KEY,
  column1 VARCHAR(255),
  column2 VARCHAR(255)
);
```

---

## 이 단계에서 중요한 판단 기준

`rows.forEach` 안에서 `connection.query`를 호출하면 행마다 별도 쿼리가 실행된다. 행이 수천 개라면 `INSERT INTO ... VALUES (...)` 배치 삽입으로 전환해야 성능 문제가 생기지 않는다.

---

## 한 줄 요약 — 이것만 기억하면 된다

**multer(업로드) + xlsx(파싱) + mysql2(삽입), 이 세 라이브러리가 Express Excel 적재 파이프라인의 전부다.**

---

## 나중에 더 깊게 들어가면

- 대용량 Excel 처리를 위한 스트림 기반 파싱(exceljs의 스트림 모드)
- 배치 INSERT로 수천 행을 한 번에 삽입하는 방법
- multer 저장 위치를 S3로 대체해 서버 디스크를 거치지 않는 아키텍처

---

**원본:** [Create a backend app using Express.js that parses an Excel document and stores the data into a MySQL database hosted on AWS — https://memoryhub.tistory.com/289](https://memoryhub.tistory.com/289)
