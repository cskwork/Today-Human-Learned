# Apache Airflow - 복잡한 데이터 작업을 조율하는 오케스트라 지휘자

> **TL;DR**
> Airflow는 데이터 파이프라인을 Python 코드로 정의하고, 순서와 실패 재시도까지 자동으로 관리해주는 워크플로우 오케스트레이터다.

---

## Apache Airflow를 왜 쓰는지 감 잡기

예전에는 데이터 수집, 변환, 적재(ETL) 작업을 쉘 스크립트로 작성하고 리눅스 cron에 등록했다. 작업이 늘어날수록 문제가 생겼다. A 작업이 끝난 뒤에만 B가 실행돼야 하는데 이 의존성을 cron으로 표현하기 어렵고, 실패하면 수동으로 재시작해야 했으며, 전체 흐름을 한눈에 볼 방법도 없었다.

Airflow는 이 문제를 해결하기 위해 Airbnb에서 시작됐다. 지금은 Apache 재단의 프로젝트로 전 세계 기업들이 데이터 파이프라인 관리에 쓰고 있다. 작업 흐름을 Python 코드로 명확하게 정의하기 때문에 Git으로 버전 관리도 가능하다.

`핵심 흐름: DAG 정의(Python) → Scheduler가 실행 시점 감지 → Executor가 Worker에 배정 → Worker가 실제 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| DAG | 작업들의 실행 순서와 의존 관계를 정의한 청사진. Python 파일로 작성한다. |
| Task | DAG 안에서 실제로 수행되는 개별 작업 단위. |
| Operator | Task가 무엇을 할지 결정하는 템플릿. BashOperator, PythonOperator 등이 있다. |
| Scheduler | DAG 파일을 주기적으로 스캔하여 실행 시점이 된 Task를 큐에 넣는 역할. |
| 멱등성 | 같은 Task를 여러 번 실행해도 결과가 동일한 성질. 재시도 안전성의 핵심이다. |

## 예를 들어 설명하면

아래는 Task 간 의존 관계를 설정하는 최소 예시다.

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

with DAG("example", start_date=datetime(2025, 1, 1), schedule="@daily") as dag:
    extract = PythonOperator(task_id="extract", python_callable=run_extract)
    transform = PythonOperator(task_id="transform", python_callable=run_transform)
    load = PythonOperator(task_id="load", python_callable=run_load)

    extract >> transform >> load  # 순서 정의
```

`extract`가 성공해야 `transform`이 시작된다. 실패하면 Airflow가 설정된 횟수만큼 자동으로 재시도한다.

## 이 단계에서 중요한 판단 기준

Task는 멱등성을 갖도록 설계해야 한다. 네트워크 오류로 같은 Task가 두 번 실행되는 상황에서 데이터가 중복 삽입되면 장애가 생긴다. 단순 INSERT 대신 UPSERT를 쓰거나, 특정 파티션을 삭제 후 재삽입하는 방식으로 구현하는 것이 안전하다.

## 한 줄 요약 - 이것만 기억하면 된다

**Airflow는 복잡한 데이터 파이프라인을 Python 코드로 정의하고, 스케줄링과 재시도와 모니터링을 한 곳에서 관리하게 해준다.**

## 나중에 더 깊게 들어가면

- Celery Executor와 Kubernetes Executor를 이용한 분산 실행
- Connections와 Hooks를 이용한 외부 시스템 보안 연동
- TaskGroups를 활용한 복잡한 DAG 시각화 정리

---

**원본:** [Apache Airflow - 복잡한 데이터 작업을 조율하는 오케스트라 지휘자](https://memoryhub.tistory.com/518)
