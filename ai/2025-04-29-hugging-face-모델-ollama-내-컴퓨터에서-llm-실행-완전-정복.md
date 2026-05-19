# Hugging Face 모델과 Ollama — 내 컴퓨터에서 LLM 실행하기

> **TL;DR**
> Hugging Face에서 GGUF 형식 모델을 받아 Ollama에 등록하면, 클라우드 없이 내 PC에서 LLM을 실행할 수 있다.

---

## 왜 로컬에서 LLM을 실행하는가

클라우드 LLM API는 호출마다 비용이 발생하고, 데이터가 외부 서버를 거친다. 민감한 데이터를 다루거나 반복적으로 모델을 호출하는 환경에서는 로컬 실행이 현실적인 대안이다.

Ollama는 로컬 LLM 실행을 단순화한 도구다. Hugging Face Hub에는 수천 개의 오픈소스 모델이 GGUF 형식으로 공개되어 있다. 이 둘을 연결하면 원하는 모델을 골라 내 컴퓨터에서 직접 돌릴 수 있다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: Hugging Face(모델 저장소) → GGUF 파일 다운로드 → Modelfile 작성 → Ollama 등록 → 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| GGUF | LLM을 압축해서 일반 PC에서 돌릴 수 있게 만든 파일 형식 |
| 양자화(Quantization) | 모델 정밀도를 낮춰 파일 크기와 메모리 사용량을 줄이는 기법. Q4, Q5 등으로 표기 |
| Modelfile | Ollama에게 "이 GGUF 파일을 이렇게 설정해서 써라"고 알려주는 텍스트 설정 파일 |
| num_ctx | 모델이 한 번에 기억할 수 있는 토큰(단어 조각) 수. 클수록 긴 대화를 기억하지만 RAM 소모도 커짐 |
| ollama create | Modelfile을 읽어 Ollama에 사용자 정의 모델을 등록하는 명령어 |

## 예를 들어 설명하면

Hugging Face에서 `Meta-Llama-3-8B-Instruct.Q4_K_M.gguf`를 다운로드했다고 가정한다. 같은 폴더에 `Modelfile`을 아래처럼 작성한다.

```
FROM ./Meta-Llama-3-8B-Instruct.Q4_K_M.gguf

PARAMETER temperature 0.7
PARAMETER num_ctx 4096

SYSTEM "You are a helpful AI assistant."
```

그 다음 터미널에서 두 명령으로 등록하고 실행한다.

```bash
ollama create my-llama3 -f Modelfile
ollama run my-llama3
```

매번 이 과정을 반복하기 번거롭다면, 아래 스크립트로 다운로드부터 등록까지 한 번에 처리할 수 있다.

```bash
./ollama_hf_import.sh QuantFactory/Meta-Llama-3-8B-Instruct-GGUF \
  Meta-Llama-3-8B-Instruct.Q4_K_M.gguf my-llama3-8b
```

## 이 단계에서 중요한 판단 기준

`num_ctx`를 높이기 전에 내 RAM 용량을 확인하라 — 값이 커질수록 메모리 부족으로 모델 로딩이 실패할 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**Hugging Face에서 GGUF를 받아 Modelfile로 num_ctx를 설정하고 Ollama에 등록하면, 로컬에서 나만의 LLM을 실행할 수 있다.**

## 나중에 더 깊게 들어가면

- GGUF 양자화 레벨(Q4 vs Q8)별 품질과 성능 트레이드오프
- Ollama의 GPU 가속 설정 (CUDA, Metal)
- Modelfile의 TEMPLATE 지시어로 모델별 채팅 형식 맞추기

---

**원본:** [Hugging Face 모델 & Ollama - 내 컴퓨터에서 LLM 실행 완전 정복](https://memoryhub.tistory.com/565)
