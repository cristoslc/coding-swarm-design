# Musing: Swain's architecture — commission, substrates, conformance, and core goals

## Swain is the union of commission + substrates

Swain is not just the commission protocol (v2's conceptual contribution) and not just a runtime (v1's artifacts-plus-skills model). It is the combination of both:

```
swain = commission_protocol × substrates
```

The **commission protocol** is the conceptual architecture: mission → crawl-self → define-done → plan → approve → execute → verify → ship. The phases, the altitude hierarchy, WIP limits, verification mechanisms — this is the invariant that holds across all implementations.

**Substrates** are the concrete carriers that implement the protocol. Multiple substrates can coexist, each suited to different contexts:

| Substrate | What it enforces | Best for |
|-----------|-----------------|----------|
| Filesystem (swain-v2 current) | Phase transitions via scripts (phase.py), frontmatter validation, WIP checks | Single-operator, no external infrastructure, transitional state |
| Forge (issue/PR/chronicle model) | Issue body as commission, PR as work bundle, comments as chronicle | Multi-agent, team-visible, requires forge platform |
| SQLite / runtime DB | Structured queries, enforcement at the tool-call level | When structured querying and deterministic enforcement outweigh context cost |
| Hybrid | Some phases on filesystem, some on forge, some in code | Gradual migration between substrates |

The commission protocol is substrate-agnostic. The choice of substrate is an operational decision, not a protocol decision. An ADR about substrate selection describes *which* substrate to use, not *how* commissions work.

This reframes earlier musings: the issue/PR/chronicle model is one substrate, not the protocol itself.

## ADR design vs. ADR conformance

An ADR makes a design decision. It records what was decided, why, and what the consequences are.

But the ADR's level of specification determines what "conformance" means. There are two distinct questions:

1. **ADR design** — Does the ADR describe the architectural intent correctly? Is the decision sound? This is a human-review question evaluated at the ADR's authoring gate.

2. **ADR conformance** — Does the current system actually follow the ADR's prescriptions? This is a verification question. It needs an active mechanism.

The boundary between design and conformance is the level of specificity in the ADR:

| ADR specificity level | Design (human gate) | Conformance (verification) |
|-----------------------|---------------------|---------------------------|
| High (concrete): "The API gateway MUST validate JWTs from issuer X before forwarding to service Y" | Reviewed once at authoring | Automated — a CI test or policy check can verify this |
| Medium: "Services MUST authenticate via mTLS" | Reviewed once at authoring | Partially automatable — CI can check TLS config, but not every edge case |
| Low (abstract): "The system should favor eventual consistency" | Reviewed once at authoring | Not directly automatable — requires LLM-driven verifier or structured signoff |

**The open question**: at what level of specificity should an ADR be written to maximize automated conformance checking without over-constraining the implementation? Over-specific ADRs constrain implementors; under-specific ADRs produce unverifiable prescriptions.

Tentative guideline: an ADR should be specific enough that at least one acceptance criterion in its producing commission's define-done can verify conformance. If no criterion can verify it, the ADR is too abstract to be useful as a standing artifact.

## When conformance checks run

Conformance checks operate at multiple points in the lifecycle:

| Checkpoint | What it checks | Who runs it |
|------------|---------------|-------------|
| **Commission verify phase** | Does the implementation satisfy the ADR's prescriptions? | Agent (via verify.py, tests) |
| **PR merge gate** | Does this change violate any active ADR? | CI pipeline (lint, SAST, policy-as-code) |
| **On-commit (continuous)** | Does the system still conform? | Scheduled CI / cron |
| **On incident** | Did a production issue reveal ADR drift? | Human (postmortem) |

The commission's verify phase checks conformance at the time of delivery. The continuous check catches drift after the fact. Both are needed: delivery-time catches before it ships; continuous catches after things change.

The earlier musing's "chronicle" file is one mechanism for the verify phase — it records phase transitions and script invocations that prove conformance at a fine-grained level. But conformance verification itself happens in the verify phase, not in the chronicle.

## Core goals and how they constrain the model

The core goal of the entire swain system is: **ship reliable, working software as quickly as possible.**

This implies several non-negotiable design constraints:

### Incrementalism over big-batch work
Small, frequent merges beat large, infrequent ones. The arXiv study of 33k+ agentic PRs confirms this empirically: large PRs fail more. The PR-as-channel substrate must enforce small work bundles. A PR that grows too large should be abandoned and re-scoped (as the communication musing already prescribes).

### Trunk-based (or near trunk-based) development
Long-lived branches accumulate drift. "Near trunk-based" means short-lived feature branches that merge within hours or days, not weeks. The commission protocol's WIP limits and phase gates serve this — they prevent too many in-flight work items. The substrate should actively discourage long-lived branches.

### Rigorous test suites
Every code change must pass verification before merging. The commission's verify phase is the protocol-level mechanism. The substrate's CI pipeline is the implementation-level mechanism. They must agree on what "passes" means.

### Feature toggles over branch-based isolation
Toggle-switch new work behind flags rather than holding it on a branch until complete. This enables trunk-based development even for multi-PR features. The issue layer (commission) tracks the toggle lifecycle; the PR layer toggles individual work bundles behind the same flag.

### Review velocity must match generation velocity
This is the critical bottleneck the counterevidence trove identifies. The commission protocol addresses this through WIP limits (limit how much work enters the pipeline). The substrate addresses this through structured review gates (CI checks, automated quality gates, review agent assignments). If review velocity doesn't keep pace, no amount of protocol refinement helps — the substrate must compensate.

### Traceability without ceremony
Every change must be linkable to its originating commission (issue), work bundle (PR), and execution record (chronicle). But the system must not require agents to manually maintain these links — they should be structural properties of the substrate.

## Relationship to existing musings

The earlier musing (`issue-pr-chronicle-agent-communication.md`) describes one substrate (forge-based) in detail. This musing provides the architectural framing that substrate fits into. The communication musing is about *how* agents talk to each other on a forge substrate; this musing is about *what* swain is and what it optimizes for, independent of substrate choice.

The earlier musing's three-tier model (issue → PR → chronicle) is one valid substrate implementation. The core goals (incrementalism, trunk-based dev, feature toggles, review velocity) define the constraints any substrate must satisfy.