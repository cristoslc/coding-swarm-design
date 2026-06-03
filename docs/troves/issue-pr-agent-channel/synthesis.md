# Synthesis: Issue/PR as Agent Communication Channel

**Trove:** issue-pr-agent-channel
**Created:** 2026-06-03
**Sources:** 5

## Key findings

### 1. GitHub Agentic Workflows is the leading production-scale implementation of the issue→PR→chronicle pattern

The GitHub Agentic Workflows framework (Microsoft Research / GitHub Next) implements the three-tier model at scale in a real repository (github/gh-aw), with 500+ merged agentic PRs across 19+ workflow categories.

| Our model layer | Gh-AW equivalent | Scale evidence |
|---|---|---|
| **Issue** (commission) | Issue with parent/child hierarchy + `/plan` command | Plan Command: 514 merged PRs (67% merge rate) |
| **PR** (work bundle) | PR from Copilot coding agent | Issue Monster dispatches tasks to Copilot one at a time |
| **Chronicle** (execution) | Discussion + issue comments + PR timeline | Causal chain tracking across all phases |

### 2. Sub-issues are the native sub-commission mechanism

Gh-AW's IssueOps and ResearchPlanAssignOps patterns formalize what our musing calls sub-commissions:

- **Parent-child issue hierarchies** are built into the safe-outputs system via the `parent` field and `temporary_id` references
- **The Plan Command workflow** decomposes a parent issue into up to 5 sub-issues, each formatted with "a clear objective, the files to touch, step-by-step implementation guidance, and acceptance criteria"
- **Sub Issue Closer** automatically closes completed sub-issues when the parent issue resolves
- This is the existing production implementation of our "commission stack (issue → sub-issue), not a worktree stack" model

### 3. Causal chain tracking (Discussion → Issue → PR) confirms multi-agent attribution

Gh-AW tracks complete causal chains across agent handoffs. A research agent's discussion becomes a planning agent's issue becomes a coding agent's PR. Attribution is preserved even when different agents perform different phases. Examples:
- Discussion #7631 → Issue #8058 → PR #8110
- Discussion #13934 → Issue #14084 → PR #14129

This validates the model's auditability claim: the issue→PR→chronicle stack provides a complete, queryable trail.

### 4. Addy Osmani independently identifies the issue-as-commission pattern

In "Conductors to Orchestrators" (O'Reilly, Feb 2026), Addy Osmani describes the same pattern from the human engineer's perspective:
- "An engineer can assign tasks to AI agents (e.g., via issues or prompts) and have those agents asynchronously produce code changes — often as ready-to-review pull requests"
- Orchestrator workflows produce "persistent artifacts like branches, commits, and pull requests preserved in version control"
- The key differentiator from conductor-style (single agent) is that orchestrators leave a "paper trail (a Git trail)"

### 5. The arXiv study empirically validates PR decomposition requirements

The empirical study of 33k+ agentic PRs found that failed agentic PRs share a root cause: misalignment with repository workflows and lack of project coordination. The recommended solution — "decompose tasks into localized changes" — is exactly what the issue→PR→chronicle model enforces by design.

### 6. Chronicling remains file-on-disk even in the Gh-AW model

Gh-AW doesn't have a chronicle.md equivalent per se, but it has:
- Discussion posts (research findings)
- Issue comments (cross-PR narrative)
- PR timeline (commit history)

These cover the communication layers, but there's no fine-grained execution record equivalent to chronicle.md for script invocations and phase transitions. This confirms that chronicle.md as a file-on-disk artifact fills a gap that even the most sophisticated forge-based model doesn't cover.

## Points of agreement

All sources converge on:
- **Issues are the commission unit.** Gh-AW, Osmani, and our musing all place the issue as the strategic container for work.
- **PRs are the work bundle unit.** Small, focused, reviewable chunks that fit in a single review cycle.
- **Human gates at transitions.** All models have human decision points: approve the plan before execution, review the PR before merge.
- **Parallelism through issue decomposition, not PR fan-out.** Multiple agents work on different sub-issues, not the same PR.

## Gaps

- **Chronicle as file-on-disk.** None of the external sources address the need for a fine-grained execution record. Gh-AW relies on forge-native records (issue comments, PR timeline). Our chronicle.md concept fills this gap.
- **Phase contracts.** Gh-AW has phases (Research → Plan → Assign → Merge) but they're pipeline phases, not lifecycle phases with enforcement. The commission protocol's eight-phase lifecycle (mission through ship) is more structured.
- **WIP limits.** None of the external sources discuss WIP-limited activation for agent work. Gh-AW uses Issue Monster as a sequencer but doesn't enforce per-altitude limits.