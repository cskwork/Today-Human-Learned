+++
title = "로컬 환경에서 API 호스팅을 위한 Ollama 설정 종합 가이드"
date = "2025-03-02"
description = "Ollama는 인터넷 없이 내 PC에서 대형 언어 모델을 실행하고 REST API로 바로 호출할 수 있는 도구다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Ollama는 인터넷 없이 내 PC에서 대형 언어 모델을 실행하고 REST API로 바로 호출할 수 있는 도구다.

---

## Ollama를 왜 쓰는지 감 잡기

GPT 같은 AI 모델을 쓰려면 보통 외부 API에 요청을 보내야 한다. 그런데 인터넷이 없거나, 비용이 걱정되거나, 데이터를 외부로 보내기 어려운 환경이라면 어떻게 할까? Ollama는 이 문제를 해결한다. 모델을 로컬 PC에 내려받아 실행하고, `http://localhost:11434` 주소로 REST API를 제공한다. OpenAI API와 형식이 비슷하기 때문에, 기존 코드를 크게 바꾸지 않고도 전환할 수 있다.

핵심 흐름:

`모델 다운로드 (ollama pull) → 서버 실행 (ollama serve) → API 호출 (/api/generate 또는 /api/chat)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| LLM | 대형 언어 모델. 텍스트를 이해하고 생성하는 AI 모델 전체를 가리킨다. |
| GGUF | 모델 파일 형식. 크기를 줄여 일반 PC에서도 실행 가능하게 만든 포맷이다. |
| 양자화 (Quantization) | 모델 정밀도를 낮춰 메모리 사용량을 줄이는 기법. Q4, Q8 같은 숫자가 정밀도 수준을 뜻한다. |
| REST API | HTTP 요청으로 서비스를 호출하는 방식. `curl`이나 Python으로 모델에 질문을 보낼 수 있다. |
| 컨텍스트 길이 (num_ctx) | 모델이 한 번에 처리할 수 있는 텍스트 토큰 수. 길수록 메모리를 더 쓴다. |

## 예를 들어 설명하면

Python에서 Ollama로 질문 하나를 보내는 최소 코드다.

```python
import ollama

resp = ollama.generate(model="llama3:8b", prompt="기계학습이란?")
print(resp["response"])

# 스트리밍이 필요하면
for chunk in ollama.generate(model="llama3:8b", prompt="시를 써줘", stream=True):
    print(chunk["response"], end="", flush=True)
```

REST API를 직접 쓸 수도 있다.

```bash
curl http://localhost:11434/api/generate \
     -d '{"model":"llama3:8b","prompt":"기계학습이란?"}'
```

## 이 단계에서 중요한 판단 기준

RAM이 8 GB라면 7B 모델의 Q4 양자화 버전(`llama3:8b-q4_K_M`)을 선택해야 안정적으로 실행된다.

## 설치 및 기본 명령

macOS나 Linux는 설치 스크립트 한 줄로 끝난다.

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

설치 후 기본 명령어:

```bash
ollama pull llama3:8b     # 모델 다운로드
ollama run llama3:8b      # 대화형 실행
ollama ps                 # 현재 로드된 모델 확인
ollama stop llama3:8b     # 모델 중지
```

외부 네트워크에서 API에 접근하려면 환경변수로 바인딩 주소를 바꾼다.

```bash
OLLAMA_HOST=0.0.0.0 ollama serve
```

## 자주 겪는 오류 빠른 해결표

| 증상 | 해결책 |
|---|---|
| 404 또는 연결 실패 | `ollama serve`가 실행 중인지, 방화벽이 11434를 막지 않는지 확인 |
| GPU가 사용되지 않음 | NVIDIA 드라이버와 CUDA 버전 확인 |
| 메모리 부족 | 더 작은 모델이나 낮은 양자화 선택, `num_ctx` 값 줄이기 |
| 모델 저장 경로 변경 | `export OLLAMA_MODELS=/원하는/경로` 후 재시작 |

## 한 줄 요약 — 이것만 기억하면 된다

**Ollama는 `ollama pull → ollama serve → API 호출` 세 단계로 로컬 LLM을 REST API로 만들어주는 도구다.**

## 나중에 더 깊게 들어가면

- systemd 서비스로 등록해 서버 부팅 시 자동 실행하기
- TLS 리버스 프록시(nginx + certbot)로 외부 노출 시 보안 강화
- Modelfile로 커스텀 시스템 프롬프트와 파라미터가 포함된 모델 만들기

---

**원본:** [로컬 환경에서 API 호스팅을 위한 Ollama 설정 종합 가이드](https://memoryhub.tistory.com/460)
