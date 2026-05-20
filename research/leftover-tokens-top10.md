# Top 10 Ways to Burn Leftover Claude Code + Codex Tokens

Compiled 2026-05-20. Ranked by community popularity (Reddit/HN/Medium/dev.to/blog signal) and unit-of-token productivity. Each method lists target user, setup cost, pros, cons, and a quick-start recipe.

## Ranking summary

| # | Method | Tool | Best for |
|---|--------|------|----------|
| 1 | Ralph Wiggum overnight loop | Claude Code / Codex | Solo devs shipping side projects |
| 2 | Claude Code Routines (cloud schedule) | Claude Code | Maintenance work, no laptop needed |
| 3 | Parallel sub-agents / agent teams | Claude Code | Multi-file refactors, reviews |
| 4 | Codex `/goal` long-horizon autonomous tasks | Codex CLI | Multi-hour migrations, deployments |
| 5 | Test coverage generation | Both | Repos stuck at 30–50% coverage |
| 6 | Legacy refactoring / file splitting | Both | 500–800+ LOC files, hot spots |
| 7 | Security audit + dependency upgrades | Claude Code | OWASP scan, CVE triage, SonarQube |
| 8 | Karpathy-style LLM Wiki / second brain | Claude Code | Personal knowledge accumulation |
| 9 | Codebase onboarding + doc generation | Both | New repo, missing CLAUDE.md/README |
| 10 | Spec-to-ship side project / hackathon | Both | Prototyping, learning new stack |

---

## 1. Ralph Wiggum overnight loop
The dominant "what to do with spare tokens" pattern. You write a task list, run a Bash loop that re-invokes Claude until a completion signal fires, then sleep. Hooks gate "I'm done" until the work is actually done.

- **Target**: Solo devs with a backlog and a Max/Pro plan that rolls weekly.
- **Setup**: ~15 min. Clone `frankbria/ralph-claude-code` or copy a 30-line bash loop. Define a completion sentinel ("complete"). Set per-hour API cap.
- **Pros**: Real autonomous shipping. YC hackathon teams report 6 repos overnight for ~$297. Built-in circuit breaker stops thrash.
- **Cons**: Easy to burn through credits on a bad spec. Needs a clear spec + tests as the convergence signal. Quality varies without a verification reviewer agent.
- **Recipe**: `while true; do claude -p "Pick next TODO and ship; output 'complete' when done" || break; done`. Add a hook that checks tests before allowing "complete".

## 2. Claude Code Routines (scheduled cloud automation)
Routines run on Anthropic-managed infra on a cron/GitHub-event/API trigger. They live in a **separate quota bucket** from interactive sessions — this is the single best leftover-token vehicle if you have a Pro/Max plan.

- **Target**: Anyone who wants overnight or weekly maintenance done without keeping a laptop on.
- **Setup**: `/schedule` in the CLI, or the web/desktop dashboard. One-time prompt + repo + connectors.
- **Pros**: Doesn't eat interactive session limits. Survives laptop sleep. Built-in GitHub triggers, Slack outputs.
- **Cons**: Hourly cadences blow the daily cap by lunch — most useful at once-a-day or once-a-week cadence. Limited debug visibility vs. local.
- **Recipe**: nightly "review yesterday's merged PRs and open update PRs for stale deps"; weekly "dependency audit + drafts of upgrade PRs"; on-PR "apply internal checklist + inline comments".

## 3. Parallel sub-agents / agent teams
Spawn N sub-agents that each take an independent slice — security review, perf check, types, tests — and report back. As of Apr 2026, sub-agents + MCP init in parallel, so startup overhead is gone. Cuts multi-file work 50–70%.

- **Target**: Devs touching ≥3 files or wanting multi-perspective review.
- **Setup**: Built-in `Agent` / `Task` tool; agent teams via `code.claude.com/docs/en/agent-teams`.
- **Pros**: Token-parallel — you spend more in absolute terms but get answers 4–9x faster. Great for code reviews (9 reviewers simultaneously is a popular pattern).
- **Cons**: Each agent is a fresh context window cost; coordination overhead if tasks share state. Don't use for sequentially dependent work.
- **Recipe**: For a PR, dispatch in one message: security-reviewer, perf checker, type checker, doc-updater, test-engineer.

## 4. Codex `/goal` long-horizon autonomous tasks
The Codex equivalent of Ralph, but native. `/goal` activates an agentic Stop-hook loop that plans, executes, self-corrects, and only stops when the goal holds or it hits an unrecoverable block.

