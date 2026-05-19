# Maven에 커스텀 원격 저장소 추가하기

> **TL;DR**
> `pom.xml`의 `<repositories>` 섹션이나 `settings.xml`의 프로파일에 저장소 URL을 추가하면 Maven Central 외의 저장소에서도 의존성을 받아올 수 있다.

---

## 커스텀 저장소가 왜 필요한지 감 잡기

Maven Central에 없는 라이브러리가 있다. 사내 전용 라이브러리, 아직 릴리즈 전인 스냅샷 버전, 서드파티 벤더가 자체 저장소로 배포하는 SDK가 그 예다. 이럴 때 Maven이 Maven Central 외에도 지정한 URL을 함께 뒤지도록 설정해야 한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 저장소 URL 등록 → mvn 빌드 → 로컬 캐시 없으면 커스텀 저장소 탐색 → JAR 다운로드`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| repositories | pom.xml 안에서 저장소 목록을 정의하는 XML 섹션 |
| settings.xml | 사용자 전체 Maven 설정 파일. `~/.m2/settings.xml`에 위치한다 |
| profile | settings.xml에서 여러 환경 설정을 묶어놓는 단위. 활성화 여부를 제어할 수 있다 |
| snapshots / releases | 저장소가 스냅샷 버전과 릴리즈 버전을 각각 제공하는지 설정하는 옵션 |
| activeProfiles | settings.xml에서 기본으로 활성화할 프로파일을 지정하는 섹션 |

## 예를 들어 설명하면

방법은 두 가지다. 프로젝트에만 적용하려면 `pom.xml`에 추가한다.

```xml
<repositories>
    <repository>
        <id>custom-repo</id>
        <url>https://my.custom.repo/repository</url>
        <snapshots><enabled>true</enabled></snapshots>
        <releases><enabled>true</enabled></releases>
    </repository>
</repositories>
```

모든 Maven 프로젝트에 전역 적용하려면 `~/.m2/settings.xml`의 프로파일에 넣는다.

```xml
<profiles>
    <profile>
        <id>custom-repo-profile</id>
        <repositories>
            <repository>
                <id>custom-repo</id>
                <url>https://my.custom.repo/repository</url>
            </repository>
        </repositories>
    </profile>
</profiles>
<activeProfiles>
    <activeProfile>custom-repo-profile</activeProfile>
</activeProfiles>
```

`settings.xml` 방식은 해당 머신에서 실행되는 모든 프로젝트에 적용된다. CI 서버에서 넥서스(Nexus)나 아티팩토리(Artifactory) 같은 사내 저장소를 쓸 때 주로 이 방식을 쓴다.

## 이 단계에서 중요한 판단 기준

프로젝트 저장소(`pom.xml`)는 팀 전체에 공유되지만, 인증 정보(아이디·비밀번호)는 pom.xml에 절대 넣지 않는다. 인증이 필요한 저장소는 `settings.xml`의 `<servers>` 섹션에 자격증명을 분리해 관리한다.

## 한 줄 요약 — 이것만 기억하면 된다

**pom.xml은 프로젝트 한정, settings.xml은 머신 전체 적용이며, 인증 정보는 반드시 settings.xml에만 넣는다.**

## 나중에 더 깊게 들어가면

- `settings.xml`의 `<servers>` 섹션으로 저장소 인증 정보 관리
- 넥서스(Nexus) / 아티팩토리(Artifactory) 같은 사내 저장소 프록시 구성
- Maven 미러(mirror) 설정으로 특정 저장소 요청을 다른 URL로 리다이렉트

---

**원본:** [Can you add custom remote repositories to Maven? How? — https://memoryhub.tistory.com/152](https://memoryhub.tistory.com/152)
