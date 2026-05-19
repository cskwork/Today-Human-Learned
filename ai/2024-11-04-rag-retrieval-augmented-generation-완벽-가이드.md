# RAG — 검색으로 보강된 생성

> **TL;DR**
> RAG는 LLM이 답변할 때 외부 문서를 실시간으로 검색해서 참고하게 만드는 구조다.

---

## 이 주제를 왜 쓰는지 감 잡기

LLM은 학습 시점 이후의 정보를 모른다. 사내 문서, 최신 뉴스, 내부 정책 같은 건 모델 가중치 안에 없다. 이걸 해결하는 가장 현실적인 방법이 RAG다. 모델 자체를 다시 학습시키는 대신, 질문이 들어올 때마다 관련 문서를 찾아서 모델에게 "참고해서 답해줘"라고 넘겨주는 방식이다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 문서 → 벡터 변환 및 저장 → 질문 입력 → 유사 문서 검색 → LLM 생성`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 임베딩(Embedding) | 텍스트를 숫자 배열(벡터)로 변환하는 것. 의미가 비슷한 문장은 벡터 공간에서 가까운 위치에 놓인다 |
| 벡터 데이터베이스 | 임베딩 벡터를 저장하고 "가장 가까운 벡터"를 빠르게 찾아주는 특수한 데이터베이스 |
| 청크(Chunk) | 긴 문서를 검색 가능한 크기로 잘라낸 조각. 너무 크면 노이즈, 너무 작으면 문맥 손실 |
| 검색기(Retriever) | 질문 벡터와 문서 벡터 사이의 유사도를 계산해서 상위 N개 청크를 꺼내는 컴포넌트 |
| 환각(Hallucination) | 모델이 사실이 아닌 내용을 자신 있게 답하는 현상. RAG는 출처 기반 답변으로 이걸 줄인다 |

## 예를 들어 설명하면

```python
# 1. 문서를 벡터로 변환해 저장
from sentence_transformers import SentenceTransformer
import chromadb

model = SentenceTransformer('all-MiniLM-L6-v2')
docs = ["반품 정책: 구매 후 30일 이내 가능", "배송 기간: 영업일 기준 3일"]
embeddings = model.encode(docs)

client = chromadb.Client()
col = client.create_collection("support")
col.add(embeddings=embeddings.tolist(), documents=docs, ids=["d1", "d2"])

# 2. 질문이 들어오면 관련 청크 검색
query_vec = model.encode(["반품하려면 어떻게 해요?"])
results = col.query(query_embeddings=query_vec.tolist(), n_results=1)
context = results["documents"][0][0]

# 3. LLM에 컨텍스트와 질문을 함께 전달
prompt = f"다음 정보를 바탕으로 답하세요:\n{context}\n\n질문: 반품하려면 어떻게 해요?"
```

## 이 단계에서 중요한 판단 기준

청크 크기는 512~1024 토큰이 기본값이지만, 검색 품질이 떨어지면 BM25(키워드 기반)와 벡터 검색을 함께 쓰는 하이브리드 방식을 먼저 시도하라.

## 한 줄 요약 — 이것만 기억하면 된다

**LLM이 "모른다"고 말해야 할 때, 외부 문서를 검색해서 알게 만드는 구조가 RAG다.**

## 나중에 더 깊게 들어가면

- 재순위화(Reranking): 검색 결과를 한 번 더 정렬해 정확도 높이기
- 하이브리드 검색: BM25와 벡터 검색 결합
- 평가 지표: Precision@K, MRR로 검색 품질 측정하기

---

**원본:** [RAG(Retrieval-Augmented Generation) 완벽 가이드](https://memoryhub.tistory.com/355)
