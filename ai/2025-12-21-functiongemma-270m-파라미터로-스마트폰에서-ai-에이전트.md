# FunctionGemma, 270M 파라미터로 스마트폰에서 AI 에이전트

> **TL;DR**
> FunctionGemma는 자연어를 API 호출로 변환하도록 특화된 초경량 모델로, 클라우드 없이 스마트폰에서 동작하며 파인튜닝 후 85% 정확도를 달성한다.

---

## FunctionGemma를 왜 쓰는지 감 잡기

AI 에이전트가 "불 꺼줘"라는 말을 들었을 때, 사람에게 응답하는 것과 조명 API를 호출하는 것은 전혀 다른 능력이다. 대부분의 LLM은 텍스트 생성에 최적화되어 있고, 함수 호출을 일관되게 수행하려면 큰 모델이나 클라우드 의존이 필요했다. FunctionGemma는 이 문제를 다르게 접근한다. 처음부터 "자연어 입력 → 구조화된 함수 호출 출력"에만 집중해 훈련된 270M 파라미터 모델이다. 메모리 0.5GB(Q8 양자화 시 300MB)로 스마트폰에서 오프라인 동작이 가능하다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 자연어 명령 → FunctionGemma → JSON 함수 호출 → 앱/기기 API 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Function Calling | AI가 텍스트 대신 실행 가능한 함수 이름과 파라미터를 JSON으로 출력하는 것. 앱이 이 JSON을 읽어 실제 동작을 수행한다. |
| 파인튜닝 (Fine-tuning) | 사전 훈련된 모델을 특정 작업 데이터로 추가 학습시키는 것. FunctionGemma는 파인튜닝 없이 58%, 파인튜닝 후 85% 정확도를 기록한다. |
| 에지 디바이스 | 클라우드가 아닌 스마트폰, IoT 기기처럼 로컬에서 처리하는 하드웨어. 네트워크 없이 동작하므로 지연 없고 개인정보가 외부로 나가지 않는다. |
| 양자화 (Quantization) | 모델의 가중치 정밀도를 낮춰 크기를 줄이는 기법. FP16 0.5GB가 Q8_0 양자화로 300MB가 된다. |
| Mobile Actions | 구글이 제공하는 스마트폰 작업 자동화 평가 데이터셋. 알람 설정, 연락처 추가, 앱 열기 등 실제 모바일 UI 조작 시나리오로 구성된다. |

## 예를 들어 설명하면

스마트홈 앱에 FunctionGemma를 탑재한다고 가정하자.

```python
from transformers import AutoProcessor, AutoModelForCausalLM

processor = AutoProcessor.from_pretrained("google/functiongemma-270m-it", device_map="auto")
model = AutoModelForCausalLM.from_pretrained("google/functiongemma-270m-it", dtype="auto", device_map="auto")
```

사용자가 "거실 불 꺼줘"라고 말하면 모델은 다음을 출력한다.

```json
{"function": "toggle_light", "params": {"room": "거실", "state": "off"}}
```

앱이 이 JSON을 파싱해 스마트 전구 API를 호출한다. 인터넷 연결 없이, 서버 비용 없이.

## 이 단계에서 중요한 판단 기준

FunctionGemma는 "실행 가능한 액션 목록이 미리 정해진 앱"에 최적이다. 일반 대화나 오픈 엔드 질의응답에는 범용 모델이 낫다.

## 한 줄 요약 — 이것만 기억하면 된다

**FunctionGemma는 클라우드 없이 스마트폰에서 자연어를 API 호출로 바꾸는, 처음부터 에이전트용으로 만들어진 초경량 모델이다.**

## 나중에 더 깊게 들어가면

- Hugging Face TRL의 SFTTrainer로 Mobile Actions 데이터셋 파인튜닝 실습
- LiteRT-LM을 통한 Android 기기 직접 배포 방법
- FunctionGemma와 대형 모델(Gemma 3 27B)을 조합한 하이브리드 라우팅 설계

---

**원본:** FunctionGemma, 270M 파라미터로 스마트폰에서 AI 에이전트 — https://memoryhub.tistory.com/942
