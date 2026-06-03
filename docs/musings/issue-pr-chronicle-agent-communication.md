# Musing: Issue, PR, and chronicle as three-tier agent communication layers

## The idea

Inter-agent communication in a swarm operates at three distinct altitudes, each with its own forge primitive and record granularity:

| Layer | Primitive | Scope | Records | Drives |
|-------|-----------|-------|---------|--------|
| Strategic | **Issue** | Commission (entire feature/epic) | Mission, ACs, operator gates, cross-PR narrative | Why we're doing this |
| Tactical | **PR** | Work bundle (one implementation chunk) | Approach decisions, review findings, technical discussions | How we're doing this |
| Execution | **chronicle.md** (file on branch) | Implementation session | Phase transitions, timestamps, script invocations | What happened |

The layers are composable: a single issue can spawn many PRs, each with its own chronicle. Sub-commissions are related issues, linked from the parent issue. Branches are the primitive; worktrees are a local mechanism for managing branches, not part of the protocol.

## Concrete example: "Add dark mode support"

```
Issue #27 — "Add dark mode support to the user dashboard"
│
├─ Issue thread (cross-PR narrative)
│  Agent: crawl-self — no overlap, WIP check passes
│  Agent: define-done — 5 ACs: design tokens, component coverage,
│         persistence, a11y contrast, feature flag
│  Agent: plan identifies 3 work bundles. Operator approves.
│  Agent: PR #271 (design tokens) done. PR #272 (part 1) done.
│  Agent: AC-5 (a11y contrast) needs its own evaluation →
│         opens Issue #34 as sub-commission
│  Agent: Issue #34 shipped — all palette pairs pass WCAG AA
│  Agent: PR #273 (feature flag) done. All ACs pass.
│  Operator: approved. Shipped.
│
├─ PR #271 — "Extract design tokens to shared palette"
│  chronicle.md: token schema, light/dark variable files
│
├─ PR #272 — "Migrate core components (sidebar, header, layout)"
│  chronicle.md: replace hardcoded colors, component audit
│
├─ PR #273 — "Feature flag + rollout toggle in settings"
│  chronicle.md: flag definition, settings UI, LaunchDarkly integration
│
└─ linked → Issue #34 — "Verify WCAG AA contrast for palette pairs in dark mode"
               (sub-commission, parent=Issue #27)
               │
               ├─ Issue thread
               │  Agent: define-done — 2 ACs: automated checks +
               │         human signoff. PRs filed.
               │  Agent: PR #341, PR #342 done. Both ACs pass.
               │  Agent: result posted to Issue #27 thread.
               │  Operator: approved. Shipped.
               │
               ├─ PR #341 — "Automated contrast checker for palette"
               │  chronicle.md: script, CI integration
               └─ PR #342 — "Visual regression snapshots for themes"
                  chronicle.md: snapshot tooling, baseline capture
```

## Layer responsibilities

### Issue layer (strategic)

The issue body is the canonical commission record. It contains:
- **Mission**: one-paragraph outcome statement
- **Acceptance criteria**: falsifiable, at-commission-altitude
- **Plan**: phased around gates, referencing PRs as work bundles
- **Why this exists**: context and rationale

The issue comment thread is the cross-PR narrative. It records:
- Phase transitions (crawl-self result, operator approvals at gates)
- Sub-commission spawn/receive events
- Links to PRs as they're filed and completed
- Final ship summary when the commission closes

Issue comments are the **most aerial** layer — the operator-facing summary across all PRs.

### PR layer (tactical)

PRs are work bundles — discrete chunks of implementation that fit in a single reviewable unit. A PR:
- Has a focused scope (one design decision, one component migration batch)
- Contains review discussions about approach
- Gets review-approved by code review agents
- Pushes code that the operator or review agents approve

The PR comment thread records:
- The working agent's plan for this specific work bundle
- Code review agents' findings (inline on diffs)
- The working agent's responses and plan to address findings
- Technical decisions made during implementation

PR comments are **mid-aerial** — the reviewer/implementer-facing layer. Not every execution detail belongs here; only approach-level and review-level communication.

### chronicle.md layer (execution)

The chronicle file lives on the branch, committed as part of the PR's commit history. It records:
- Every phase transition (mission → crawl-self → crawl-context → ... → ship)
- Every script invocation (phase.py, crawl-self.py, wip-check.py, verify.py)
- Timestamps for each event
- Agent reasoning that doesn't warrant PR-level discussion

chronicle.md is the **finest-grained** layer — the evidence trail for execution. It is NOT a communication surface (no threading, no replies). It is a record.

Commit convention: `[chronicle]` prefix in the commit message so the PR timeline shows chronicle entries distinctly from code changes.

## Sub-commissions as related issues

A sub-commission is a commission whose execution is delegated to a separate agent/scope:

1. **Parent issue thread records the spawn**: agent comment describes why a sub-commission is needed, referencing the acceptance criterion.
2. **Sub-commission gets its own issue**: the child issue links back to the parent.
3. **Sub-commission has its own PRs**: the child may itself have multiple work bundles.
4. **Sub-commission reports back to parent**: when the child ships, its final thread comment links to the result; the parent thread records receipt.
5. **Parent cannot ship until child ships**: the child satisfies an acceptance criterion of the parent.

### Decomposition: sub-issues vs. recursive PRs

A sub-issue whose work depends on the parent's unmerged code changes must PR against the parent's branch, creating a recursive git topology:

```
trunk
  └─ PR-271 (parent's worktree, for Issue #27)
       └─ PR-341 (sub-issue's branch, dependent on PR-271's code)
```

