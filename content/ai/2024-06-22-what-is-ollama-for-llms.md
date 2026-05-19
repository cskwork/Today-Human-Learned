+++
title = "Ollama란 무엇인가 — LLM을 내 컴퓨터에서 직접 실행하기"
date = "2024-06-22"
description = "Ollama는 클라우드 없이 로컬 머신에서 대형 언어 모델을 다운받아 실행할 수 있게 해 주는 오픈소스 도구다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Ollama는 클라우드 없이 로컬 머신에서 대형 언어 모델을 다운받아 실행할 수 있게 해 주는 오픈소스 도구다.

---

## Ollama를 왜 쓰는지 감 잡기

ChatGPT 같은 AI 서비스는 요청이 인터넷을 통해 외부 서버로 나간다. 코드, 사내 문서, 개인 정보가 포함된 텍스트를 다룰 때 이는 보안 문제가 될 수 있다. Ollama는 이 문제를 해결한다. 모델 자체를 로컬에 내려받아 실행하므로 데이터가 외부로 나가지 않는다. 인터넷 연결 없이도 동작하고, 사용량에 따른 API 비용도 없다.

초보자는 처음에 이렇게 이해하면 된다.

`모델 다운로드(pull) → 로컬 서버 실행(run) → HTTP API로 앱에서 호출`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| LLM (대형 언어 모델) | 대규모 텍스트로 학습해 인간처럼 글을 이해하고 생성하는 AI 모델 |
| `ollama pull` | 허브에서 특정 모델을 로컬 디스크로 내려받는 명령 |
| `ollama run` | 내려받은 모델을 터미널에서 바로 대화 모드로 실행하는 명령 |
| Modelfile | Docker의 Dockerfile처럼, 모델 동작 방식을 커스터마이즈하는 설정 파일 |
| 로컬 API 엔드포인트 | Ollama가 `localhost:11434`에 제공하는 HTTP 서버. 앱에서 모델을 호출할 때 사용 |

## 예를 들어 설명하면

설치부터 앱 연동까지 세 단계로 진행된다.

```bash
# 1. 모델 내려받기 (llama3 기준, 수 GB)
ollama pull llama3

# 2. 터미널에서 바로 대화 테스트
ollama run llama3
```

앱에서 호출할 때는 로컬에 뜬 HTTP 서버를 쓴다.

```python
import requests

def query_ollama(prompt):
    response = requests.post(
        'http://localhost:11434/api/generate',
        json={"model": "llama3", "prompt": prompt}
    )
    return response.json()['response']

print(query_ollama("재귀 함수를 한 문장으로 설명해 줘."))
```

Ollama가 `localhost:11434`에 서버를 열고 있으므로, 앱은 외부 API를 호출하는 것과 동일한 방식으로 로컬 모델을 사용한다.

## 이 단계에서 중요한 판단 기준

로컬 실행이므로 GPU 메모리가 핵심 제약이다. llama3 7B 모델은 최소 8GB RAM이 필요하고, 더 큰 모델은 그에 비례해 요구량이 늘어난다. 하드웨어 사양을 먼저 확인한 뒤 모델 크기를 선택해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Ollama는 LLM을 로컬에서 실행하는 런타임으로, 프라이버시가 중요하거나 클라우드 API 비용을 줄이고 싶을 때 사용한다.**

## 나중에 더 깊게 들어가면

- Modelfile로 시스템 프롬프트나 파라미터를 고정한 커스텀 모델 만들기
- Ollama와 Aider, LangChain 등 다른 도구를 연동하는 방법
- GPU 가속 설정 및 모델 양자화(quantization)로 속도와 메모리 균형 잡기

---

**원본:** [What is Ollama for LLMs — https://memoryhub.tistory.com/306](https://memoryhub.tistory.com/306)
