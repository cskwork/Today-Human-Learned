# 코드 스멜: Change Preventers — 변경을 어렵게 만드는 세 가지 패턴

> **TL;DR**
> Change Preventers는 코드 한 곳을 바꾸면 여러 곳을 동시에 고쳐야 하거나, 한 클래스가 너무 많은 이유로 수정되는 설계 문제를 가리킨다.

---

## Change Preventers를 왜 알아야 하는지 감 잡기

"이 기능 하나만 바꾸면 되겠지" 했다가 연관된 파일을 열 개 고쳐야 했던 경험이 있다면 Change Preventers를 만난 것이다. 코드 스멜(Code Smell)이란 버그는 아니지만 유지보수를 어렵게 만드는 설계 징후를 뜻한다. 그 중 Change Preventers는 변경 비용을 직접적으로 높이는 세 가지 패턴을 묶어 부른다.

문제는 단순히 불편함에 그치지 않는다. 여러 곳을 동시에 수정하다가 한 곳을 빠뜨리면 버그가 생기고, 간단한 수정도 전체 구조를 이해해야 하므로 개발 속도가 크게 느려진다.

`핵심 흐름: 한 클래스가 너무 많은 책임 → 변경 이유가 여러 개 → 한 곳 수정이 연쇄 수정을 유발 → 버그 위험 증가`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 코드 스멜 | 버그는 아니지만 나중에 문제가 될 설계 냄새. 마틴 파울러가 정리한 개념. |
| Divergent Change | 하나의 클래스가 서로 다른 이유로 자주 수정되는 패턴. 책임이 너무 많다는 신호. |
| Shotgun Surgery | 기능 하나를 바꾸면 여러 클래스를 동시에 수정해야 하는 패턴. |
| Parallel Inheritance | 한 클래스의 서브클래스를 만들 때마다 다른 계층에도 서브클래스를 추가해야 하는 패턴. |
| SRP (단일 책임 원칙) | 하나의 클래스는 하나의 이유로만 변경되어야 한다는 설계 원칙. |

## 예를 들어 설명하면

**Divergent Change** — 한 클래스가 DB 저장, 보고서 생성, 사용자 정보 관리를 모두 담당한다.

```java
// 문제: User 클래스가 세 가지 이유로 바뀐다
public class User {
    public void changeName(String newName) { ... }   // 사용자 정보 변경 시
    public void saveToDatabase() { ... }             // DB 스키마 변경 시
    public String generateReport() { ... }           // 보고서 형식 변경 시
}

// 개선: 책임을 클래스별로 분리
public class User { public void changeName(String n) { ... } }
public class UserRepository { public void save(User u) { ... } }
public class UserReportGenerator { public String generate(User u) { ... } }
```

**Shotgun Surgery** — 이메일 전송 로직이 여러 클래스에 복사되어 있어, 로깅 형식을 바꾸면 모두 수정해야 한다. 해결책은 `EmailService` 클래스 하나로 통합하는 것이다.

**Parallel Inheritance** — `Shape` 계층을 확장할 때마다 `ShapeRenderer` 계층도 함께 확장해야 한다. 상속 대신 합성(Composition)을 쓰면 두 계층을 하나로 합칠 수 있다.

## 이 단계에서 중요한 판단 기준

코드 리뷰에서 하나의 PR이 세 개 이상의 무관한 파일을 건드린다면 Shotgun Surgery를 의심한다 — 관련 로직이 분산되어 있다는 신호다.

## 한 줄 요약 — 이것만 기억하면 된다

**변경 이유가 하나인 클래스, 변경 범위가 한 곳인 기능 — 이 두 기준을 지키면 Change Preventers의 대부분을 예방할 수 있다.**

## 나중에 더 깊게 들어가면

- Extract Class, Move Method 리팩토링 기법의 구체적인 적용 방법
- SOLID 원칙 중 SRP와 OCP가 Change Preventers와 어떻게 연결되는지
- 과도한 분리의 부작용 — 너무 작은 클래스가 만드는 "산탄총 이해(Shotgun Comprehension)" 문제

---

**원본:** [코드 스멜: Change Preventers 코드 변경을 방해하는 요소들 — https://memoryhub.tistory.com/509](https://memoryhub.tistory.com/509)
