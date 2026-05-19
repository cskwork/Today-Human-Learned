# IntelliJ Community Edition에서 application.yml이 로드되지 않는 문제

> **TL;DR**
> IntelliJ Community Edition으로 Spring Boot를 실행하면 활성 프로파일이 지정되지 않아 application.yml을 못 읽는 경우가 있으며, Run Configuration에서 환경 변수 한 줄로 해결된다.

---

## 이 문제가 왜 생기는지 감 잡기

Spring Boot는 실행 시 `spring.profiles.active` 값을 보고 어느 설정 파일을 로드할지 결정한다. Ultimate Edition에는 Spring 플러그인이 내장되어 자동으로 처리해주지만, Community Edition에는 그 플러그인이 없다. 결과적으로 Gradle `bootRun`으로 실행할 때 프로파일이 지정되지 않아 `application-dev.yml` 같은 파일을 찾지 못하거나, 예상과 다른 설정으로 기동된다.

이 문제는 코드나 yml 파일 자체의 문제가 아니라, IntelliJ가 JVM에 프로파일 정보를 전달하지 않아서 생긴다.

`핵심 흐름: bootRun 실행 → JVM에 프로파일 값 없음 → Spring이 기본 설정만 로드 → application-dev.yml 무시`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Spring Profile | "dev", "prod" 같은 실행 환경 이름. 환경마다 다른 설정 파일을 적용할 때 쓴다. |
| application.yml | Spring Boot의 기본 설정 파일. 프로파일별로 application-dev.yml 등으로 분리할 수 있다. |
| Run Configuration | IntelliJ에서 "어떻게 실행할지"를 저장해두는 설정. 환경 변수, VM 옵션 등을 지정한다. |
| 환경 변수 (Environment Variable) | 프로그램 실행 전에 OS 수준에서 전달하는 키-값 정보. |
| VM 옵션 | JVM 시작 시 `-D`로 전달하는 시스템 속성. 환경 변수와 같은 효과를 낼 수 있다. |

## 예를 들어 설명하면

가장 빠른 해결 방법은 Run Configuration에 환경 변수를 추가하는 것이다.

1. 메인 클래스에서 오른쪽 클릭 → **Edit Configurations** 선택
2. **Environment variables** 필드에 입력:
   ```
   SPRING_PROFILES_ACTIVE=dev
   ```
3. Apply → OK → 애플리케이션 실행

같은 효과를 내는 두 가지 대체 방법도 있다.

```
# VM 옵션으로 지정 (Edit Configurations → VM options 필드)
-Dspring.profiles.active=dev

# 프로그램 인수로 지정 (Edit Configurations → Program arguments 필드)
--spring.profiles.active=dev
```

세 방법 모두 동작하지만 환경 변수 방식이 가장 명시적이고 OS 수준에서도 재사용 가능하다.

## 이 단계에서 중요한 판단 기준

새 Run Configuration을 만들 때마다 이 설정을 다시 추가해야 한다 — 기존 구성을 복사해서 쓰는 것이 실수를 줄이는 가장 간단한 방법이다.

## 한 줄 요약 — 이것만 기억하면 된다

**IntelliJ Community Edition에서 application.yml이 안 읽히면 Run Configuration의 Environment variables에 `SPRING_PROFILES_ACTIVE=dev` 한 줄을 추가하면 해결된다.**

## 나중에 더 깊게 들어가면

- `application.yml` vs `application-dev.yml` 우선순위 규칙과 프로파일 병합 동작
- Gradle `bootRun` 태스크에 `systemProperty`를 설정해 빌드 파일 수준에서 고정하는 방법
- 팀원 모두가 같은 설정을 쓰도록 `.run/` 디렉토리에 Run Configuration 파일을 커밋하는 방법

---

**원본:** [IntelliJ Community Edition에서 application.yml 인식 문제 해결 — https://memoryhub.tistory.com/525](https://memoryhub.tistory.com/525)
