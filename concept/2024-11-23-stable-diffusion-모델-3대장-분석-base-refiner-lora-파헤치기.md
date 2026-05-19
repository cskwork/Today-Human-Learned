# Stable Diffusion 모델 3종 정리 — Base, Refiner, LoRA의 역할 구분

> **TL;DR**
> Base Model이 이미지 전체 구조를 잡고, LoRA가 원하는 스타일을 주입하고, Refiner가 마지막 디테일을 다듬는다. 세 가지는 교체 가능한 부품처럼 조합해서 쓴다.

---

## 이 개념을 왜 알아야 하는지 감 잡기

Stable Diffusion을 처음 쓰면 "체크포인트", "LoRA", "Refiner" 같은 용어가 동시에 나와서 어디서부터 손을 대야 할지 모른다. 같은 프롬프트를 써도 모델을 바꾸면 결과가 완전히 달라지는 이유도 여기에 있다.

Stable Diffusion의 작동 원리는 단순하다. 순수한 노이즈 이미지에서 시작해 노이즈를 단계적으로 제거하며 원하는 이미지를 만들어낸다. 이 과정에서 어떤 노이즈를 어떻게 제거할지 학습한 데이터가 바로 모델이다. Base, Refiner, LoRA는 이 노이즈 제거 과정의 서로 다른 단계에 개입한다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: Base Model (전체 구성) → LoRA 적용 (스타일 주입) → Refiner (디테일 강화) → 최종 이미지`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Base Model (체크포인트) | 이미지 생성의 뼈대. 어떤 화풍과 품질로 그릴지를 결정하는 2~7GB 크기의 핵심 파일. |
| Refiner | Base Model이 만든 이미지의 마지막 20% 노이즈 제거 단계를 담당해 디테일을 강화하는 보조 모델. |
| LoRA (Low-Rank Adaptation) | Base Model을 바꾸지 않고 특정 캐릭터, 스타일, 의상을 추가하는 2~200MB 크기의 가벼운 플러그인. |
| Latent Space (잠재 공간) | 이미지를 픽셀이 아닌 압축된 특징 벡터로 다루는 공간. 메모리 효율을 높이기 위해 이 공간에서 노이즈 제거가 이루어진다. |
| Cross-Attention | 텍스트 프롬프트와 이미지 시각 요소를 연결하는 신경망 부분. LoRA는 주로 이 레이어를 미세 조정한다. |

## 예를 들어 설명하면

SDXL Base + Refiner 조합을 코드로 쓰는 방식이다.

```python
# Base 모델이 전체 40 스텝 중 80%를 처리
image = base(
    prompt="a portrait of a knight in detailed armor",
    num_inference_steps=40,
    denoising_end=0.8,      # 0~80% 구간: Base 담당
    output_type="latent",
).images

# Refiner가 나머지 20% 구간에서 디테일을 다듬음
image = refiner(
    prompt="a portrait of a knight in detailed armor",
    num_inference_steps=40,
    denoising_start=0.8,    # 80~100% 구간: Refiner 담당
    image=image,
).images[0]
```

LoRA는 Base 모델을 로드한 직후 U-Net에 가중치를 추가하는 방식으로 적용한다.

```python
pipe = StableDiffusionPipeline.from_pretrained("runwayml/stable-diffusion-v1-5")
pipe.unet.load_attn_procs("path/to/lora_weights")  # LoRA 주입
image = pipe("A character in fantasy style", num_inference_steps=25).images[0]
```

## 이 단계에서 중요한 판단 기준

| 컴포넌트 | 언제 쓰나 | 주의점 |
|---|---|---|
| Base Model | 항상 필요. 화풍과 품질의 기준을 결정 | 파일이 크다. 베이스를 바꾸면 결과가 크게 달라진다 |
| Refiner | SDXL 계열에서 품질을 높이고 싶을 때 | 효과가 미미할 수 있고 처리 시간이 늘어난다 |
| LoRA | 특정 캐릭터나 스타일이 필요할 때 | 가중치를 너무 높이면 이미지가 무너진다. 베이스 모델과 궁합을 확인해야 한다 |

## 한 줄 요약 — 이것만 기억하면 된다

**Base로 큰 그림을 잡고, LoRA로 원하는 스타일을 입히고, Refiner로 마무리 품질을 올린다.**

## 나중에 더 깊게 들어가면

- LoRA 가중치 직접 학습시키기 (dreambooth, kohya-ss)
- 여러 LoRA를 동시에 조합할 때 가중치 조절 방법
- ComfyUI에서 Base + Refiner + LoRA 워크플로 구성하기

---

**원본:** [Stable Diffusion 모델 3대장 분석 — https://memoryhub.tistory.com/408](https://memoryhub.tistory.com/408)
