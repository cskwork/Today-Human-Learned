# AWS DocumentDB — MongoDB 호환 완전 관리형 데이터베이스

> **TL;DR**
> DocumentDB는 MongoDB 드라이버와 쿼리를 그대로 쓰면서 AWS가 서버 관리·복제·백업을 대신 해주는 완전 관리형 문서 데이터베이스다.

---

## DocumentDB를 왜 쓰는지 감 잡기

웹·모바일 앱이 커지면서 고정된 행/열 구조(RDBMS)로 담기 어려운 데이터가 늘었다. 사용자 프로필, 상품 카탈로그, 로그처럼 필드가 제각각인 데이터는 JSON 문서 형태로 저장하는 편이 훨씬 자연스럽다. MongoDB가 그 역할을 잘 해왔지만, MongoDB를 직접 운영하면 서버 패치·백업·고가용성 설정을 모두 스스로 해야 한다.

AWS DocumentDB는 이 운영 부담을 없앤다. 애플리케이션은 기존 MongoDB 드라이버로 그대로 연결하고, 실제 데이터 저장·복제·장애 복구는 AWS 인프라가 처리한다.

초보자는 처음에 이렇게 이해하면 된다.

`애플리케이션 (MongoDB 드라이버) → 클러스터 엔드포인트 → Primary/Replica 인스턴스 → 분산 스토리지 (3개 AZ, 6중 복제)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 문서(Document) | 행/열 대신 JSON처럼 중첩 가능한 키-값 묶음 하나. 테이블의 "행"에 해당하지만 구조가 유연하다. |
| 클러스터(Cluster) | Primary 인스턴스 1개와 Replica 인스턴스(최대 15개)를 묶은 DocumentDB 배포 단위. |
| Primary 인스턴스 | 읽기·쓰기를 모두 처리하는 주 노드. 장애 시 Replica 중 하나가 자동으로 승격된다. |
| Replica 인스턴스 | 읽기 전용 노드. 읽기 요청을 분산해 성능을 높이고, Primary 장애 시 대기한다. |
| 클러스터 볼륨 | 3개 가용 영역(AZ)에 걸쳐 6개 복사본을 자동 관리하는 분산 스토리지. 용량은 최대 128TiB까지 자동 증가한다. |

## 예를 들어 설명하면

전자상거래 서비스에서 상품마다 속성이 다를 때 DocumentDB가 유용하다. 의류에는 `size`, 전자기기에는 `voltage` 필드가 있어도 같은 컬렉션에 섞어 저장할 수 있다.

연결 예시 (Python, pymongo 드라이버):

```python
from pymongo import MongoClient

# 클러스터 엔드포인트에 연결 — 특정 인스턴스 IP가 아닌 클러스터 DNS 사용
client = MongoClient(
    "mongodb://user:password@cluster.cluster-xxxx.ap-northeast-2.docdb.amazonaws.com:27017/",
    tls=True,
    tlsCAFile="global-bundle.pem",
    readPreference="secondaryPreferred",  # 읽기는 Replica로 분산
)
db = client["shop"]
db["products"].insert_one({"name": "T-shirt", "size": "M", "price": 25000})
```

기존 MongoDB 코드에서 연결 문자열과 TLS 인증서 경로만 바꾸면 동작한다.

## 이 단계에서 중요한 판단 기준

MongoDB를 AWS에서 운영 중이거나 운영 부담을 없애고 싶다면 DocumentDB를 택하되, 마이그레이션 전에 반드시 공식 호환성 문서를 확인해 사용 중인 연산자가 지원되는지 점검한다.

DocumentDB는 MongoDB API와 100% 호환이 아니다. `$where`, 일부 `$lookup` 기능 등은 동작하지 않거나 다르게 작동한다. I/O 비용도 인스턴스 메모리 크기와 워크로드에 따라 예상보다 커질 수 있으므로 CloudWatch로 지속 모니터링해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**DocumentDB는 MongoDB 드라이버를 그대로 쓰면서 AWS에 운영을 맡길 수 있는 관리형 문서 데이터베이스다. 단, 100% 호환이 아니므로 마이그레이션 전 호환성 검증은 필수다.**

## 나중에 더 깊게 들어가면

- Elastic Cluster 모드: 샤딩(수평 분산) 구성으로 초당 수백만 건 쓰기 처리하는 방법
- 성능 최적화: 인덱스 설계, 워킹셋이 메모리에 올라가도록 인스턴스 크기 선택하는 기준
- 비용 구조 심화: I/O 최적화 스토리지 유형 선택과 CloudWatch + Performance Insights 활용법

---

**원본:** [AWS DocumentDB - MongoDB 호환 완전 관리형 데이터베이스 ?](https://memoryhub.tistory.com/563)
