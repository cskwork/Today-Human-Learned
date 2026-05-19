# Hibernate 네이밍 전략 충돌 해결

> **TL;DR**
> `PrefixPhysicalNamingStrategy`와 `PrefixQueryModifier`를 함께 쓰면 테이블 이름이 이중으로 변조되어 쿼리가 깨진다. 네이밍 전략은 하나만 선택하고, 쿼리 가로채기는 표준 JPA 기능으로 대체하라.

---

## Hibernate 네이밍 전략을 왜 쓰는지 감 잡기

Hibernate는 Java 클래스 이름을 데이터베이스 테이블 이름으로 자동 매핑한다. 프로젝트마다 테이블 이름 앞에 접두사를 붙이거나(`APP_USER`, `APP_ORDER`처럼), 레거시 DB의 명명 규칙을 따라야 할 때 네이밍 전략을 설정한다.

문제는 이 역할을 담당하는 도구가 두 가지 있을 때 발생한다. 하나는 스키마 생성 시점에 이름을 바꾸고, 다른 하나는 쿼리 실행 직전에 이름을 바꾼다. 둘이 동시에 작동하면 접두사가 두 번 붙거나 테이블 이름이 예상과 달라진다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 클래스 이름 → PhysicalNamingStrategy 적용 → DB 테이블 이름 확정 → 쿼리 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| PhysicalNamingStrategy | Hibernate가 DB에 실제로 쓸 테이블·컬럼 이름을 결정하는 규칙 |
| ImplicitNamingStrategy | 개발자가 이름을 지정하지 않았을 때 Hibernate가 자동으로 이름을 만드는 규칙 |
| PrefixPhysicalNamingStrategy | 테이블 이름 앞에 접두사를 붙이는 PhysicalNamingStrategy 구현체 |
| Interceptor (`EmptyInterceptor`) | Hibernate가 쿼리를 DB에 보내기 직전에 SQL 문자열을 가로채서 수정할 수 있는 훅 |
| 멀티테넌트 | 하나의 서버에서 여러 고객(테넌트)의 데이터를 분리해서 관리하는 구조 |

## 예를 들어 설명하면

다음 두 설정이 동시에 존재하면 충돌이 발생한다.

```java
// PhysicalNamingStrategy: 스키마 생성 시 "user" → "APP_user"로 변환
public class PrefixPhysicalNamingStrategy extends PhysicalNamingStrategyStandardImpl {
    @Override
    public Identifier toPhysicalTableName(Identifier name, JdbcEnvironment context) {
        return new Identifier("APP_" + name.getText(), name.isQuoted());
    }
}

// Interceptor: 쿼리 실행 시 "FROM user" → "FROM APP_user"로 다시 변환
// 결과: "FROM APP_APP_user" 형태가 되어 테이블을 찾지 못함
public class PrefixQueryModifier extends EmptyInterceptor {
    @Override
    public String onPrepareStatement(String sql) {
        return sql.replaceAll("FROM (\\w+)", "FROM APP_$1");
    }
}
```

해결책은 `PhysicalNamingStrategy`만 남기고 Interceptor를 제거하는 것이다. Spring Boot 설정 예시:

```yaml
spring:
  jpa:
    hibernate:
      naming:
        physical-strategy: com.example.PrefixPhysicalNamingStrategy
        implicit-strategy: org.springframework.boot.orm.jpa.hibernate.SpringImplicitNamingStrategy
```

## 이 단계에서 중요한 판단 기준

네이밍 변환은 스키마 생성 단계(PhysicalNamingStrategy) 한 곳에서만 처리하라. 런타임 SQL 가로채기는 Hibernate 내부 최적화와 충돌할 수 있고, 디버깅이 어렵다.

## 한 줄 요약 — 이것만 기억하면 된다

**테이블 이름 변환은 `PhysicalNamingStrategy` 하나로 끝내고, `Interceptor`로 SQL을 직접 수정하는 방식은 피하라.**

## 나중에 더 깊게 들어가면

- Hibernate 멀티테넌트 기능 — 테넌트별 스키마 분리를 공식 지원하는 방법
- JPA Criteria API — 타입 안전하게 동적 쿼리를 만드는 방법
- QueryDSL — 복잡한 조건부 쿼리를 코드로 표현하는 방법

---

**원본:** [Hibernate 설정 이슈 해결](https://memoryhub.tistory.com/313)
