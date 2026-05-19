# LightRAG vs RagAnything — 텍스트 검색에 그래프를 쓰면 무슨 일이 생기나

> **TL;DR**
> LightRAG는 텍스트 전용 RAG의 사실상 표준 선택이고, RagAnything은 PDF·이미지·표가 섞인 문서를 처리할 때만 추가로 고려한다.

---

## LightRAG를 왜 쓰는지 감 잡기

기존 RAG(Retrieval-Augmented Generation)는 텍스트를 벡터로 변환해서 유사도로 검색한다. 개별 문장은 잘 찾지만 "A와 B의 관계"처럼 여러 문서에 걸친 복잡한 질문에는 약하다.

LightRAG는 텍스트에서 엔티티(개념, 사람, 장소)와 관계를 추출해 지식 그래프로 만든다. 검색할 때 이 그래프를 탐색하므로 단순 유사도 검색보다 복잡한 추론이 가능하다.

RagAnything은 LightRAG 위에 구축된 확장판이다. PDF, 이미지, 표를 파싱해서 같은 그래프에 넣는 역할을 한다. 텍스트만 다루면 이 레이어는 불필요한 오버헤드다.

`핵심 흐름: 문서 → 엔티티/관계 추출 → 지식 그래프 → 듀얼 레벨 검색 → 응답`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| RAG | 검색해서 찾은 문서를 LLM에 넘겨 답변을 생성하는 방식. 모델을 재학습하지 않아도 최신 정보를 활용할 수 있다. |
| 지식 그래프 | 개념들 사이의 관계를 노드와 엣지로 표현한 구조. "서울 → 한국의 수도"처럼. |
| 듀얼 레벨 검색 | 특정 사실(local)과 주제 흐름(global)을 별도 경로로 검색해 합치는 방식. |
| 토큰 효율성 | 같은 질문에 사용하는 LLM 입력 토큰 수. LightRAG는 전통 그래프 RAG보다 6,100배 적다. |
| MinerU | RagAnything이 PDF와 이미지를 텍스트로 파싱할 때 쓰는 문서 처리 라이브러리. |

## 예를 들어 설명하면

법률 문서 수백만 토큰을 인덱싱하고 "A 계약서의 해지 조건이 B 판례와 충돌하는가?"를 묻는다고 하자.

벡터 검색은 두 문서를 따로 찾아 LLM에 넘기지만 관계를 파악하지 못한다. LightRAG는 두 문서에서 관련 엔티티와 관계를 추출해 그래프로 연결하고, 질문 시 그 경로를 탐색해 답변한다.

벤치마크 결과: 법률 데이터셋 80% 이상 검색 정확도, 응답 시간 평균 80~90ms, GraphRAG 대비 토큰 사용량 6,100분의 1.

## 이 단계에서 중요한 판단 기준

입력이 순수 텍스트라면 LightRAG만 쓴다. PDF·이미지·표가 포함된 복합 문서를 처리해야 할 때만 RagAnything을 추가한다.

## 한 줄 요약 — 이것만 기억하면 된다

**텍스트 RAG는 LightRAG로 시작하고, 멀티모달 문서가 생길 때 RagAnything으로 확장한다.**

## 나중에 더 깊게 들어가면

- LightRAG의 5가지 쿼리 모드(naive, local, global, hybrid, mix) 각각 언제 쓰는지
- PostgreSQL, Neo4j, Redis 백엔드 선택 기준과 분산 스케일링
- Self-RAG, Corrective RAG 같은 RAG 변형 기법과의 비교

---

**원본:** [LightRAG vs RagAnything: Technical Framework Comparison — memoryhub.tistory.com/707](https://memoryhub.tistory.com/707)
