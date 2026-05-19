+++
title = "pi-autoresearch — AI가 밤새 내 코드를 스스로 튜닝한다고요?"
date = "2026-04-17"
description = "pi-autoresearch는 \"시도 → 측정 → 채택 또는 폐기 → 반복\" 루프를 AI 에이전트가 자율로 돌리게 해, 개발자가 자는 동안 지정한 지표를 자동으로 최적화해 주는 오픈소스 확장이다."
tags = ["ai"]
categories = ["ai"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> pi-autoresearch는 "시도 → 측정 → 채택 또는 폐기 → 반복" 루프를 AI 에이전트가 자율로 돌리게 해, 개발자가 자는 동안 지정한 지표를 자동으로 최적화해 주는 오픈소스 확장이다.

---

## pi-autoresearch를 왜 쓰는지 감 잡기

코드 한 줄을 바꿀 때마다 테스트 실행 시간이 줄었는지 직접 재보고, 결과를 비교하고, 되돌릴지 말지 결정하는 과정은 반복 작업이다. pi-autoresearch는 이 루프 전체를 AI 에이전트에게 위임한다. 개발자가 "테스트 시간을 10% 줄여라"는 목표와 벤치마크 스크립트 한 개만 주면, 에이전트가 코드를 수정하고 측정하고 판단하는 과정을 밤새 반복한다. Shopify CEO Tobi Lutke가 직접 오픈소스로 공개한 pi 확장으로, GitHub 스타 4.7k를 넘겼다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 목표 설정 → 코드 수정 → 벤치마크 실행 → 개선 여부 판단 → 반복`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| pi | 터미널에서 쓰는 AI 코딩 에이전트. pi-autoresearch의 실행 숙주다. |
| autoresearch 루프 | 시도 → 측정 → 채택 또는 폐기 → 반복 사이클. 사람이 하던 반복 실험을 에이전트가 대신한다. |
| MAD (Median Absolute Deviation) | 측정값 흔들림의 중앙값. "이 개선이 노이즈인지 진짜인지"를 판단하는 기준이다. |
| confidence | 개선 폭을 MAD로 나눈 값. 2.0 이상이면 실제 개선, 1.0 미만이면 노이즈로 본다. |
| autoresearch.jsonl | 모든 실험 기록이 쌓이는 파일. 세션이 중단돼도 이 파일을 읽어 이어서 실행된다. |

## 예를 들어 설명하면

Jest 테스트 전체 실행 시간을 줄이고 싶다고 가정하자. 벤치마크 스크립트를 아래처럼 한 줄 작성하고 에이전트를 시작하면 된다.

```bash
# autoresearch.sh — 테스트 실행 시간을 METRIC으로 출력
pnpm test 2>&1 | tail -n 1 \
  | awk '{print "METRIC name=test_time value="$NF}'
```

에이전트는 이 스크립트를 반복 실행하며 `METRIC name=test_time value=18.42` 형태의 출력을 파싱해 jsonl에 기록한다. 실험이 3회 이상 쌓이면 MAD 기반 신뢰도가 함께 출력된다.

```
run #7  metric=test_time  value=18.42s  best_delta=-1.80s
         MAD=0.42s  confidence=4.3x   [green]
```

실험이 끝나면 `/skill:autoresearch-finalize`로 검증된 변경만 골라 독립 브랜치를 만든다. 각 브랜치를 PR로 리뷰하면 된다.

## 이 단계에서 중요한 판단 기준

`autoresearch.config.json`에 `maxIterations`를 반드시 설정해야 한다. 설정하지 않으면 LLM API 비용이 통제 없이 증가한다.

## 한 줄 요약 — 이것만 기억하면 된다

**벤치마크 스크립트 한 개와 목표 지표만 있으면 pi-autoresearch가 나머지 실험 루프를 자동으로 돌리고, MAD 신뢰도로 노이즈를 걸러낸 다음 리뷰 가능한 브랜치까지 뽑아준다.**

## 나중에 더 깊게 들어가면

- MAD 외에 다른 통계 기반 신뢰도 지표(예: t-검정, 부트스트랩)와의 차이
- pi 에이전트가 코드 수정 범위를 제한하는 방법(수정 허용 파일 경로 설정)
- 여러 지표를 동시에 최적화하는 다목적 실험 설계

---

**원본:** [pi-autoresearch, AI가 밤새 내 코드를 스스로 튜닝한다고요? — https://memoryhub.tistory.com/1053](https://memoryhub.tistory.com/1053)