- **Target**: Codex Plus/Pro users with long migrations, ticket→fix→deploy chains, or overnight test/doc runs.
- **Setup**: Enable the `goals` feature flag. Write 4 spec files (Prompt / Plan / Implement / Documentation) using the official Scope / Behavior / Verification vocabulary. Add HUMAN GATE markers around irreversible steps.
- **Pros**: Doesn't give up until done. Auto-clears on success. Pairs with Automations for scheduled runs.
- **Cons**: A bad spec burns 7% of weekly Plus quota on a single prompt (reported). Always gate destructive steps.
- **Recipe**: `codex /goal "fix ticket X end-to-end: repro, branch, fix, tests, PR, deploy, verify, comment on ticket"`.

## 5. Test coverage generation
Reported jumps from 30% → 85% coverage in a 20-minute session on real codebases. High value per token because coverage compounds — future agents work on a safer base.

- **Target**: Any repo where coverage is the bottleneck for refactoring or shipping confidently.
- **Setup**: None. Just point Claude/Codex at a module and ask for table-driven / property-based tests with edge cases.
- **Pros**: Mechanically suited to LLM work. Detects 3–5 issues per file human review misses, often including 1 security issue.
- **Cons**: Generated tests can pass against current (buggy) behavior — pair with mutation testing or human spot-check.
- **Recipe**: "Read `src/foo.py`, list every branch, generate pytest cases covering happy + edge + failure, run, iterate until coverage ≥ 85%."

## 6. Legacy refactoring / file splitting
Splitting an 800-line legacy file into testable modules reportedly drops from 4–8 hours manual → <15 minutes with Claude Code. AI-assisted refactoring reduced regression error rate ~40% vs. manual in reported studies.

- **Target**: Repos with God files, copy-pasted modules, or stuck mid-migration.
- **Setup**: Lock with `git diff --stat` baseline + a green test suite as guardrail.
- **Pros**: One of the highest ROI uses of tokens — turns dread tasks into 20-min sessions.
- **Cons**: Without tests, refactor introduces silent regressions. Always run before/after test+lint+type-check.
- **Recipe**: "Identify the 3 concerns mixed into `legacy.py`. Split by domain (not by type). Keep public API identical. Run tests after every commit."

## 7. Security audit + dependency upgrades
Three battle-tested skills: `/security-review`, `/secret-scanner`, `/deps-check`. Trace imports → real exposure; map dep graph → CVE cross-reference → minimum fixed version; review changelogs for breaking changes.

- **Target**: Any production codebase. Especially valuable before quarterly audits.
- **Setup**: `/security-review` is built-in. Pair with GitHub Action for PR-time review. Full-codebase audit skill uses 1M context for cross-file flow tracing.
- **Pros**: Catches SQLi, XSS, auth flaws, insecure data handling, vulnerable deps. Classifies findings OWASP/CWE.
- **Cons**: False positives common; needs a triage pass. Major-version upgrades need human review of breaking changes.
- **Recipe**: nightly routine: `/security-review && /deps-check && open PRs for fixed-only upgrades`.

## 8. Karpathy-style LLM Wiki / personal second brain
Andrej Karpathy's pattern: a folder of markdown files (Obsidian vault is just markdown), Claude Code reads/writes it. The wiki compounds across sessions. Pair with `wiki-init`, `wiki-ingest`, `wiki-query`, `wiki-update`, `wiki-lint` skills.

- **Target**: Researchers, knowledge workers, long-term learners. Anyone with a chat/note history worth synthesizing.
- **Setup**: A repo. A `CLAUDE.md` constitution. An ingest pipeline (papers / articles / transcripts → wiki pages).
- **Pros**: Tokens turn into durable assets — not just one-off answers. Every session leaves a trace.
- **Cons**: Garbage in, garbage out. Needs `wiki-lint` runs every 5–10 ingests to catch contradictions / orphans.
- **Recipe**: `/wiki-init` → ingest 10 papers via `/wiki-ingest` → query with `/wiki-query` → `/wiki-lint` weekly.

## 9. Codebase onboarding + documentation generation
Auto-generate `CLAUDE.md`, architecture map, key entry points, setup/run/test commands, prioritized onboarding checklist. Codex equivalent maps unfamiliar repos and points to the next files worth reading.

- **Target**: Joining a new repo. Open-source maintainers wanting better READMEs. Teams onboarding new hires.
- **Setup**: `codebase-onboarding` skill (available in everything-claude-code and several skill marketplaces).
- **Pros**: Output is itself a force multiplier for every later session in that repo.
- **Cons**: Generated docs drift; rerun after major restructures.
- **Recipe**: `/init` (built-in) or invoke `codebase-onboarding` skill → outputs `CLAUDE.md` + architecture.md + onboarding checklist.

