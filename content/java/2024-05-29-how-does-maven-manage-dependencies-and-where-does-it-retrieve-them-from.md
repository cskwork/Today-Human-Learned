+++
title = "Maven의 의존성 관리 — pom.xml부터 로컬 캐시까지"
date = "2024-05-29"
description = "Maven은 `pom.xml`에 선언된 의존성을 로컬 저장소에서 먼저 찾고, 없으면 원격 저장소에서 내려받아 캐시한다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Maven은 `pom.xml`에 선언된 의존성을 로컬 저장소에서 먼저 찾고, 없으면 원격 저장소에서 내려받아 캐시한다.

---

## Maven 의존성 관리를 왜 쓰는지 감 잡기

Java 프로젝트는 외부 라이브러리 없이는 거의 아무것도 할 수 없다. 과거에는 JAR 파일을 직접 내려받아 프로젝트 폴더에 넣었다. 버전이 바뀌면 파일을 수동으로 교체해야 했고, 팀원 간 버전이 달라지면 빌드가 깨졌다. Maven은 "어떤 라이브러리의 어떤 버전이 필요한지"를 파일 한 곳에 선언하면 나머지를 자동으로 처리한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: pom.xml 선언 → 로컬 저장소 확인 → 없으면 원격 저장소 다운로드 → 로컬에 캐시 → 빌드 클래스패스 구성`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| pom.xml | 프로젝트 설정 파일. 필요한 라이브러리 목록과 버전을 여기에 선언한다 |
| 로컬 저장소 | 내 컴퓨터의 `~/.m2/repository`. 한 번 내려받은 JAR이 여기 쌓인다 |
| 원격 저장소 | Maven Central처럼 인터넷에 있는 JAR 보관소 |
| 전이적 의존성 (Transitive Dependency) | 내가 쓰는 라이브러리가 또 다른 라이브러리에 의존할 때 Maven이 그것도 자동으로 포함시키는 것 |
| scope | 의존성이 어느 단계에서 필요한지 지정. `compile`, `test`, `provided` 등이 있다 |

## 예를 들어 설명하면

Spring Core와 JUnit을 쓴다고 선언하면 아래처럼 작성한다.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

`mvn install`을 실행하면 Maven은 `~/.m2/repository`를 먼저 확인한다. 없으면 Maven Central(`https://repo.maven.apache.org/maven2`)에서 내려받고 로컬에 저장한다. `scope=test`인 JUnit은 테스트 컴파일·실행 단계에서만 클래스패스에 포함되고 최종 패키지에는 들어가지 않는다.

## 이 단계에서 중요한 판단 기준

전이적 의존성 충돌이 생기면 `mvn dependency:tree`로 어떤 경로로 라이브러리가 포함됐는지 먼저 확인한다. 버전을 강제로 고정하려면 `<dependencyManagement>` 섹션을 쓴다.

## 한 줄 요약 — 이것만 기억하면 된다

**Maven은 pom.xml 선언 하나로 라이브러리 다운로드, 버전 관리, 전이적 의존성 해소를 모두 자동 처리한다.**

## 나중에 더 깊게 들어가면

- `<dependencyManagement>`로 멀티 모듈 프로젝트 버전 통합 관리
- BOM(Bill of Materials) import로 관련 라이브러리 버전 일괄 선언
- 커스텀 원격 저장소 추가 방법 (`pom.xml` vs `settings.xml`)

---

**원본:** [How does Maven manage dependencies, and where does it retrieve them from? — https://memoryhub.tistory.com/151](https://memoryhub.tistory.com/151)
