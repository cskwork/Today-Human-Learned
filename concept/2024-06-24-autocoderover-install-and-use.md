# AutoCodeRover — AI가 GitHub 이슈를 읽고 코드를 고쳐주는 도구

> **TL;DR**
> AutoCodeRover는 GitHub 이슈를 입력받아 LLM이 코드베이스를 탐색하고 패치를 생성하는 자동화 도구다. Docker로 설치하고 API 키만 있으면 로컬에서 실행할 수 있다.

---

## AutoCodeRover를 왜 쓰는지 감 잡기

버그 리포트나 기능 요청이 GitHub 이슈로 올라오면 개발자는 코드베이스를 탐색하고, 원인을 찾고, 수정하고, 테스트하는 과정을 반복한다. AutoCodeRover는 이 흐름을 LLM이 대신 수행하도록 설계된 연구 도구다.

이슈 링크를 주면 저장소를 클론하고, 관련 코드를 탐색하고, 수정 패치를 생성한다. SWE-bench 벤치마크에서 실제 오픈소스 이슈를 자동으로 해결하는 실험에 사용되기도 한다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: 이슈 입력 → 저장소 클론 → LLM 코드 탐색 → 패치 생성 → 결과 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SWE-bench | 실제 GitHub 이슈와 정답 패치를 모아 AI 코드 수정 성능을 측정하는 벤치마크 데이터셋. |
| GitHub Issue Mode | 공개 저장소의 이슈 URL을 직접 넣어 AutoCodeRover가 패치를 생성하는 실행 방식. |
| Local Issue Mode | 로컬에 클론된 저장소와 텍스트 파일 형태의 이슈를 사용하는 오프라인 실행 방식. |
| conda 환경 | AutoCodeRover 컨테이너 내부에서 Python 의존성을 격리하는 가상 환경. 컨테이너 진입 후 활성화해야 한다. |
| denoising step | (해당 없음 - 이 문서 범위 밖) |

## 예를 들어 설명하면

로컬에서 처음 실행하는 전체 흐름이다.

```bash
# 1. 저장소 클론
git clone https://github.com/nus-apr/auto-code-rover.git
cd auto-code-rover

# 2. API 키 환경 변수 설정
export OPENAI_KEY=sk-...

# 3. Docker 이미지 빌드 (Apple Silicon은 Dockerfile.scratch 사용)
docker build -f Dockerfile -t acr .

# 4. 컨테이너 실행
docker run -it -e OPENAI_KEY="${OPENAI_KEY}" -p 3000:3000 -p 5000:5000 acr

# 5. 컨테이너 내부에서 GitHub 이슈 모드 실행
cd /opt/auto-code-rover
conda activate auto-code-rover
PYTHONPATH=. python app/main.py github-issue \
  --output-dir output \
  --setup-dir setup \
  --model gpt-4-0125-preview \
  --model-temperature 0.2 \
  --task-id my-task \
  --clone-link https://github.com/owner/repo.git \
  --commit-hash abc1234 \
  --issue-link https://github.com/owner/repo/issues/42
```

결과는 `output/` 디렉터리에 패치 파일 형태로 저장된다. 웹 UI는 `localhost:3000`에서 확인할 수 있다.

## 이 단계에서 중요한 판단 기준

"실제 프로덕션 코드 수정에 바로 쓰기보다는 패치 초안 생성 및 SWE-bench 실험 용도로 적합하다. 생성된 패치는 반드시 사람이 검토해야 한다."

## 한 줄 요약 — 이것만 기억하면 된다

**AutoCodeRover는 GitHub 이슈를 LLM에게 넘겨 코드 패치 초안을 자동 생성하는 연구용 도구다.**

## 나중에 더 깊게 들어가면

- SWE-bench 전체 태스크셋으로 성능 측정하기
- Anthropic, Groq 등 다른 LLM 제공자로 전환하는 방법
- AutoCodeRover 패치를 CI 파이프라인과 연동하는 아이디어

---

**원본:** [AutoCodeRover Install and Use — https://memoryhub.tistory.com/312](https://memoryhub.tistory.com/312)
