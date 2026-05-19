# PostgreSQL — 오픈소스 관계형 데이터베이스 입문

> **TL;DR**
> PostgreSQL은 SQL 표준을 충실히 따르는 오픈소스 RDBMS로, 복잡한 쿼리와 데이터 무결성이 필요한 프로젝트에 잘 맞는다.

---

## PostgreSQL을 왜 쓰는지 감 잡기

데이터를 파일에 그냥 저장하면 나중에 "이 사람이 구매한 것 중 10만원 넘는 것만 최신순으로 보여줘" 같은 요구에 대응하기가 어렵다. 데이터베이스는 이런 조건 검색, 정렬, 집계를 빠르게 처리하도록 설계된 전문 저장소다.

PostgreSQL은 그중에서도 1986년부터 개발된 검증된 오픈소스 시스템이다. 무료이고, SQL 표준 준수도가 높으며, JSON 저장, 배열 타입, 지리 정보 처리 같은 확장 기능도 갖추고 있다.

`핵심 흐름: 데이터베이스 생성 → 테이블 정의 → SQL로 CRUD 수행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 테이블(Table) | 행과 열로 구성된 데이터 저장 단위. 엑셀 시트와 비슷하다 |
| 행(Row) | 테이블 안의 한 건의 데이터. 사람 한 명, 주문 한 건 등 |
| 열(Column) | 각 데이터 항목의 종류. 이름, 나이, 가입일 같은 속성 |
| Primary Key | 각 행을 유일하게 식별하는 값. 중복 불가, NULL 불가 |
| Foreign Key | 다른 테이블의 Primary Key를 참조해서 두 테이블을 연결하는 열 |

## 예를 들어 설명하면

도서관 관리 시스템을 만든다고 하자. 책 정보를 저장하는 테이블을 만들고 데이터를 조작하는 기본 흐름은 다음과 같다.

```sql
-- 테이블 생성
CREATE TABLE books (
  id             SERIAL PRIMARY KEY,
  title          VARCHAR(100),
  author         VARCHAR(100),
  published_date DATE
);

-- 데이터 삽입
INSERT INTO books (title, author, published_date)
VALUES ('채식주의자', '한강', '2007-10-30');

-- 조회
SELECT * FROM books WHERE author = '한강';

-- 수정
UPDATE books SET title = '채식주의자 (개정판)' WHERE id = 1;

-- 삭제
DELETE FROM books WHERE id = 1;
```

`SERIAL`은 PostgreSQL에서 자동 증가 정수를 만드는 단축 표기다. 별도로 시퀀스를 만들 필요가 없다.

## 이 단계에서 중요한 판단 기준

여러 테이블의 데이터를 JOIN으로 연결해서 쓰거나, 트랜잭션 무결성이 중요한 업무(결제, 재고 관리 등)라면 PostgreSQL 같은 관계형 DB가 적합하다.

## 한 줄 요약 — 이것만 기억하면 된다

**PostgreSQL은 무료이면서 SQL 표준을 잘 따르는 관계형 DB로, 복잡한 쿼리와 안정성이 필요한 곳에 쓴다.**

## 나중에 더 깊게 들어가면

- JOIN: 여러 테이블을 연결해 한 번에 데이터를 조회하는 방법
- 인덱스(Index): 쿼리 속도를 높이는 색인 구조와 언제 만들어야 하는지
- 트랜잭션과 ACID: 여러 쿼리를 하나의 묶음으로 처리해 데이터 일관성을 보장하는 방법

---

**원본:** PostgreSQL Introduced — https://memoryhub.tistory.com/113