## 10. Spec-to-ship side project / hackathon prototype
The "ambitious side project" use of leftover tokens. Define spec evening → run Ralph or `/goal` loop overnight → wake up to a working app. Multiple YC hackathon teams reported shipping 1–6 repos a night.

- **Target**: Learning a new stack, validating ideas, building personal tools.
- **Setup**: Write spec + test acceptance criteria. Use `using-git-worktrees` for isolation.
- **Pros**: Best psychological ROI — tokens become tangible projects, not just incremental commits.
- **Cons**: Cost can balloon if spec is vague. Cap API calls/hour. Always run in a worktree, never on main.
- **Recipe**: spec.md + tests.md + `omc autopilot` or `omc ralph` → worktree → review on wake.

---

## Honorable mentions (didn't make top 10)
- **Coding tutor** — personalized tutorials grounded in your real code. Lower token throughput than autonomous runs but high learning ROI.
- **MCP + Agents SDK pipelines** — exposing Codex/Claude Code as MCP servers for deterministic multi-agent workflows. Powerful, but heavy setup; really pays off only if reused across many tasks.
- **Personal life automation** — Gmail triage, Calendar agenda, KakaoTalk drafting (per the skill registry: `gws-gmail`, `gws-calendar`, `kakao-message-tone`). Modest token use, very high quality-of-life.
- **Reddit/Naver-blog drafting** — `reddit-poster`, `naver-blog-easy-explainer` skills convert spare tokens into content. Watch platform policy.

## Anti-patterns / what NOT to do with spare tokens
- Letting a single conversation balloon past ~15 messages. Past message 15, every reply re-sends 4–6k tokens of stale context.
- Using Opus for trivial edits. Switch to Haiku for simple lookups; reserve Opus for genuine deep reasoning.
- Setting a routine to hourly. Pro/Max budgets are gone by lunch.
- Running Ralph without a circuit breaker. Same error 3x = halt.

---

## Sources

