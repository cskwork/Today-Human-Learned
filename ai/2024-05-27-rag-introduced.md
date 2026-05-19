# RAG — LLM에 외부 지식을 연결하는 방법

> **TL;DR**
> RAG는 LLM이 모르는 정보를 외부 문서에서 검색해 프롬프트에 붙여주는 기법이다. 모델을 재학습하지 않고도 최신 정보나 사내 문서를 활용할 수 있다.

---

## RAG를 왜 쓰는지 감 잡기

LLM은 학습 데이터의 컷오프 이후 정보를 모른다. 사내 문서, 제품 매뉴얼, 실시간 뉴스도 모른다.
이를 해결하려면 모델을 다시 학습시켜야 하는데 비용이 막대하다.

RAG(Retrieval-Augmented Generation)는 다른 길을 택한다.
사용자가 질문하면 먼저 외부 지식 베이스에서 관련 문서를 검색하고,
그 내용을 프롬프트에 붙여 모델에 전달한다. 모델은 검색된 맥락을 기반으로 답변을 생성한다.
결국 모델 자체는 그대로이고, 질문할 때마다 필요한 정보를 동적으로 공급하는 구조다.

`핵심 흐름: 사용자 질문 -> 벡터 검색 -> 관련 문서 추출 -> 프롬프트에 추가 -> LLM 생성`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 벡터 임베딩 | 텍스트를 숫자 배열로 변환한 것. 의미가 비슷한 텍스트는 숫자 공간에서도 가깝다 |
| 지식 베이스 | RAG가 검색하는 문서 저장소. 사내 문서, FAQ, 논문 등 어떤 텍스트든 넣을 수 있다 |
| 유사도 검색 | 쿼리 벡터와 가장 가까운 벡터들을 찾는 연산. 코사인 유사도가 가장 많이 쓰인다 |
| Augmentation | 검색된 문서를 프롬프트에 붙여 모델의 입력 맥락을 보강하는 과정 |
| 청킹 | 긴 문서를 검색하기 좋은 크기의 단위로 나누는 전처리 작업 |

## 예를 들어 설명하면

고객 지원 챗봇에 RAG를 적용하는 흐름이다.

```python
from sentence_transformers import SentenceTransformer
from langchain.vectorstores import Chroma

embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
knowledge_base = Chroma.from_documents(support_docs, embedding_model.encode)

def rag_chatbot(query):
    # 1. Retrieval: 유사 문서 검색
    relevant_docs = knowledge_base.similarity_search(query, k=2)
    retrieved_info = "\n".join([doc.page_content for doc in relevant_docs])

    # 2. Augmentation: 프롬프트에 문서 추가
    augmented_prompt = (
        f"고객 질문: {query}\n\n"
        f"참고 정보:\n{retrieved_info}\n\n"
        f"답변:"
    )

    # 3. Generation: LLM이 답변 생성
    return llm.generate(augmented_prompt)
```

"비밀번호 초기화 방법을 알려줘"라는 질문이 들어오면, 모델은 매뉴얼에서 관련 절차를 검색해 정확한 답변을 만든다.
사전 학습된 지식만으로는 사내 절차를 알 수 없지만, RAG 덕분에 실시간으로 문서를 참조할 수 있다.

## 이 단계에서 중요한 판단 기준

RAG의 품질은 검색 품질에 달려 있다. 관련 없는 문서가 검색되면 모델이 잘못된 맥락으로 답변을 만든다. 청킹 전략과 임베딩 모델 선택이 핵심이다.

## 한 줄 요약 — 이것만 기억하면 된다

**RAG는 LLM을 재학습하지 않고 외부 문서를 실시간으로 검색해 프롬프트에 붙여줌으로써 최신성과 정확성을 높이는 기법이다.**

## 나중에 더 깊게 들어가면

- 청킹 전략: 고정 크기 vs 문장 단위 vs 시맨틱 청킹의 장단점
- 재순위(Reranking): 검색 결과를 한 번 더 정렬해 정밀도를 높이는 방법
- 파인튜닝 vs RAG: 언제 모델을 직접 학습시키고 언제 RAG를 쓰는 것이 나은지

---

**원본:** [RAG Introduced — https://memoryhub.tistory.com/124](https://memoryhub.tistory.com/124)
