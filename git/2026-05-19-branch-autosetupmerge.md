# Git이 새 브랜치를 부모에 자동으로 연결시키는 함정 — `branch.autoSetupMerge`

> **TL;DR**
> `git checkout -b feature/x origin/main` 하면 git이 기본적으로 `feature/x`의 upstream을 `origin/main`으로 박는다.
> 이후 무심코 `git push` 하면 `main`을 향해 푸시가 날아갈 수 있다.
> 해결책 한 줄: `git config --global branch.autoSetupMerge simple`

---

## 1. 무슨 일이 벌어졌나

운영 보호 브랜치(`production` 같은)에서 기능 브랜치를 따서 작업하다가 `git push` 한 줄을 쳤을 때, 본인 기능 브랜치가 아니라 보호 브랜치 쪽으로 푸시가 시도되는 사건이 생긴다. 또는 `git status`가 이런 메시지를 보여준다.

```text
On branch feature/login-refactor
Your branch is ahead of 'origin/main' by 7 commits.
```

`feature/login-refactor`인데 왜 `origin/main`을 기준으로 ahead라고 말하는가? **upstream이 `origin/main`으로 설정돼 있기 때문**이다.

이 동작은 버그가 아니라 git의 **기본 설정**(`branch.autoSetupMerge=true`)이다.

## 2. 왜 이렇게 동작하는가

git에서 새 브랜치를 만들 때 시작점(start point)이 원격 트래킹 브랜치(`origin/main`, `origin/dev` 등)면, git은 친절하게도 "이 새 브랜치는 그 원격 브랜치와 연관된 거겠지?"라고 추측해서 upstream을 자동 설정한다.

```bash
# 아래 모든 경우가 origin/main을 upstream으로 박는다.
git checkout -b feature/x origin/main
git switch -c feature/x origin/main
git checkout main && git checkout -b feature/x   # main이 origin/main을 트래킹 중이면 전파
```

의도된 동작이긴 하다. 문제는 **"기능 브랜치 작업 워크플로우"에서는 거의 항상 잘못된 추측**이라는 점이다. `feature/x`의 upstream은 `origin/feature/x`여야 하지, `origin/main`이면 안 된다.

## 3. 4가지 옵션 비교

`branch.autoSetupMerge`는 5개 값을 가질 수 있다. 핵심 4개를 비교한다.

| 값 | 동작 | 위 시나리오 결과 | 추천도 |
|---|---|---|---|
| `true` (기본) | 원격 트래킹 브랜치에서 분기하면 무조건 트래킹 설정 | `feature/x`가 `origin/main`을 트래킹 — **사고 유발** | 비추 |
| `simple` | **새 브랜치 이름과 원격 브랜치 이름이 같을 때만** 트래킹 | `feature/x` ≠ `main` → 트래킹 안 함. `git checkout dev`(원격 dev 존재)는 트래킹 OK | **추천** |
| `inherit` | 시작점이 트래킹 중이던 정보를 그대로 상속 | `main`이 `origin/main`을 트래킹 중이면 → `feature/x`도 `origin/main` 트래킹. 결국 `true`와 거의 같음 | 조건부 |
| `false` | 절대 auto-track 안 함 | `git push -u origin feature/x`를 매번 직접 쳐야 함 | 안전 최우선일 때 |
| `always` | 트래킹 브랜치든 로컬 브랜치든 무조건 트래킹 | 사고 가장 많이 남 | 비추 |

### 각 옵션의 진짜 의미

**`true`** — git의 디폴트. "원격에서 따왔으면 그 원격을 따라간다." 직관적으로 보이지만 **기능 브랜치 워크플로우와 정면 충돌**한다. 대부분의 프로젝트에서 base 브랜치(`main`, `develop`, `production`)에서 짧은 수명 기능 브랜치를 따는 게 표준인데, 이 디폴트는 그걸 모른다.

**`simple`** — git 2.0부터 도입. "이름이 같을 때만 연결해라." 이게 **거의 모든 사용자가 원하는 동작**이다.
- `git checkout dev` (로컬 dev 없음, 원격 dev 존재) → 트래킹 ✓ 합리적
- `git checkout -b feature/x origin/main` → 트래킹 ✗ 합리적
- `git push -u origin feature/x` 한 번만 명시하면 그 다음부터 자기 자신을 트래킹

**`inherit`** — git 2.27부터. 시작점의 트래킹 정보를 복사한다. base 브랜치가 원격을 트래킹 중이면 새 브랜치도 그 원격을 트래킹하게 된다. 즉 base를 깐 채로 분기하면 `true`와 결과가 같다. **팀이 특정 트래킹 관습을 강제할 때만** 의미 있다.

**`false`** — 자동 트래킹을 완전히 끈다. 안전하지만, 모든 새 브랜치에 대해 `git push -u origin <name>`을 매번 쳐야 한다. 컨벤션이 강한 팀이거나, push 동작을 완벽히 통제하고 싶을 때.

