# Musing: Issue, PR, and chronicle as three-tier communication layers

## The idea

Inter-agent communication in a swarm operates at three distinct altitudes, each with its own forge primitive and record granularity:

| Layer | Primitive | Scope | Records | Drives |
|-------|-----------|-------|---------|--------|
| Strategic | **Issue** | Commission (entire feature/epic) | Mission, ACs, operator gates, cross-PR narrative | Why we're doing this |
| Tactical | **PR** | Work bundle (one implementation chunk) | Approach decisions, review findings, technical discussions | How we're doing this |
| Execution | **chronicle.md** (file on branch) | Implementation session | Phase transitions, exact timestamps, script invocations, tool outputs | What happened |

The layers are composable: a single issue can spawn many PRs, each with its own chronicle. Sub-commissions are related issues, linked from the parent issue. This refines the earlier musing (`cove-pr-as-agent-channel.md`), which collapsed strategic and tactical layers into "PR as channel."

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
├─ PR #272.1 — "Migrate remaining components (charts, tables, forms)"
│  chronicle.md: continuation chunk, fix edge cases
│  (split because PR #272 grew too large mid-implementation)
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

## Layer responsibilities in detail

### Issue layer (strategic)

The issue body IS the canonical commission record. It contains:

- **Mission**: one-paragraph outcome statement
- **Acceptance criteria**: falsifiable, at-commission-altitude
- **Plan**: phased around gates, referencing PRs as work bundles
- **Why this exists**: context and rationale

The issue comment thread is the cross-PR narrative. It records:
- Phase transitions (crawl-self result, operator approvals at gates)
- Sub-commission spawn/receive events
- Links to PRs as they're filed and completed
- Final ship summary when the commission closes

Issue comments are the **most aerial** layer — they are the operator-facing summary of what happened across all PRs.

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

PR comments are **mid-aerial** — they are the reviewer/implementer-facing layer. Not every execution detail belongs here; only approach-level and review-level communication.

### chronicle.md layer (execution)

The chronicle file lives in the worktree, committed on the PR branch. It records:
- Every phase transition (mission → crawl-self → crawl-context → ... → ship)
- Every script invocation (phase.py, crawl-self.py, wip-check.py, verify.py)
- Timestamps for each event
- Agent's tactical decisions that don't warrant PR-level discussion

chronicle.md is the **finest-grained** layer — it's the evidence trail for what exactly happened during execution. It is NOT a communication surface (no threading, no replies). It is a record.

## Sub-commissions as related issues

A sub-commission is a commission whose execution is delegated to a separate agent/scope. In this model, that means:

1. **Parent issue thread records the spawn**: agent writes a comment describing why a sub-commission is needed, referencing the acceptance criterion that demands it.

2. **Sub-commission gets its own issue**: the child issue has `parent=Issue #N` in its metadata, linking it back.

3. **Sub-commission has its own PRs**: the child commission may itself have multiple work bundles.

4. **Sub-commission reports back to parent**: when the child ships, its final thread comment links to the result, and the parent thread records receipt.

5. **Parent cannot ship until child ships**: if the child was spawned to satisfy an acceptance criterion, that criterion is blocked until the child completes.

The hierarchy is a **commission stack** (issue → sub-issue), not a worktree stack. All PRs are against trunk. There is no recursive PR hierarchy — the distance-from-trunk concept from the earlier musing is replaced by issue-relatedness.

## PR splitting mid-execution

A natural consequence of work bundles: sometimes a PR grows too large during implementation. The agent can split it by:
1. Creating a related PR (e.g., PR #272.1) from the same worktree branch
2. Continuing with the remaining scope in the new PR
3. Noting the split in the issue thread for operator awareness

This is not a sub-commission — it's tactical chunking within the same commission scope.

## Comparison with the file-based commission model

In swain-v2's current file-based protocol:

| swain-v2 file | This model's equivalent |
|---|---|
| `docs/commissions/COMMISSION-NNN/commission.md` | Issue body |
| `evidence/chronicle.md` | chronicle.md (per-PR, on branch) |
| `evidence/insights.md` | PR comments (discoveries surfaced during implementation) |
| Parent `commission.md` frontmatter (`parent-commission:`) | Issue links (`parent=Issue #N`) |
| Sub-commission directory under `docs/commissions/proposed/` | Related issue |
| Gate approvals (operator signoff in files) | Issue comments (operator reply) |
| Standing artifacts at `docs/standing/` | Files in the repo (unchanged) |

The key difference: the forge (issue + PR) replaces the custom file scaffolding for communication, while chronicle.md remains as the execution-level record that files-on-disk are canonical for.

## What stays file-on-disk

- **chronicle.md** — committed on the PR branch as the execution evidence trail
- **Standing artifacts** — ADRs, Visions, Designs, etc. (the durable outputs that outlive the commission)
- **Source code** — the actual work product
- **Verification mechanisms** — tests, checks, signoff files (they need to be on disk to run)

## What lives on the forge

- **Commission record** — the issue body and thread (replaces `commission.md`)
- **Review communication** — PR comments and inline reviews (replaces `docs/ai-code-reviews/`)
- **Cross-PR narrative** — the issue comment thread (replaces manual file‑based chronicle aggregation)

## Chronicling conventions

chronicle.md and the PR commit timeline are complementary, not redundant. A commit says "added palette definitions." A chronicle entry says "crawl-context completed — discovered 37 hardcoded colors in 14 components." One captures code history; the other captures agent reasoning and phase transitions. Both are valuable.

The authoring convention: when an agent pushes a chronicle commit, the commit message is prefixed with `[chronicle]` so the forge PR timeline shows chronicle entries distinctly from code changes. This makes the chronicle visible in the PR timeline without migrating it off disk.

swain-v2's `phase.py` already auto-appends chronicle rows on phase transitions. That automation carries forward — it writes to `chronicle.md` on the worktree branch. The file is the same; its location shifts from `docs/commissions/COMMISSION-NNN/evidence/` to the PR branch root.