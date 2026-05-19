# gpt-oss 실행 가이드 — 로컬에서 돌리는 추론형 LLM

> **TL;DR**
> gpt-oss는 Apache 2.0 라이선스로 로컬 실행이 가능한 추론 모델이다. reasoning_effort로 속도와 정확도를 조절하고, 구조화 출력과 함수 호출을 기본 지원한다.

---

## gpt-oss를 왜 쓰는지 감 잡기

"회사 데이터는 외부 API로 못 보내는데, GPT급 추론 능력은 필요하다"는 상황이 실제로 많다. gpt-oss는 이 틈을 정확히 겨냥한다. 모델 가중치가 공개되어 있어 자체 서버에 올려 쓰면 데이터가 OpenAI 서버로 전혀 나가지 않는다.

GPT-2 이후 OpenAI는 언어 모델 가중치를 공개하지 않았다. 2025년 8월 5일 gpt-oss-120b와 gpt-oss-20b를 공개하면서 기조가 바뀌었다. 두 모델은 단순한 텍스트 생성이 아니라 에이전트 워크플로(웹 검색, 코드 실행, 함수 호출)를 염두에 두고 설계되었다.

`핵심 흐름: 모델 다운로드 → 서빙 서버 실행 → OpenAI 호환 API로 호출 → reasoning.effort 조절`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| MoE | 모든 파라미터를 쓰지 않고 각 토큰마다 일부 "전문가"만 활성화해 메모리를 줄이는 구조 |
| Chain-of-Thought | 답을 바로 내놓지 않고 중간 추론 단계를 거쳐 정확도를 높이는 기법 |
| reasoning.effort | low / medium / high로 추론 깊이를 조절하는 파라미터 |
| 구조화 출력 | JSON 스키마를 지정해 모델이 정해진 형식으로만 응답하도록 강제하는 기능 |
| MXFP4 | 모델 가중치를 4비트로 압축해 메모리 사용량을 줄이는 양자화 방식 |

## 예를 들어 설명하면

로컬 Transformers 서버를 띄우고 reasoning.effort를 조절하는 흐름이다.

```bash
# 1) 서버 실행
transformers serve
```

```json
// 2) 간단한 작업: effort=low (빠름)
{
  "model": "openai/gpt-oss-20b",
  "reasoning": { "effort": "low" },
  "input": [{"role": "user", "content": "한 문장으로 요약해줘"}]
}

// 3) 복잡한 수학 문제: effort=high (정확)
{
  "model": "openai/gpt-oss-20b",
  "reasoning": { "effort": "high" },
  "input": [{"role": "user", "content": "이 알고리즘의 시간 복잡도를 증명해줘"}]
}
```

구조화 출력이 필요한 경우 `response_format`에 JSON 스키마를 지정하면 후처리 없이 바로 파싱 가능한 응답을 받을 수 있다.

## 이 단계에서 중요한 판단 기준

20b는 MXFP4 기준 16GB VRAM, 120b는 80GB 이상(또는 멀티 GPU)이 필요하므로, 하드웨어 조건을 먼저 확인하고 모델을 고르라.

## 한 줄 요약 — 이것만 기억하면 된다

**gpt-oss는 데이터를 외부에 보내지 않고 로컬에서 GPT급 추론을 실행하려는 팀에게 실질적인 선택지다.**

## 나중에 더 깊게 들어가면

- vLLM과 Ollama를 이용한 gpt-oss 서빙 비교 (처리량 vs 운영 편의)
- 지식 컷오프(2024년 6월)를 넘어서기 위한 웹 검색 도구 연동 방법
- Preparedness Framework와 오픈 모델의 안전 거버넌스 실제 적용

---

**원본:** [OpenAI 오픈 모델(gpt-oss) — memoryhub.tistory.com/743](https://memoryhub.tistory.com/743)