A sub-issue whose work is scoped independently of the parent's code can PR directly against trunk, even while the parent remains open:

```
trunk
  ├─ PR-271 (for Issue #27)
  └─ PR-341 (for Issue #34, no code dependency on #27)
```

Both patterns are valid. The right choice depends on whether the sub-issue's code depends on the parent's in-flight changes. The git topology is always flat at the level where work is truly independent; recursive when there are code dependencies.

### Decomposition guideline: commit count, not wall time

The meaningful metric for branch drift risk is commit count, not wall time. AI agents accumulate commits faster than humans but the drift risk from large branches is real regardless of speed. A PR with N+ commits should have been multiple properly-scoped sub-issues from the start.

## PR splitting and scope redefinition

If a PR grows too large during implementation, the correct response is **abandon and re-scope**, not mid-stream splitting:

1. Close the PR (abandoned) with a comment explaining why: scope was broader than expected, initial decomposition was wrong.
2. Return to the parent issue and redefine the scope — break into properly-sized sub-issues.
3. Spawn new PRs from the redefined decomposition.

This is consistent with empirical findings: failed agentic PRs frequently stem from poor task decomposition. An overgrown PR is a diagnostic signal, not a situation to patch.

## Branches, not worktrees

Branches are the primitive. Worktrees are a local mechanism for managing branches on disk — a runtime substrate detail, not part of the agent communication protocol. An agent should be able to operate on a branch whether it's realized as a worktree, a container sandbox (CI runner), or a server-side checkout. The protocol constrains the branch lifecycle, not the storage mechanism.

## Error and abandon at different altitudes

| Signal | Altitude | What happens |
|---|---|---|
| PR abandoned (single failed attempt) | Tactical | PR closed. Dispatcher retries or re-scopes. No escalation. |
| Issue accumulates multiple abandoned PRs | Strategic | Issue escalated with `issue/escalated`. Human attention needed. The issue thread preserves the full failure record for diagnosis. |

A single failed PR is normal — the task was too large, the approach was wrong, or the agent failed. It's the *pattern* of repeated failure under the same issue that indicates a deeper problem requiring operator attention.

## Escalation at the issue level

Escalation triggers when an issue accumulates multiple closed-without-merge PRs (attempt count + abandonment rate exceeding a threshold). The issue body gains `Status: Escalated` and `needs-operator` label. The issue thread contains the full chronicle of failed attempts — each PR's closure comment, review findings, and abandonment reason — so the operator can diagnose the root cause without re-reading every PR.

This preserves the thread while leaving the PR-level "try and fail" cycle lightweight. PRs fail; issues escalate.

## WIP limits: operator attention management

WIP limits are about operator attention in small teams or single-operator contexts, not about system capacity. They prevent the operator from having too many cross-PR narratives to follow simultaneously. They are a project-specific concern, not part of the communication protocol.

## Relationship to execution mechanisms

The PR-as-channel model is a communication protocol. It is orthogonal to how agents are executed:

| Execution mechanism | Shape | How it interacts with PR-as-channel |
|---|---|---|
| **Dispatch skill** (agent spawns sub-agents, non-linear graph) | Graph — agents spawn agents, recombine, iterate | Dispatch creates worktrees/branches from issues. The PR is where sub-agents report results upward. |
| **CI pipeline** (linear DAG, deterministic) | Pipeline — sequential or parallelized, known upfront | CI creates PRs from pipeline jobs. The PR is where pipeline output is reviewed. |
| **Agent teams** (peer agents, shared context) | Mesh — agents negotiate task division | All agents post to the same issue/PR thread. The issue body is the shared spec. |

The dispatch skill enables agentic graph architectures (branching, recombination, iteration loops, partial merges). CI pipelines are a fundamentally different model — linear implementation protocols. Both can use PRs as their communication surface.

## What stays file-on-disk

- **chronicle.md** — committed on the branch as the execution evidence trail
- **Standing artifacts** — ADRs, Visions, Designs, etc.
- **Source code** — the actual work product
- **Verification mechanisms** — tests, checks, signoff files

## What lives on the forge

- **Commission record** — the issue body and thread
- **Review communication** — PR comments and inline reviews
- **Cross-PR narrative** — the issue comment thread

## Relationship to existing external evidence

The three-tier model is validated by production-scale patterns in GitHub Agentic Workflows (Microsoft Research / GitHub Next):

- **Plan Command** decomposes issues into sub-issues with objective, files-to-touch, implementation guidance, and ACs — the same structure as the issue layer.
- **ResearchPlanAssignOps** implements a four-phase flow that maps to issue (research → plan) → PR (assign → merge).
- **Causal chain tracking** (Discussion → Issue → PR) preserves attribution across agent handoffs — validating the issue-thread as cross-PR narrative.
- **Sub Issue Closer** closes parent issues when sub-issues complete — implementing the "parent cannot ship until child ships" rule.

The chronicle.md concept has no parallel in any existing system and fills a genuine gap: fine-grained execution records that capture agent reasoning and phase transitions, not just code changes and CI logs.

Empirical evidence from a study of 33k+ agentic PRs (MSR '26) confirms that small, focused PRs succeed at higher rates and that failed agentic PRs share a root cause of poor task decomposition.

## Supersedes

This musing supersedes:
- `docs/musings/cove-pr-as-agent-channel.md` — collapsed strategic and tactical layers into "PR as channel," proposed recursive PR hierarchy contradicted by production evidence
- `docs/musings/issue-pr-chronicle-layers.md` — refined the three tiers but missed branch-primitive model, dispatch vs. CI distinction, escalation altitude, and trove validation