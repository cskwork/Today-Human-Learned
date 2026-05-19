+++
title = "Remotion: React 코드로 영상 생성, 영상 편집기 없이 MP4 만들기"
date = "2025-12-07"
description = "Remotion은 React 컴포넌트를 프레임 단위로 렌더링해 실제 MP4 파일로 변환하는 오픈소스 프레임워크로, 웹 개발 지식만으로 데이터 기반 영상을 자동 생성할 수 있다."
tags = ["javascript"]
categories = ["javascript"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Remotion은 React 컴포넌트를 프레임 단위로 렌더링해 실제 MP4 파일로 변환하는 오픈소스 프레임워크로, 웹 개발 지식만으로 데이터 기반 영상을 자동 생성할 수 있다.

---

## 이 주제를 왜 쓰는지 감 잡기

영상 편집 도구는 반복 작업에 약하다. 100명에게 각자 이름이 들어간 맞춤 영상을 보내려면 100번 직접 편집해야 한다. API 데이터를 받아 실시간으로 영상을 생성하는 것 역시 기존 편집 소프트웨어로는 불가능하다.

Remotion은 이 문제를 코드로 푼다. React 컴포넌트가 각 프레임을 그리고, FFmpeg가 그 프레임들을 묶어 MP4로 만든다. 데이터만 바꾸면 같은 구조의 영상을 수천 개 자동 생성할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: React 컴포넌트 작성 → 프레임별 렌더링 → FFmpeg 인코딩 → MP4 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Composition | 영상 한 편의 단위. 해상도, fps, 총 프레임 수를 여기서 정한다 |
| `useCurrentFrame` | 현재 몇 번째 프레임인지 알려주는 Hook. 이 숫자로 애니메이션을 제어한다 |
| `Sequence` | 특정 프레임 구간에만 컴포넌트를 표시하는 타임라인 역할 |
| `interpolate` | 프레임 번호를 원하는 값(투명도, 크기 등)으로 변환하는 유틸 함수 |
| `spring` | 자연스러운 물리 기반 모션을 만들어주는 애니메이션 함수 |

## 예를 들어 설명하면

텍스트가 서서히 나타나는 인트로 장면:

```tsx
import { useCurrentFrame, interpolate } from 'remotion';

const FadeInText: React.FC = () => {
  const frame = useCurrentFrame();
  // 0~30프레임(1초) 동안 투명도 0 → 1
  const opacity = interpolate(frame, [0, 30], [0, 1]);

  return (
    <div style={{ opacity, fontSize: 60, color: 'white' }}>
      2024 연말 결산
    </div>
  );
};
```

`useCurrentFrame()`이 반환하는 숫자가 0에서 시작해 1, 2, 3... 증가한다. 스크롤 위치에 따라 CSS 값을 바꾸는 것과 동일한 패턴이다. 프레임이 30에 도달하면 텍스트는 완전히 불투명해진다.

렌더링은 터미널 한 줄로 실행한다:

```bash
npx remotion render src/index.ts MyVideo out/video.mp4
```

## 이 단계에서 중요한 판단 기준

긴 영상이나 대량 생성이 필요하다면 로컬 렌더링 대신 Remotion Lambda(AWS 병렬 처리)를 써야 시간이 선형으로 단축된다.

## 한 줄 요약 — 이것만 기억하면 된다

**Remotion은 `useCurrentFrame`으로 프레임을 제어하는 React 컴포넌트를 작성하고, FFmpeg로 MP4를 뽑아내는 구조다. 데이터만 바꾸면 동일 템플릿으로 수천 개 영상을 자동 생성할 수 있다.**

## 나중에 더 깊게 들어가면

- Remotion Lambda 설정과 AWS IAM 권한 구성
- `<Sequence>`를 중첩해 복잡한 타임라인 구성하기
- Remotion Player로 웹 앱 안에 인터랙티브 영상 미리보기 삽입하기

---

**원본:** [Remotion: React 코드로 영상 생성, 영상 편집기 없이 MP4 생성하기](https://memoryhub.tistory.com/922)
