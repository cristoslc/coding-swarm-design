## Key findings

### 1. GitHub Agentic Workflows is the leading production-scale implementation of the issue→PR model

The GitHub Agentic Workflows framework (Microsoft Research / GitHub Next) implements the three-tier model at scale, with 500+ merged agentic PRs across 19+ workflow categories.

| Musing layer | Gh-AW equivalent | Scale evidence |
|---|---|---|
| Issue (commission) | Issue with parent/child hierarchy + `/plan` command | Plan Command: 514 merged PRs (67% merge rate) |
| PR (work bundle) | PR from Copilot coding agent | Issue Monster dispatches tasks one at a time |
| Cross-PR narrative | Issue comment thread + causal chain tracking | Discussion→Issue→PR attribution across handoffs |

Parent-child issue hierarchies, Sub Issue Closer, and ResearchPlanAssignOps validate the "commission stack, not worktree stack" model.

### 2. Sub-issues are the native sub-commission mechanism

ResearchPlanAssignOps formats each sub-issue for a coding agent with "a clear objective, the files to touch, step-by-step implementation guidance, and acceptance criteria" — matching the musing's commission structure.

### 3. Addy Osmani independently identifies the issue-as-commission pattern

In "Conductors to Orchestrators" (O'Reilly, Feb 2026), Osmani describes: "An engineer can assign tasks to AI agents (e.g., via issues or prompts) and have those agents asynchronously produce code changes — often as ready-to-review pull requests." Orchestrator workflows produce "persistent artifacts like branches, commits, and pull requests that are preserved in version control."

### 4. Chronicling remains a gap

No existing system provides a fine-grained execution record equivalent to chronicle.md. Gh-AW relies on forge-native records (issue comments, PR timeline, CI logs) but none capture agent reasoning, phase transitions, or script invocations at the execution level.

### 5. The arXiv study empirically validates PR decomposition requirements

33k+ agentic PRs analyzed. Small, focused PRs succeed more. Failed PRs share root cause: poor task decomposition and lack of project coordination. This validates the musing's emphasis on well-scoped sub-issues and limited work bundles.

---

## Counterevidence: risks and limitations of the PR-as-channel model

### 5. AI-generated code carries measurably higher defect rates

CodeRabbit's analysis of 470 open-source PRs found that AI-co-authored PRs contain approximately 1.7x more issues overall than human-only PRs. Logic and correctness issues were 75% more common, readability issues more than 3x, security issues up to 2.74x higher. This is not a minor difference — it is a structural quality gap that any PR-based agent protocol must account for through gate reviews and automated quality checks.

### 6. PR volume has overwhelmed open source maintainer capacity

Mitchell Hashimoto (Ghostty): "Agentic programming has removed the effort-based backpressure that previously limited low-quality submissions, making it too easy to produce large volumes of bad content with minimal cost." Ghostty, tldraw, curl, QEMU, and NetBSD have all restricted or banned AI-generated contributions. GitHub is considering a PR "kill switch" for overburdened maintainers (Camilla Moraes, GitHub PM). One in ten AI-created PRs is legitimate (maintainer survey).

### 7. Platform infrastructure is straining under agent commit volume

GitHub now handles 275 million commits/week (14x year-over-year increase), driven by AI agents. 17 million monthly agentic PRs in March 2026 (325% increase in six months). Five major incidents occurred in early April 2026. The PR-as-channel model assumes the forge platform has infinite capacity — it does not.

### 8. The PR primitive itself is becoming an attack surface

Maintainers report that the same open-forge PR model that works for human contributors is being gamed by agents. Projects are closing contributions not because the code is bad, but because the cost of triage exceeds the value of any single AI-generated PR. The musing's model must account for volume gating, attribution requirements, and the fact that PR as a communication primitive requires trust — which agents have eroded.

---

## Gaps between trove evidence and musing

| Musing claim | Trove validates | Trove challenges |
|---|---|---|
| Issue = commission, PR = work bundle | Gh-AW Plan Command, IssueOps | — |
| Sub-commissions as related issues | Parent-child issues, Sub Issue Closer | — |
| Causal chain across agent handoffs | Discussion→Issue→PR attribution | — |
| Small, focused PRs | arXiv 33k-PR study | — |
| chronicle.md as execution record | Gap confirmed — no system has this | — |
| Chronicle commit convention (`[chronicle]`) | No precedent found | — |
| Branch as primitive, worktree as local | — | Verified (worktrees not in external sources) |
| Issue-level escalation after failures | — | No external validation found |
| Dispatch vs CI pipeline distinction | — | Implicit in Gh-AW's pipeline model vs. agentic graph architectures |
| Phase-level enforcement | — | No external system enforces multi-phase lifecycle |
| **PR quality: assumed acceptable** | — | **1.7x more issues, 3x readability, 2.74x security (CodeRabbit)** |
| **PR volume: assumed manageable** | — | **Maintainer burnout, project bans, kill-switch discussions (Pickuma)** |
| **Platform capacity: assumed infinite** | — | **14x commit growth, five April incidents (van Riel)** |
| **PR trust: assumed** | — | **Ghostty, tldraw, QEMU banning AI PRs (Pickuma)** |
| **Effort-based backpressure: not addressed** | — | **Hashimoto: removed by agentic programming (Pickuma)** |

## Synthesis

The three-tier communication model (issue → PR → chronicle) is validated by production evidence at scale. Gh-AW demonstrates that the pattern works: 500+ merged agentic PRs, causal chains across agent handoffs, parent-child issue hierarchies as decomposition primitives. The chronicle.md concept fills a genuine gap.

However, the trove also reveals four structural risks the musing does not adequately address:

1. **Quality risk**: AI PRs carry 1.7x more defects. The PR-as-channel model needs structured review gates that match this elevated risk profile.
2. **Volume risk**: PR output now outpaces human review capacity. The model needs volume gating (WIP limits, rate limiting) or the PR primitive becomes counterproductive.
3. **Platform risk**: Infrastructure cannot keep pace with agent commit volume. Protocol-level capacity awareness is needed.
4. **Trust erosion**: Open source projects are restricting the PR primitive itself. The model must account for the fact that agents have degraded the forge's social contract.

These risks do not invalidate the model. They define its boundary conditions and the design constraints any implementation must respect.