### Claude Code & Anthropic
- [Models, usage, and limits in Claude Code | Claude Help Center](https://support.claude.com/en/articles/14552983-models-usage-and-limits-in-claude-code)
- [Best practices for Claude Code](https://code.claude.com/docs/en/best-practices)
- [Orchestrate teams of Claude Code sessions](https://code.claude.com/docs/en/agent-teams)
- [Automated Security Reviews in Claude Code | Claude Help Center](https://support.claude.com/en/articles/11932705-automated-security-reviews-in-claude-code)

### Ralph loop & autonomous overnight
- [Claude Code Ralph Wiggum: Run Autonomously Overnight](https://claudefa.st/blog/guide/mechanics/ralph-wiggum-technique)
- [Ralph Wiggum - AI Loop Technique for Claude Code](https://awesomeclaude.ai/ralph-wiggum)
- [GitHub - frankbria/ralph-claude-code](https://github.com/frankbria/ralph-claude-code)
- [Ralph Loop: Running Claude Code Autonomously for Hours - Samanvya Tripathi](https://samanvya.dev/blog/claude-code-ralph-loop)
- [The Ralph Wiggum Approach (dev.to)](https://dev.to/sivarampg/the-ralph-wiggum-approach-running-ai-coding-agents-for-hours-not-minutes-57c1)
- [Claude Code Autonomous Loops: Ship Features While You Sleep](https://claudefa.st/blog/guide/mechanics/autonomous-agent-loops)
- [How I Shipped 100k LOC in 2 Weeks with Coding Agents](https://alexlavaee.me/blog/ai-coding-infrastructure/)

### Routines & scheduling
- [Claude Code Routines (Pasquale Pillitteri)](https://pasqualepillitteri.it/en/news/851/claude-code-routines-cloud-automation-guide)
- [Claude Code Routines — Automate Your Dev Work While You Sleep (AyyazTech)](https://www.ayyaztech.com/blog/claude-code-routines-tutorial)
- [Claude Code Routines Explained (lowcode.agency)](https://www.lowcode.agency/blog/claude-code-routines-explained)
- [How to Use Claude Code Scheduled Tasks (MindStudio)](https://www.mindstudio.ai/blog/claude-code-scheduled-tasks-cloud-routines)

### Sub-agents & parallel work
- [How to Use Claude Code Sub-Agents for Parallel Work — Tim Dietrich](https://timdietrich.me/blog/claude-code-parallel-subagents/)
- [Claude Code Sub-Agents: Parallel vs Sequential Patterns](https://claudefa.st/blog/guide/agents/sub-agent-best-practices)
- [9 Parallel AI Agents That Review My Code — HAMY](https://hamy.xyz/blog/2026-02_code-reviews-claude-subagents)
- [Claude Code Sub-Agents Guide (2026) — AI Builder Club](https://www.aibuilderclub.com/blog/claude-code-sub-agents-guide)

### Codex CLI & /goal
- [Codex rate card | OpenAI Help Center](https://help.openai.com/en/articles/20001106-codex-rate-card)
- [Codex CLI Features – OpenAI Developers](https://developers.openai.com/codex/cli/features)
- [Using Codex with your ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan)
- [How to Use OpenAI Codex's /goal Command (MindStudio)](https://www.mindstudio.ai/blog/openai-codex-goal-command-autonomous-tasks)
- [Codex Goal Mode in-depth analysis](https://help.apiyi.com/en/codex-goal-mode-autonomous-task-guide-en.html)
- [Building Consistent Workflows with Codex CLI & Agents SDK](https://cookbook.openai.com/examples/codex/codex_mcp_agents_sdk/building_consistent_workflows_codex_cli_agents_sdk)
- [Show remaining Codex credits / usage in the CLI statusline (GitHub issue)](https://github.com/openai/codex/issues/19555)
- [Understand large codebases | Codex use cases](https://developers.openai.com/codex/use-cases/codebase-onboarding)

### Test coverage, refactoring, code quality
- [Claude Code Use Cases | SFEIR Institute](https://institute.sfeir.com/en/claude-code/claude-code-resources/use-cases/)
- [Code Refactoring With Claude AI: Ultimate Guide](https://claytonjohnson.com/code-refactoring-with-claude-ai-for-the-lazy-but-brilliant/)
- [Claude Code Prompts for Refactoring (Ralphable)](https://ralphable.com/blog/claude-code-prompts-for-refactoring)
- [How to Automate Refactoring with Claude Code](https://claudecode-lab.com/en/blog/claude-code-refactoring-automation/)
- [Writing tests with Claude Code (On Test Automation)](https://www.ontestautomation.com/writing-tests-with-claude-code-part-1-initial-results/)
- [awesome-claude-code-toolkit / test-coverage](https://github.com/rohitg00/awesome-claude-code-toolkit/blob/main/commands/testing/test-coverage.md)

### Security & dependency management
- [Security Scanning with Claude Code (developertoolkit.ai)](https://developertoolkit.ai/en/claude-code/lessons/security-audit/)
- [GitHub - McGo/claude-code-security-audit](https://github.com/McGo/claude-code-security-audit)
- [Automate OWASP Security Audits with Claude Code Security Pack (dev.to)](https://dev.to/myougatheaxo/automate-owasp-security-audits-with-claude-code-security-pack-4mah)
- [Dependency Management with Claude Code (dev.to)](https://dev.to/myougatheaxo/dependency-management-with-claude-code-auditing-updating-and-staying-secure-3hd5)

### Personal knowledge / second brain / wiki
- [Karpathy's LLM Wiki — Personal Knowledge Base With Claude Code (MindStudio)](https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-knowledge-base-claude-code)
- [How to Build an AI Second Brain with Claude Code and Obsidian (MindStudio)](https://www.mindstudio.ai/blog/build-ai-second-brain-claude-code-obsidian)
- [I Built a Personal Second Brain with Markdown Files and Claude Code (dev.to)](https://dev.to/shirisha_uppoju_b20d30705/i-built-a-personal-second-brain-with-markdown-files-and-claude-code-heres-how-2m14)
- [GitHub - charlie947/ai-second-brain](https://github.com/charlie947/ai-second-brain)

### Token optimization & anti-patterns
- [10 Tips to Stop Burning Your Tokens in Claude Code (Medium)](https://medium.com/@habib23me/10-tip-to-stop-burning-your-tokens-in-claude-code-4776d4ac8956)
- [7 Practical Ways to Reduce Claude Code Token Usage (KDnuggets)](https://www.kdnuggets.com/7-practical-ways-to-reduce-claude-code-token-usage)
- [Claude Code Token Optimization: Stop the $1,600 Bill (buildtolaunch)](https://buildtolaunch.substack.com/p/claude-code-token-optimization)
- [Pre-Warm Claude Code: Beat the 5-Hour Rate Limit (Medium)](https://medium.com/@AymanAlkurdi/stop-hitting-claudes-rate-limit-at-11-am-7cad2b7db3d9)
