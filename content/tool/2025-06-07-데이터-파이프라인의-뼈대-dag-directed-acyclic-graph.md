+++
title = "데이터 파이프라인의 뼈대 — DAG로 작업 의존성 다루기"
date = "2025-06-07"
description = "DAG는 방향성이 있고 순환하지 않는 그래프로, 데이터 파이프라인에서 작업의 선후 관계를 정의한다. 위상 정렬로 안전한 실행 순서를 자동으로 구하고, Airflow 같은 도구가 이를 실제 스케줄링에 활용한다."
tags = ["tool"]
categories = ["tool"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> DAG는 방향성이 있고 순환하지 않는 그래프로, 데이터 파이프라인에서 작업의 선후 관계를 정의한다. 위상 정렬로 안전한 실행 순서를 자동으로 구하고, Airflow 같은 도구가 이를 실제 스케줄링에 활용한다.

---

## DAG를 왜 쓰는지 감 잡기

여러 단계로 이루어진 데이터 처리에서 의존성이 꼬이면 시스템이 교착 상태에 빠진다. 예를 들어 고객 데이터를 수집(A)하고 정제(B)한 뒤 분석(C)해서 리포트(D)를 만드는 파이프라인에서 D가 A보다 먼저 실행되면 처리할 데이터가 없다. 더 나쁜 경우는 D가 A를 다시 트리거하는 순환 구조가 생겨 무한 루프에 빠지는 것이다.

DAG(Directed Acyclic Graph, 유향 비순환 그래프)는 이 두 문제를 구조적으로 차단한다. 방향성으로 선후 관계를 강제하고, 비순환 조건으로 루프 자체를 허용하지 않는다. Apache Airflow, Spark, dbt는 모두 내부에서 이 모델을 사용한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 작업을 노드로 표현 → 의존성을 방향 엣지로 연결 → 위상 정렬로 실행 순서 확정 → 스케줄러가 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 노드(Node/Vertex) | 그래프에서 하나의 작업 단위를 가리키는 점 |
| 엣지(Edge) | 노드 사이의 방향 화살표. "A 완료 후 B 시작"이라는 의존성을 표현 |
| 비순환(Acyclic) | 출발한 노드로 다시 돌아오는 경로가 없다는 보장. 이 조건이 교착 방지의 핵심 |
| 위상 정렬(Topological Sort) | DAG의 모든 노드를 의존성 순서에 맞게 일렬로 나열하는 알고리즘 |
| 진입 차수(In-degree) | 특정 노드를 가리키는 엣지 수. 0이면 선행 작업이 없어 바로 시작 가능 |

## 예를 들어 설명하면

아래는 Python 딕셔너리로 DAG를 표현하고 Kahn's Algorithm으로 위상 정렬하는 코드다.

```python
from collections import deque

dag = {
    'A': ['B', 'C'],  # A가 끝나야 B, C 시작 가능
    'B': ['D'],
    'C': ['D'],
    'D': []
}

def topological_sort(graph):
    in_degree = {node: 0 for node in graph}
    for node in graph:
        for neighbor in graph[node]:
            in_degree[neighbor] += 1

    queue = deque([n for n in graph if in_degree[n] == 0])
    result = []

    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    return result if len(result) == len(graph) else "사이클 존재"

print(topological_sort(dag))
# 출력: ['A', 'B', 'C', 'D'] 또는 ['A', 'C', 'B', 'D'] (둘 다 유효)
```

B와 C는 A 이후에 병렬 실행 가능하다. D는 B와 C 모두 완료된 후에 실행된다.

## 이 단계에서 중요한 판단 기준

파이프라인 설계 시 순환 여부를 먼저 검증해야 한다. `len(result) == len(graph)` 조건이 실패하면 사이클이 존재한다는 신호다. 이 검사를 배포 전 자동화 테스트에 포함하는 것이 안전하다.

## 한 줄 요약 — 이것만 기억하면 된다

**DAG는 방향성과 비순환 두 규칙으로 작업 의존성을 안전하게 모델링하고, 위상 정렬이 실행 순서를 자동으로 도출한다.**

## 나중에 더 깊게 들어가면

- Apache Airflow에서 Python 코드로 DAG를 선언하고 스케줄링하는 방법
- DFS 기반 위상 정렬과 Kahn's Algorithm의 구현 차이 및 사이클 탐지 방식
- dbt, Spark, Luigi에서 DAG 모델을 사용하는 방식 비교

---

**원본:** 데이터 파이프라인의 뼈대, DAG(Directed Acyclic Graph) — [https://memoryhub.tistory.com/667](https://memoryhub.tistory.com/667)
