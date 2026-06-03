# Musing: Cove PR as inter-agent communication channel

## The idea

Replace file-based inter-agent communication (plan docs, review artifacts, todo files) with Cove/Forgejo PRs. The PR — already a native forge primitive — becomes the durable, threaded record of intent for a multi-agent work session.

## Flow

1. **Dispatch agent** creates a worktree from an issue, then immediately files a **draft PR** toward the target branch. The PR description contains the commission: what to do, constraints, acceptance criteria.

2. **Working agent** documents its plan as **PR comments** (top-level thread). Each major decision, approach choice, or implementation step gets its own comment thread.

3. **Code review agents** document findings as **PR comments** — inline on diffs when code exists, or as review threads on the plan comments.

4. **Working agent** responds to review findings by posting **plan comments** describing how it will address each item. Optionally, a **review gate agent** signs off on the plan before the working agent begins implementation.

5. The working agent pushes commits to the PR branch. Review agents re-evaluate on new commits. The cycle repeats until the PR is approved and merged.

6. The PR is **closed** (merge is one form of close). The **merged/closed PR** becomes a permanent, queryable artifact linking commission → plan → review → implementation → merge.

## Advantages over file-based communication

| Aspect | Files in repo | PR as channel |
|--------|--------------|---------------|
| Persistence | Git history (buried) | Forge UI, searchable, filterable |
| Threading | Manual file naming | Native comment threads, replies |
| Review | Separate review artifact | Native PR review with inline comments |
| Notification | Polling or webhook setup | Forge-native notifications, CI triggers |
| Access control | Git permissions | PR-level visibility, requested reviewers |
| State machine | Custom (file names, status files) | Draft → Open → Changes requested → Approved → Merged |
| Cross-agent visibility | Agent must read files from other worktree | All agents see the same PR |
| Audit trail | Diff of plan files | Full PR timeline with comments, reviews, commits |

## Key protocol considerations

- **Comment format**: Agents prefix comments with structured headers (e.g., `## Plan`, `## Review`, `## Response`), so other agents and humans can parse role/section at a glance.

- **PR description as commission**: Structured sections: `Objective`, `Constraints`, `Acceptance Criteria`, `Referenced Issues`.

- **Phase locking**: Only one agent works at a time within a given phase. Agents use a convention — a "status" comment updated in place: `Status: Planning`, `Status: Awaiting Review`, `Status: Implementing`, `Status: Ready`.

- **PR title/branch convention**: Branch name from the worktree name. PR title includes the issue reference (if any) and a concise purpose statement.

- **CI/CD integration**: Forge CI reacts to PR label changes to trigger agent invocations (e.g., `agent/ready-for-review` triggers review agent; `agent/plan-approved` triggers implementation).

## Lifecycle: worktree cleanup on PR close

The durable record is the PR, not the worktree. The worktree is ephemeral. Cleanup triggers on **PR close** (not merge), because close covers all terminal states uniformly:

| Terminal state | Close trigger | Worktree action |
|---|---|---|
| Completion (merged) | PR merged, then closed | Clean up |
| Abandonment (stale, no progress) | PR closed without merge | Clean up |
| Supersession (replaced by another PR) | PR closed as superseded | Clean up |

A single signal — PR closed → clean up worktree — avoids ambiguity.

## Error vs. Abandon

These are distinct states that the musing originally conflated:

- **Error**: The agent hit something it cannot resolve. It posts a final comment describing the failure and why, then escalates (flags a human operator or supervisor agent). The PR is NOT closed — it stays open with label `agent/escalated` so a human or supervisor can pick up from the PR state.

- **Abandon**: No progress, stale lock, agent disappeared. The PR is closed without merge. The worktree is force-cleaned.

Escalation preserves the thread so a responder can read the full conversation. Abandonment is a silent cleanup.

## Recursive PR hierarchy and distance from trunk

The most interesting implication. Agents have a **distance from trunk** — a count of how many PRs deep they are. This creates a natural hierarchy:

```
trunk
  └─ PR-1 ← secondary agent (distance 1)
       └─ PR-2 ← tertiary agent (distance 2)
            └─ PR-3 ← quaternary agent (distance 3)
```

Rules by distance:

| Distance | Writes to | Creates PRs against |
|---|---|---|
| 0 (prime/dispatch) | trunk | — |
| 1 (working agent) | its worktree | trunk |
| 2+ (sub-agents) | their worktree | the worktree one level up |

A secondary agent that needs to parallelize creates its **own** worktree + PR off **its** branch, becoming prime on that sub-branch. Its subagents (distance 2+ from trunk) PR against that branch, not trunk.

```
trunk ← PR-1 ← worktree-A (distance-1 agent)
              └─ PR-2 ← worktree-B (distance-2 agent, parallelizes within PR-1's scope)
```

Each PR is a scoped communication channel between adjacent levels. A distance-2 agent never sees PR-1. The distance-1 agent is the sole bridge between trunk and the sub-branch.

This also answers open question 4 (multiple working agents): **v1 uses sequential work within a single PR**, no fan-out within the PR. Parallelization happens by creating many PRs at the same distance, each scoped to its own commission. Recursive PR creation is the escape hatch when a distance-1 agent itself needs to parallelize.

## Relationship to the dispatch skill

The dispatch skill (`dispatch-opencode`) and the PR-as-channel protocol are **orthogonal**, not competing:

| Concern | Dispatch skill | PR channel |
|---|---|---|
| What it handles | Execution lifecycle | Communication lifecycle |
| Mechanism | Lockfiles, polling, worktree isolation | Forge PR comments, reviews, state machine |
| Scope | Spawn → monitor → timeout → cleanup subagents | Document intent → receive feedback → iterate plan |
| Visibility | Agent-only (`.subagents/` is gitignored) | All agents + humans (forge UI) |

A working agent might use the dispatch skill internally (to parallelize implementation subtasks) while using the PR to communicate upward with the dispatcher. The PR replaces lockfiles *for communication*, not for execution lifecycle management.

## Recursive depth: guidelines not hard cap

No hard limit, but strong preference:

- **Typical case**: distance = 1 (trunk → working branch). One PR per commission.
- **Maximum practical**: distance = 3. Beyond that, traceability degrades sharply.
- **If you need depth > 3**: the original PR scope is probably too broad. Split the commission.