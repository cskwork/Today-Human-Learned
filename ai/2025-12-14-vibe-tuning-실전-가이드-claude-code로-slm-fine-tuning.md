# Vibe-tuning: 자연어로 소형 언어 모델을 내 것으로 만들기

> **TL;DR**
> 원하는 출력 스타일을 글로 써두면, Claude Code가 데이터 생성부터 학습, 평가까지 자동으로 수행해 SLM 파인튜닝 시간을 몇 주에서 몇 시간으로 단축한다.

---

## Vibe-tuning을 왜 쓰는지 감 잡기

GPT-4 같은 대형 모델은 비싸고, 오픈소스 소형 모델은 서비스 톤에 맞지 않는다. 파인튜닝이 답인 건 알지만, 학습 데이터 수천 건 확보, 하이퍼파라미터 설정, GPU 환경 구성은 ML 엔지니어링 경험 없이 시작하기 어렵다.

Vibe-tuning은 이 진입 장벽을 낮춘다. "이런 말투로 답해줘"처럼 원하는 결과를 자연어로 기술하면, Claude Code가 합성 데이터를 생성하고, Hugging Face 인프라에 학습을 제출하며, 완료 후 평가까지 수행한다. 로컬에 고사양 GPU가 없어도 된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Vibe Spec 작성 → 합성 데이터 생성 → LoRA 학습 → 자동 평가 → 배포`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Vibe Spec | 원하는 말투, 출력 포맷, 금지 규칙을 마크다운으로 쓴 명세 문서 |
| SLM | 0.5B~3B 파라미터급 소형 언어 모델. 가볍고 저렴하게 운영 가능 |
| SFT | 레이블된 데이터로 모델을 추가 학습시키는 방식 (Supervised Fine-Tuning) |
| LoRA | 전체 가중치 대신 일부만 학습해 비용과 시간을 줄이는 기법 |
| Chat Template | 학습과 추론 시 입출력 형식을 통일하는 포맷. 불일치 시 성능이 급락한다 |

## 예를 들어 설명하면

"JSON만 반환하는 한국어 기술 어시스턴트"를 만들고 싶다면 `VIBE_SPEC.md`를 이렇게 작성한다.

```
목표: 한국어 기술 어시스턴트. 반드시 JSON으로만 응답.

[필수 사항]
- 출력은 순수 JSON 단일 객체: answer, assumptions, risks, next_steps 키 포함
- answer는 3~7문장

[금지 사항]
- "무조건", "100%" 같은 과장 표현
```

그다음 Claude Code에 "이 Spec을 기준으로 학습 데이터 800개(train.jsonl)와 검증 데이터 200개(eval.jsonl)를 생성해줘"라고 요청한다. 생성 후 "Qwen3-0.6B를 이 데이터로 파인튜닝해줘"라고 하면 Hugging Face Jobs에 작업이 제출된다. 학습 완료 후 JSON 파싱 성공률, 필수 키 존재 여부, 금칙어 검출 등을 자동으로 검증한다.

## 이 단계에서 중요한 판단 기준

Spec이 정량적일수록 학습 데이터 품질이 높아진다. "짧게"보다 "3~7문장"처럼 숫자로 명시하라.

## 한 줄 요약 — 이것만 기억하면 된다

**Vibe Spec에 원하는 출력 규칙을 정량적으로 적고, Claude Code에 맡기면 SLM 파인튜닝 전 과정이 자동화된다.**

## 나중에 더 깊게 들어가면

- Chat Template 불일치 문제와 TRL `completion_only=True` 설정
- SFT 이후 DPO/GRPO로 선호 스타일을 정교화하는 2단계 정렬
- Catastrophic Forgetting 방지를 위한 일반 데이터 혼합(Replay Buffer) 전략

---

**원본:** [Vibe-tuning 실전 가이드: Claude Code로 SLM Fine-Tuning](https://memoryhub.tistory.com/927)
