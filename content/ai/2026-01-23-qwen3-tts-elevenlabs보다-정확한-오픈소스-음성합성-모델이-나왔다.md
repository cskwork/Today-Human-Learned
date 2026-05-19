+++
title = "Qwen3-TTS: 오픈소스인데 ElevenLabs보다 정확한 음성합성 모델"
date = "2026-01-23"
description = "알리바바 Qwen 팀이 공개한 Qwen3-TTS는 상용 TTS 서비스 수준의 품질을 Apache 2.0 라이선스로 무료 제공하며, 3초 음성 클로닝과 97ms 초저지연을 갖췄다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 알리바바 Qwen 팀이 공개한 Qwen3-TTS는 상용 TTS 서비스 수준의 품질을 Apache 2.0 라이선스로 무료 제공하며, 3초 음성 클로닝과 97ms 초저지연을 갖췄다.

---

## Qwen3-TTS를 왜 쓰는지 감 잡기

TTS(텍스트를 음성으로 변환) 시장은 오랫동안 두 갈래로 나뉘어 있었다. ElevenLabs, MiniMax 같은 고품질 유료 서비스와, 품질이 아쉬운 오픈소스 모델. 개발자들은 "품질을 원하면 돈을 내고, 무료를 원하면 품질을 포기하라"는 선택지만 있었다.

Qwen3-TTS는 이 공식을 깼다. 알리바바 클라우드 Qwen 팀이 2026년 1월 공개한 이 모델은 500만 시간 이상의 음성 데이터로 학습되었고, 10개 언어와 9개 중국어 방언을 지원한다. 벤치마크에서 단어 오류율(WER)이 ElevenLabs와 MiniMax보다 낮으면서도 완전 무료다.

유튜브 나레이션, 게임 NPC 음성, 고객센터 IVR, 오디오북 제작 등 TTS가 필요한 모든 곳에 적용된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 텍스트 입력 → Dual-Track LM 처리 → 12.5토큰/초 압축 → 음성 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| WER (Word Error Rate) | 음성합성 정확도 지표. 낮을수록 좋다. 1%이면 100단어 중 1개 오류 |
| 음성 클로닝 (Voice Clone) | 짧은 샘플 음성을 들려주면 그 목소리를 복제하는 기능 |
| Dual-Track LM | 텍스트 처리와 음성 출력을 동시에 스트리밍하는 아키텍처 |
| 음성 디자인 (Voice Design) | "20대 여성, 밝고 경쾌한 톤"처럼 자연어로 존재하지 않는 음성을 만드는 기능 |
| 초저지연 (97ms) | 첫 음성 패킷이 나오기까지 걸리는 시간. 일반 TTS의 1/3 수준 |

## 예를 들어 설명하면

한국어 화자 Sohee 음색으로 음성을 생성하는 기본 예제:

```python
from qwen_tts import Qwen3TTSModel
import soundfile as sf

model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice",
    device_map="cuda:0",
)

wavs, sr = model.generate_custom_voice(
    text="오늘 발표 자료를 음성으로 변환합니다.",
    language="Korean",
    speaker="Sohee",
    instruct="차분하고 명확한 어조로",
)
sf.write("output.wav", wavs[0], sr)
```

모델 선택 기준:

| 용도 | 권장 모델 |
|---|---|
| 프로덕션 TTS, 다양한 캐릭터 | CustomVoice 1.7B |
| 새로운 가상 음성 생성 | VoiceDesign 1.7B |
| 특정 화자 복제, 파인튜닝 | Base 1.7B |
| 경량 배포 (GPU 메모리 제한) | CustomVoice 0.6B |

## 이 단계에서 중요한 판단 기준

GPU 메모리가 충분하면 1.7B 모델을, 리소스 제한 환경이면 0.6B 모델을 선택한다. 실시간 대화처럼 지연이 중요한 경우에만 스트리밍 모드(`stream=True`)를 켠다.

## 한 줄 요약 — 이것만 기억하면 된다

**Qwen3-TTS는 상용 서비스 수준의 TTS를 무료로 로컬에서 실행할 수 있는 첫 번째 실용적 오픈소스 선택지다.**

## 나중에 더 깊게 들어가면

- 특정 성우 스타일로 파인튜닝하는 방법
- 97ms 스트리밍 모드를 실시간 대화 AI에 통합하는 아키텍처
- 한국어 외 다국어 음성 클로닝의 품질 한계와 개선 방법

---

**원본:** Qwen3-TTS, ElevenLabs보다 정확한 오픈소스 음성합성 모델이 나왔다 — [https://memoryhub.tistory.com/989](https://memoryhub.tistory.com/989)
