# 벡터 데이터베이스 — 유사한 것을 빠르게 찾는 데이터베이스

> **TL;DR**
> 벡터 데이터베이스는 데이터를 숫자 배열(벡터)로 변환해 저장하고, 의미적으로 유사한 데이터를 빠르게 찾아주는 검색 전용 데이터베이스다.

---

## 벡터 데이터베이스를 왜 쓰는지 감 잡기

기존 데이터베이스는 "이름이 '고양이'인 행을 찾아줘"처럼 정확히 일치하는 값을 검색할 때 잘 작동한다. 그런데 "이 이미지와 비슷한 이미지를 찾아줘"나 "이 문장과 뜻이 가장 가까운 문장은?" 같은 질문에는 답을 못 한다. 의미적 유사성은 일치 여부가 아니라 거리로 표현해야 하기 때문이다.

벡터 데이터베이스는 이 문제를 해결하기 위해 만들어졌다. 텍스트, 이미지, 음성 같은 비정형 데이터를 숫자 배열로 변환한 뒤, 그 숫자들 사이의 거리를 계산해 가장 가까운 항목을 돌려준다. ChatGPT 스타일 검색, 추천 시스템, 의미 검색, 이미지 유사도 검색이 모두 이 방식으로 작동한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 원본 데이터 → 임베딩 모델 → 벡터 → 벡터 DB 저장 → 유사도 검색 → 결과 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 벡터(Vector) | 데이터를 표현하는 숫자 배열. 예: `[0.2, 0.5, -0.1, ...]` |
| 임베딩(Embedding) | 텍스트·이미지를 벡터로 바꾸는 과정. 의미가 비슷하면 벡터도 비슷하게 나온다. |
| 벡터 인덱스(Vector Index) | 수백만 개의 벡터 중에서 유사한 것을 빠르게 찾기 위한 색인 자료구조. |
| 코사인 유사도(Cosine Similarity) | 두 벡터가 얼마나 같은 방향을 가리키는지 측정하는 유사도 지표. 1에 가까울수록 비슷하다. |
| ANN(근사 최근접 이웃 탐색) | 완벽히 가장 가까운 벡터를 찾는 대신, 충분히 가까운 벡터를 훨씬 빠르게 찾는 방법. |

## 예를 들어 설명하면

"kitten"이라는 단어를 검색하면 "cat"과 유사한 결과를 반환하는 예시다. 의미가 비슷하기 때문에 벡터 거리도 가깝다.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

texts = ["cat", "dog", "fish"]
vectorizer = TfidfVectorizer()
vectors = vectorizer.fit_transform(texts).toarray()

query = "kitten"
query_vector = vectorizer.transform([query]).toarray()
similarities = cosine_similarity(query_vector, vectors)

most_similar_index = np.argmax(similarities)
print(f"'{query}'와 가장 유사한 단어: '{texts[most_similar_index]}'")
```

실제 프로덕션에서는 Pinecone, Weaviate, pgvector 같은 전용 DB를 쓰고, TF-IDF 대신 OpenAI나 Sentence-Transformers 같은 임베딩 모델을 사용한다.

## 이 단계에서 중요한 판단 기준

정확한 값 일치 검색이 필요하면 관계형 DB를 쓰고, 의미적 유사성 검색이 필요하면 벡터 DB를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**데이터를 벡터로 바꾸고 거리를 재면, "비슷한 것"을 기계가 찾을 수 있다.**

## 나중에 더 깊게 들어가면

- HNSW, IVF 같은 벡터 인덱스 알고리즘의 작동 원리
- OpenAI Embeddings API를 활용한 실제 시맨틱 검색 구현
- 관계형 DB + 벡터 검색을 함께 쓰는 하이브리드 검색 패턴

---

**원본:** [Vector Database Introduced — https://memoryhub.tistory.com/210](https://memoryhub.tistory.com/210)
