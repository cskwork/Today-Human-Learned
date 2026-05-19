+++
title = "LangExtract + Gemini 2.5 — 비정형 텍스트에서 구조화된 정보 추출하기"
date = "2025-08-05"
description = "LangExtract는 개발자가 정의한 스키마에 따라 비정형 텍스트에서 정보를 추출하고, 추출 결과를 원문의 정확한 위치에 연결해 추적 가능하게 만드는 Python 라이브러리다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> LangExtract는 개발자가 정의한 스키마에 따라 비정형 텍스트에서 정보를 추출하고, 추출 결과를 원문의 정확한 위치에 연결해 추적 가능하게 만드는 Python 라이브러리다.

---

## LangExtract를 왜 쓰는지 감 잡기

LLM에게 "이 뉴스 기사에서 기업명과 재무 수치를 뽑아줘"라고 하면 그럴듯한 답이 나온다. 하지만 결과가 원문의 어느 부분에서 왔는지 알 수 없고, 매번 출력 형식이 달라지고, 긴 문서에서는 일부를 놓치기 쉽다.

LangExtract(Google 오픈소스)는 이 세 가지 문제를 해결한다. 추출 결과를 원문의 문자 오프셋에 연결해 출처를 추적하고, few-shot 예시로 출력 스키마를 강제하며, 긴 문서는 청킹과 멀티-패스 처리로 높은 재현율을 유지한다. Gemini 계열 모델 외에 Ollama를 통한 로컬 모델도 지원한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 추출 스키마 정의 + few-shot 예시 작성 → lx.extract 실행 → JSONL 저장 → HTML 시각화`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 정보 추출(Information Extraction) | 비정형 텍스트에서 특정 패턴의 정보를 찾아 구조화된 형태로 뽑아내는 작업 |
| Few-shot 예시 | 모델에게 "이런 식으로 뽑아라"고 보여주는 입출력 예시 — 한두 개만 있어도 스키마를 따르게 된다 |
| 문자 오프셋(Character Offset) | 원문에서 추출된 텍스트가 몇 번째 글자부터 몇 번째 글자까지인지를 나타내는 위치 정보 |
| 멀티-패스(Multi-pass) | 같은 문서를 여러 번 반복해서 추출해 놓친 정보를 줄이는 방식 |
| 청킹(Chunking) | 긴 문서를 작은 조각으로 나눠 처리하는 방식 — 컨텍스트 한계를 넘는 문서에서 필수다 |

## 예를 들어 설명하면

뉴스 기사에서 기업명, 재무 지표, 시장 심리를 추출하는 예시다.

```python
import langextract as lx

prompt = "Extract company name, financial metrics, and market sentiment. Use exact text spans."

examples = [
    lx.data.ExampleData(
        text="AlphaTech (AT) announced a quarterly profit of $2.5 billion, signaling a bullish trend.",
        extractions=[
            lx.data.Extraction(extraction_class="company", extraction_text="AlphaTech",
                               attributes={"stock_ticker": "AT"}),
            lx.data.Extraction(extraction_class="financial_metric",
                               extraction_text="quarterly profit of $2.5 billion",
                               attributes={"metric_type": "profit", "value": "$2.5 billion"}),
        ],
    )
]

result = lx.extract(
    text_or_documents="Global Dynamics Inc. (GDI) reported $15 billion revenue, but stock dipped 2%.",
    prompt_description=prompt,
    examples=examples,
    model_id="gemini-2.5-flash",  # 빠른 처리. 복잡한 추론에는 gemini-2.5-pro 사용
)

lx.io.save_annotated_documents([result], output_name="results.jsonl")
html = lx.visualize("results.jsonl")
```

긴 문서(예: 소설 전문)는 `extraction_passes=3, max_workers=20`을 추가하면 병렬 멀티-패스로 처리할 수 있다.

## 이 단계에서 중요한 판단 기준

모델 선택 기준은 단순하다 — 대량 처리에는 Flash, 정확도가 중요한 복잡한 문서에는 Pro를 쓴다.

## 한 줄 요약 — 이것만 기억하면 된다

**LangExtract는 few-shot 예시로 스키마를 정의하고, 추출 결과를 원문 위치에 연결해 감사(audit)가 가능한 정보 추출 파이프라인을 만든다.**

## 나중에 더 깊게 들어가면

- Ollama를 통한 로컬 모델 연동으로 API 없이 사용하는 방법
- 추출 결과를 관계형 DB에 저장하고 RAG 시스템과 연결하는 방법
- 프롬프트와 예시를 반복 개선해 엣지 케이스를 처리하는 방법

---

**원본:** [LangExtract and Gemini 2.5 – Tutorial and Demo — https://memoryhub.tistory.com/737](https://memoryhub.tistory.com/737)