## 4. 실험으로 확인하기

```bash
# 새 작업 디렉터리에서 깨끗하게 재현
mkdir /tmp/git-autosetup && cd /tmp/git-autosetup
git init -q --initial-branch=main
git commit --allow-empty -qm "init"
git remote add origin /tmp/git-autosetup  # 자기 자신을 원격으로 가짜 등록
git fetch -q origin

# 케이스 1: true (기본)
git config --unset branch.autoSetupMerge 2>/dev/null
git checkout -qb feat-true origin/main
git config branch.feat-true.merge
# → refs/heads/main  ← origin/main 트래킹 박힘

# 케이스 2: simple
git config branch.autoSetupMerge simple
git checkout -qb feat-simple origin/main
git config branch.feat-simple.merge || echo "upstream not set"
# → upstream not set ← 이름 다르니까 안 박음

# 케이스 3: simple + 이름 같음
git checkout -qb main-simple origin/main
git config branch.main-simple.merge || echo "upstream not set"
# → upstream not set  (이름이 main-simple이라 main과 안 맞음)

# 케이스 4: simple, 진짜 이름 같으면 트래킹
# (origin에 'develop'이 있다고 가정하고 동일 이름으로 분기하면 트래킹 됨)
```

이걸 돌려보면 `simple`이 얼마나 합리적인지 즉시 체감된다.

## 5. 권장 설정

```bash
# 글로벌 적용 (강력 추천)
git config --global branch.autoSetupMerge simple

# 동시에 push 동작도 안전하게
git config --global push.default simple
```

`push.default=simple`도 같이 잡으면 한 번 더 안전망이 생긴다.
- 옛 디폴트(`matching`): 같은 이름의 원격 브랜치 전부에 푸시 → 사고 유발
- `simple`(git 2.0+): upstream이 설정돼 있고 이름이 같을 때만 푸시

요즘 git은 `push.default`가 `simple`이 디폴트지만, 회사 환경/오래된 머신은 `matching`인 경우가 있어 명시해두는 게 안전하다.

## 6. 이미 잘못 설정된 브랜치 고치기

```bash
# 현재 upstream 확인
git rev-parse --abbrev-ref @{u}
# → origin/main  ← 잘못된 트래킹이라면

# 본인 브랜치로 재설정
git branch --set-upstream-to=origin/$(git branch --show-current)

# 또는 upstream 완전히 끊기
git branch --unset-upstream
```

## 7. 보너스 — 보호 브랜치 푸시 자체를 막는 hook

설정만으로 부족하다면, git pre-push hook으로 보호 브랜치 푸시를 물리적으로 막는다.

```bash
# .git/hooks/pre-push (chmod +x 필수)
#!/usr/bin/env bash
set -euo pipefail

PROTECTED='^(main|master|develop|production|release/.*)$'

if [[ "${PROTECTED_PUSH_OK:-0}" == "1" ]]; then
  exit 0
fi

while read -r local_ref local_sha remote_ref remote_sha; do
  [[ -z "$remote_ref" ]] && continue
  remote_branch="${remote_ref#refs/heads/}"
  if [[ "$remote_branch" =~ $PROTECTED ]]; then
    echo "" >&2
    echo "  pre-push: BLOCKED — '$remote_branch' is a protected branch." >&2
    echo "" >&2
    echo "  비상시 우회:" >&2
    echo "    PROTECTED_PUSH_OK=1 git push ..." >&2
    echo "" >&2
    exit 1
  fi
done
```

`.git/hooks/`는 클론마다 별도이므로, 팀 전체에 강제하려면 `core.hooksPath`를 추적 디렉터리(예: `.githooks/`)로 가리키고 그 안에 hook을 커밋한다.

```bash
# 팀 공유 hook 사용
mkdir .githooks && mv .git/hooks/pre-push .githooks/
chmod +x .githooks/pre-push
git config core.hooksPath .githooks
git add .githooks && git commit -m "chore: add shared pre-push hook"
```

## 8. 결론

- `branch.autoSetupMerge`의 기본값 `true`는 **기능 브랜치 워크플로우와 맞지 않는다.**
- 한 줄로 해결: `git config --global branch.autoSetupMerge simple`
- 사고가 진짜 두렵다면 pre-push hook으로 보호 브랜치 푸시 자체를 막는다.

git의 디폴트는 1990년대 후반 — 단일 메인 브랜치, push.matching, 짧은 브랜치 수명 같은 가정 위에 만들어졌다. 2026년의 트렁크 기반 개발 + 짧은 PR 브랜치 워크플로우에서는 디폴트를 한 번씩 다시 의심해볼 가치가 있다.

---

**참고**
- `man git-config` → `branch.autoSetupMerge`
- `man git-push` → `push.default`
- `githooks(5)` → pre-push 훅 명세
