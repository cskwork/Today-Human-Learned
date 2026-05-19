# TEI vs Ollama vs OpenAI 임베딩 비교 — 로컬 무료가 두 개라면?

> **TL;DR**
> TEI와 Ollama 모두 로컬에서 무료로 쓸 수 있다. 차이는 성능(TEI)이냐 편의성(Ollama)이냐이고, OpenAI는 즉시 쓸 수 있는 유료 클라우드 옵션이다.

---

## 임베딩을 왜 쓰는지 감 잡기

RAG(검색 증강 생성) 시스템을 만들 때 핵심 단계가 있다. 문서를 LLM에 바로 던지는 게 아니라, 먼저 숫자 벡터(임베딩)로 변환해서 저장해두고, 질문이 들어오면 가장 관련 있는 문서 조각을 찾아 LLM에 넘기는 방식이다.

임베딩 모델은 텍스트를 벡터로 바꾸는 역할을 한다. 이 모델을 어떤 서비스로 실행할지 선택하는 게 이 글의 주제다.

`핵심 흐름: 문서 → 임베딩 모델 → 벡터 저장소 → 질문 입력 → 유사 문서 검색 → LLM 답변`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 임베딩 | 텍스트를 수백 개의 숫자 배열(벡터)로 표현한 것. "의미"를 숫자로 압축한다. |
| TEI | Hugging Face의 Text Embeddings Inference — Docker로 돌리는 고성능 임베딩 서버 |
| Ollama | LLM과 임베딩 모델을 로컬에서 간편하게 실행하는 도구 |
| Dynamic Batching | 요청이 몰려도 자동으로 묶어 처리해 처리량을 높이는 기법 |
| MoE / GGUF | 각각 TEI와 Ollama가 쓰는 모델 최적화 방식 — 내부 구조 차이 |

## 예를 들어 설명하면

세 가지 선택지의 비용과 성능을 나란히 놓으면 이렇다.

| 솔루션 | 로컬 실행 | 처리량 | 응답 시간 | 설정 난이도 |
|---|---|---|---|---|
| OpenAI | 불가 (클라우드 전용) | ~100 req/s | 200-500ms | 낮음 (API 키만) |
| TEI | 가능 (무료) | 450+ req/s | 100ms 미만 | 높음 (Docker, GPU 필요) |
| Ollama | 가능 (무료) | 20-50 req/s | 100-300ms | 중간 (한 줄 설치) |

Ollama로 빠르게 시작하는 코드는 이렇다.

```bash
# 모델 내려받기
ollama pull mxbai-embed-large

# 임베딩 요청
curl http://localhost:11434/api/embed -d '{
  "model": "mxbai-embed-large",
  "input": "딥러닝과 머신러닝의 차이점"
}'
```

TEI는 GPU 서버에서 대량 처리가 필요할 때 쓴다.

```bash
docker run --gpus all -p 8080:80 \
  ghcr.io/huggingface/text-embeddings-inference:1.7 \
  --model-id BAAI/bge-large-en-v1.5
```

## 이 단계에서 중요한 판단 기준

프로토타입이나 개인 프로젝트라면 Ollama로 시작하고, 프로덕션에서 초당 수백 건을 처리해야 한다면 TEI로 전환하라.

## 한 줄 요약 — 이것만 기억하면 된다

**로컬 임베딩은 TEI(고성능)와 Ollama(간편함) 모두 무료이므로, 규모에 맞게 고르면 된다.**

## 나중에 더 깊게 들어가면

- 한국어 임베딩 벤치마크: BAAI/bge-m3 vs multilingual-e5-large 비교
- Hugging Face Inference Endpoints를 통한 TEI 클라우드 배포
- 임베딩 모델 선택이 RAG 검색 품질에 미치는 영향 측정법

---

**원본:** [TEI vs Ollama vs OpenAI 임베딩 비교 — memoryhub.tistory.com/741](https://memoryhub.tistory.com/741)
