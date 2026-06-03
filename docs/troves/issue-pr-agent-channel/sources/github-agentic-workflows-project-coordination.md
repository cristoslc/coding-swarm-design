# Source: GitHub Agentic Workflows — Project Coordination

**URL:** https://github.github.com/gh-aw/blog/2026-01-13-meet-the-workflows-campaigns/
**Author:** Don Syme, Peli de Halleux, Mara Kiefer (Microsoft Research / GitHub Next)
**Published:** 2026-01-13
**Fetched:** 2026-06-03

---

Meet the Workflows: Project Coordination | GitHub Agentic Workflows

[Skip to main content]

# Meet the Workflows: Project Coordination

Jan 13, 2026 — Don Syme, Peli de Halleux, Mara Kiefer

My dear friends, we've arrived at the *grand finale* — the most spectacular room of all in Peli's Agent Factory!

We've journeyed through 18 categories of workflows — from triage bots to code quality improvers, from security guards to creative poets, culminating in advanced analytics that use machine learning to understand agent behavior patterns. Each workflow handles its individual task admirably.

But here's the ultimate challenge: how do you coordinate *multiple* agents working toward a shared goal? How do you break down a large initiative like "migrate all workflows to a new engine" into trackable sub-tasks that different agents can tackle? How do you monitor progress, alert on delays, and ensure the whole is greater than the sum of its parts? This final post explores planning, task-decomposition and project coordination workflows — the orchestration layer that proves AI agents can handle not just individual tasks, but entire structured projects requiring careful coordination and progress tracking.

## Planning & Project Coordination Workflows

These agents coordinate multi-agent plans and projects:

- **Plan Command** — Breaks down issues into actionable sub-tasks via /plan command — **514 merged PRs out of 761 proposed (67% merge rate)**
- **Discussion Task Miner** — Extracts actionable tasks from discussion threads — **60 merged PRs out of 105 proposed (57% merge rate)**

Plan Command has contributed **514 merged PRs out of 761 proposed (67% merge rate)**, providing on-demand task decomposition that breaks complex issues into actionable sub-tasks. This is the **highest-volume workflow by attribution** in the entire factory. Developers can comment /plan on any issue to get an AI-generated breakdown into actionable sub-issues that agents can work on. A verified example causal chain: Discussion #7631 → Issue #8058 → PR #8110.

Discussion Task Miner has contributed **60 merged PRs out of 105 proposed (57% merge rate)**, continuously scanning discussions to extract actionable tasks that might otherwise be lost. The workflow demonstrates perfect causal chain attribution: when it creates an issue from a discussion, and Copilot Coding Assistant later fixes that issue, the resulting PR is correctly attributed to Discussion Task Miner.

We learned that individual agents are great at focused tasks, but orchestrating multiple agents toward a shared goal requires careful architecture. Project coordination isn't just about breaking down work — it's about discovering work (Task Miner), planning work (Plan Command), and tracking work (Workflow Health Manager). These workflows implement patterns like epic issues, progress tracking, and deadline management. They prove that AI agents can handle not just individual tasks, but entire projects when given proper coordination infrastructure.

## What We've Learned

The key insight? **AI agents are most powerful when they're specialized, well-coordinated, and designed for their specific context.** No single agent does everything — instead, we have an ecosystem where each agent excels at its particular job, and they work together through careful orchestration.

We've learned that observability is essential, that incremental progress beats heroic efforts, that security needs careful boundaries, and that even "fun" workflows can drive meaningful engagement. We've discovered that AI agents can maintain documentation, manage campaigns, analyze their own behavior, and continuously improve codebases — when given the right architecture and guardrails.

As you build your own agentic workflows, remember: start small, measure everything, iterate based on real usage, and don't be afraid to experiment. The workflows we've shown you evolved through experimentation and real-world use. Yours will too.