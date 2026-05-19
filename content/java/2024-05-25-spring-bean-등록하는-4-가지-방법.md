+++
title = "Spring Bean 등록하는 4가지 방법"
date = "2024-05-25"
description = "Spring에서 Bean을 등록하는 방법은 XML 직접 선언, XML ComponentScan, Java Config 직접 선언, Java Config ComponentScan 네 가지이며, 현대 Spring Boot 프로젝트는 네 번째 방식을 기본으로 사용한다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Spring에서 Bean을 등록하는 방법은 XML 직접 선언, XML ComponentScan, Java Config 직접 선언, Java Config ComponentScan 네 가지이며, 현대 Spring Boot 프로젝트는 네 번째 방식을 기본으로 사용한다.

---

## Spring Bean 등록을 왜 알아야 하는지 감 잡기

Spring은 객체를 직접 `new`로 생성하지 않고 컨테이너(ApplicationContext)가 대신 만들어서 관리한다. 이렇게 컨테이너가 관리하는 객체를 Bean이라고 한다. 컨테이너가 어떤 객체를 Bean으로 만들지 알려주는 방법이 "Bean 등록"이다.

등록 방식은 Spring의 역사와 함께 발전해왔다. XML 기반에서 어노테이션 기반으로, 그리고 자동 스캔 방식으로 진화했다. 방식이 다를 뿐 결과는 같다. 컨테이너가 해당 객체를 생성하고 의존성을 주입한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Bean 정의 방법 선택 → ApplicationContext 로드 → 컨테이너가 객체 생성 및 주입`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Bean | Spring 컨테이너가 생성하고 관리하는 객체. 직접 `new`를 쓰지 않아도 된다. |
| ApplicationContext | Bean들을 담아두는 Spring의 컨테이너. Bean을 꺼내 쓰거나 의존성을 주입하는 주체다. |
| ComponentScan | 지정한 패키지를 탐색해 `@Component`, `@Service`, `@Repository` 등 어노테이션이 붙은 클래스를 자동으로 Bean으로 등록하는 기능. |
| @Configuration | 이 클래스가 Bean 설정 파일임을 Spring에 알리는 어노테이션. |
| @Autowired | 필요한 Bean을 컨테이너에서 찾아 자동으로 주입하라는 어노테이션. |

## 예를 들어 설명하면

네 가지 방식을 가장 단순한 형태로 비교하면 다음과 같다.

**방법 1 — XML에 Bean 직접 선언** (가장 오래된 방식)
```xml
<!-- application.xml -->
<bean id="bookRepository" class="com.example.BookRepository"/>
<bean id="bookService" class="com.example.BookService">
    <property name="bookRepository" ref="bookRepository"/>
</bean>
```

**방법 2 — XML + ComponentScan** (어노테이션과 XML 혼합)
```xml
<context:component-scan base-package="com.example"/>
```
클래스에는 `@Repository`, `@Service`를 붙인다.

**방법 3 — Java Config에 Bean 직접 선언**
```java
@Configuration
public class AppConfig {
    @Bean
    public BookRepository bookRepository() { return new BookRepository(); }
    @Bean
    public BookService bookService() {
        BookService s = new BookService();
        s.setBookRepository(bookRepository());
        return s;
    }
}
```

**방법 4 — Java Config + ComponentScan** (Spring Boot 기본 방식)
```java
@Configuration
@ComponentScan(basePackageClasses = DemoApplication.class)
public class AppConfig {}
// 또는 @SpringBootApplication 하나로 동일 효과
```

## 이 단계에서 중요한 판단 기준

Spring Boot를 쓰는 신규 프로젝트라면 `@SpringBootApplication`이 이미 `@ComponentScan`을 포함하므로 방법 4가 기본이다. XML 방식(방법 1, 2)은 레거시 유지보수 시에만 접한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Bean 등록은 "컨테이너에게 어떤 객체를 만들어 달라고 알려주는 방법"이며, 현재는 어노테이션 + ComponentScan 자동 탐색이 표준이다.**

## 나중에 더 깊게 들어가면

- Bean 스코프(Singleton vs Prototype): 같은 Bean을 요청할 때마다 새 인스턴스를 줄지 하나만 공유할지 설정하는 방법
- 의존성 주입 방식 비교: 생성자 주입 vs 필드 주입 vs 세터 주입의 장단점
- @Conditional: 특정 조건에서만 Bean을 등록하는 조건부 Bean 등록

---

**원본:** [Spring Bean 등록하는 4 가지 방법 — https://memoryhub.tistory.com/52](https://memoryhub.tistory.com/52)
