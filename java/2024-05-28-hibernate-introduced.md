# Hibernate — Java 객체와 DB 테이블을 연결하는 ORM

> **TL;DR**
> Hibernate는 Java 클래스와 관계형 DB 테이블을 자동으로 매핑해, SQL 대신 Java 객체로 데이터를 다룰 수 있게 해주는 ORM 프레임워크다.

---

## Hibernate를 왜 쓰는지 감 잡기

Java 앱에서 DB를 쓰려면 원래 SQL을 직접 작성하고, ResultSet을 Java 객체로 변환하는 코드를 반복해야 한다. 테이블이 많아지면 이 보일러플레이트(반복 코드)가 엄청나게 쌓인다. Hibernate는 이 변환 작업을 자동화한다. `@Entity` 어노테이션으로 클래스를 선언하면, INSERT·SELECT·UPDATE·DELETE SQL을 Hibernate가 생성해 실행한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Java 클래스(@Entity) → Hibernate 매핑 → DB 테이블 자동 연동 → Session으로 CRUD 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| ORM | Java 객체와 DB 테이블을 자동으로 매핑하는 기술 |
| Session | DB와의 연결 단위. CRUD 작업은 Session을 통해 실행한다 |
| Transaction | 여러 DB 작업을 묶어 전부 성공하거나 전부 취소되게 보장하는 단위 |
| @Entity | 이 클래스가 DB 테이블과 매핑된다고 Hibernate에 알리는 어노테이션 |
| HQL | SQL과 비슷하지만 테이블 대신 Java 클래스 이름으로 쿼리하는 Hibernate 전용 쿼리 언어 |

## 예를 들어 설명하면

`Book` 클래스를 `books` 테이블에 매핑하고, 새 레코드를 저장하는 예시다.

```java
@Entity
@Table(name = "books")
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "title")
    private String title;

    @Column(name = "author")
    private String author;
    // getters / setters 생략
}

// 저장
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

Book book = new Book();
book.setTitle("Harry Potter and the Philosopher's Stone");
book.setAuthor("J.K. Rowling");

session.save(book);  // INSERT SQL 자동 생성
tx.commit();
session.close();
```

HQL로 특정 저자의 책을 조회할 때는 테이블명 대신 클래스명을 쓴다.

```java
String hql = "FROM Book WHERE author = :authorName";
List<Book> books = session.createQuery(hql)
    .setParameter("authorName", "J.K. Rowling")
    .list();
```

## 이 단계에서 중요한 판단 기준

Spring Data JPA를 이미 사용하는 프로젝트라면 Hibernate를 직접 다루는 일은 드물다. Hibernate는 JPA의 구현체로 내부에서 동작하므로, JPA 인터페이스를 통해 간접적으로 쓰는 경우가 대부분이다.

## 한 줄 요약 — 이것만 기억하면 된다

**Hibernate는 Java 클래스를 DB 테이블로 매핑해 SQL 없이 객체를 저장·조회할 수 있게 하며, Spring Data JPA 아래에서 실제 실행 엔진으로 동작한다.**

## 나중에 더 깊게 들어가면

- JPA(Jakarta Persistence API): Hibernate가 구현하는 Java ORM 표준 인터페이스
- LazyLoading vs EagerLoading: 연관 객체를 언제 DB에서 불러올지 설정하는 전략
- N+1 문제: 연관 관계 조회 시 쿼리가 폭발적으로 늘어나는 대표적 성능 함정과 해결법

---

**원본:** [Hibernate Introduced — https://memoryhub.tistory.com/147](https://memoryhub.tistory.com/147)
