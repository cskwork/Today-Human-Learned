# Today Human Learned (THL)

> 매일 마주친 문제·도구·개념을 **주제별·날짜별**로 정리하는 학습 저장소.
> AI는 빠르게 잊지만, 사람은 글로 쌓아두면 복리로 남는다.

## 구조

```text
.
├── README.md         # 이 파일 (전체 인덱스)
├── git/              # 주제 디렉터리 — 도구·기술·언어·개념별로 자유롭게 추가
├── shell/
├── ...
└── <topic>/
    └── YYYY-MM-DD-<slug>.md
```

- 파일명 규칙: `YYYY-MM-DD-<kebab-slug>.md`
- 주제(topic) 디렉터리는 필요할 때 자유롭게 추가. 처음에는 거칠게 분류해도 OK.
- 글 하나는 **하나의 주제만** 다룬다. 짧아도 괜찮다.

## 글 작성 원칙

1. **TL;DR을 맨 위에** — 3줄 안에 결론이 보여야 한다.
2. **"왜 그렇게 되는지"를 적는다** — 명령어 모음집은 검색하면 나온다.
3. **실험으로 확인한 것만** — 추측은 추측이라고 표기한다.
4. **회사·고객·내부 시스템 이름은 일반화한다** — public 가능성을 항상 가정한다.

## 인덱스

### git

- 2026-05-19 — [Git이 새 브랜치를 부모에 자동으로 연결시키는 함정 — `branch.autoSetupMerge`](git/2026-05-19-branch-autosetupmerge.md)

### code-search

- 2026-05-19 — [codegraph vs graphify — Java 코드베이스에서 측정해본 결과](code-search/2026-05-19-codegraph-vs-graphify-java.md)

### claude-code

- 2026-01-22 — [Claude Code 처음 세팅하는 사람을 위한 기본 가이드](claude-code/2026-01-22-claude-code-basic-setup.md)

### security

- 2026-05-18 — [Salt vs Pepper — 비밀번호 해시에서 둘이 다른 이유](security/2026-05-18-salt-vs-pepper.md)

---

작성: [@cskwork](https://github.com/cskwork)
