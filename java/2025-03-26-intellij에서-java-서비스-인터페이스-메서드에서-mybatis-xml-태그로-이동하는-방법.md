# IntelliJ에서 Java 인터페이스 메서드에서 MyBatis XML 태그로 이동하기

> **TL;DR**
> MyBatis 플러그인(Free MyBatis Plugin 또는 MyBatisX)을 설치하면 인터페이스 메서드와 XML 쿼리 사이를 클릭 한 번에 오갈 수 있다.

---

## 이 기능을 왜 쓰는지 감 잡기

MyBatis 프로젝트에서는 Java 인터페이스 메서드와 XML 파일 안의 SQL이 이름으로 연결된다. 메서드 이름이 `getUserById`라면 XML에는 `id="getUserById"`인 태그가 있어야 한다. 코드를 읽다가 "이 메서드가 실제로 어떤 SQL을 쓰지?" 하고 XML을 찾아 헤매는 일이 자주 생긴다. 파일이 많아질수록 탐색 시간이 길어진다.

MyBatis 플러그인은 이 연결을 IDE가 인식하게 해서, 메서드에서 XML로, XML에서 메서드로 즉시 이동할 수 있게 한다.

`핵심 흐름: 플러그인 설치 → 프로젝트 인덱싱 → 인터페이스 메서드 클릭 → XML 태그 직접 이동`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Mapper 인터페이스 | MyBatis에서 SQL 메서드를 선언하는 Java 인터페이스 |
| Mapper XML | 인터페이스 메서드와 대응하는 실제 SQL이 담긴 XML 파일 |
| namespace | XML 파일이 어느 인터페이스와 연결되는지 지정하는 속성 |
| Free MyBatis Plugin | IntelliJ Marketplace의 무료 MyBatis 탐색 플러그인 |
| MyBatisX | JPA/MyBatis 모두 지원하는 고기능 탐색·생성 플러그인 |

## 예를 들어 설명하면

플러그인 설치 후 아래 인터페이스 메서드에서 `Ctrl+Alt+B`를 누르면 대응하는 XML 태그로 바로 이동한다.

```java
@Mapper
public interface UserMapper {
    User getUserById(Long id);  // 여기서 Ctrl+Alt+B → XML의 id="getUserById"로 이동
}
```

XML 쪽에서도 같은 단축키를 쓰면 인터페이스 메서드로 돌아온다. 플러그인을 설치하면 거터(gutter) 아이콘도 생겨서 클릭만으로도 이동할 수 있다.

**설치 경로**: `File > Settings > Plugins > Marketplace > "MyBatisX" 또는 "Free MyBatis Plugin" 검색 후 설치`

연동이 안 될 때 첫 번째로 확인할 것: XML의 `namespace`가 인터페이스 전체 경로(`com.example.mapper.UserMapper`)와 정확히 일치하는지 확인한다.

## 이 단계에서 중요한 판단 기준

플러그인을 설치했는데도 이동이 안 된다면, `namespace` 불일치가 원인인 경우가 가장 많다 — XML과 인터페이스의 패키지 경로가 완전히 같아야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**MyBatisX 플러그인 하나로 인터페이스 ↔ XML 양방향 탐색이 가능해지고, namespace 일치가 그 전제 조건이다.**

## 나중에 더 깊게 들어가면

- MyBatisX의 자동 코드 생성 기능 (테이블에서 Mapper, XML, Entity 일괄 생성)
- `File > Invalidate Caches / Restart`로 인덱스 문제 해결하는 시점
- `@Select`, `@Insert` 등 어노테이션 기반 SQL과 XML 방식의 트레이드오프

---

**원본:** [IntelliJ에서 Java 서비스 인터페이스 메서드에서 MyBatis XML 태그로 이동하는 방법](https://memoryhub.tistory.com/531)
