# Source: GitHub Infrastructure Buckles Under AI Agent Commits

**URL:** https://zenvanriel.com/ai-engineer-blog/github-ai-agent-commits-infrastructure-crisis/
**Author:** Zen van Riel (Senior AI Engineer, ex-Microsoft, ex-GitHub)
**Published:** 2026-06
**Fetched:** 2026-06-03

---

# GitHub Infrastructure Buckles Under AI Agent Commits

While AI engineers celebrate productivity gains from coding agents, a sobering reality is emerging: the platforms we depend on were never built for this. GitHub logged five major incidents in the first two days of April as AI coding agents overwhelmed infrastructure designed for human developers. The numbers reveal the scale of the problem.

GitHub processed 1 billion commits in all of 2025. Now it handles 275 million commits every single week. That trajectory puts 2026 on track for 14 billion commits, a 14x increase year over year. Every major AI coding tool, from Cursor to Claude Code to Devin, routes its output straight into GitHub.

| Metric | Before AI Agents | April 2026 |
|---|---|---|
| Weekly commits | ~19 million | 275 million |
| AI agent PRs (monthly) | 4 million (Sept 2025) | 17 million |
| GitHub Actions minutes/week | 500 million (2023) | 2.1 billion |
| Claude Code commits/week | ~100,000 | 2.6 million |

The platform that underpins nearly every software team's workflow is showing visible strain. This affects every AI engineer who ships code through GitHub.

## What Actually Broke in April

The first week of April exposed the fragility. On April 1 and 2, GitHub experienced five separate incidents that degraded core services. Copilot's backend exhausted resources, causing a 2.7 hour outage. Code search went down for 8.7 hours. The Copilot Cloud Agent degraded for four hours due to emergency rate limiting.

A week later, conditions worsened. Between April 9 and 13, agent sessions peaked at 54 minute wait times compared to the normal 15 to 40 seconds. Approximately 84% of requests to start agent sessions failed during peak load, briefly spiking to 97.5%. A caching bug compounded the problem by persisting rate limited states beyond the actual limit window, creating recurring outage waves rather than single recovery events.

GitHub COO Kyle Daigle acknowledged the shift: "There were 1 billion commits in 2025. Now, it's 275 million per week."

The infrastructure was sized for human scale usage. Autonomous agent fleets operating simultaneously across thousands of repositories represent an entirely different traffic pattern.

## The Real Scale of AI Generated Code

Claude Code alone now accounts for 4.5% of all public commits on GitHub, generating 2.6 million commits weekly. That represents a 25x increase from roughly 100,000 weekly commits in late September 2025.

Pull requests from AI agents jumped from 4 million in September to 17 million in March, a 325% increase in six months. Each PR triggers CI runs, webhook events, code review bots, and often more agent activity downstream. The multiplication effect strains every layer of the infrastructure stack.

GitHub Actions compute usage tells the same story. Weekly usage jumped from 500 million minutes in 2023 to 1 billion in 2025, then exploded to 2.1 billion minutes in a single week in early 2026.

## Quality Concerns Beyond Infrastructure

Xavier Portilla Edo, a prominent open source maintainer, reported that "only 1 out of 10 PRs created with AI is legitimate." The other 90% generate noise requiring maintainer review effort. This creates a multiplicative burden. Not only do AI agents flood the system with volume, but human maintainers must spend cycles filtering low quality contributions.

GitHub evaluated several "kill switch" options including disabling PRs for opted in repos, restricting submissions to collaborators only, implementing AI triage filters, and mandatory attribution requirements. None have been implemented yet, but the discussion signals that fundamental changes to open source contribution models may be coming.

## Practical Implications for AI Engineers

Rate limiting will become more aggressive. Wait times for CI/CD pipelines will increase during peak usage. Agent session reliability will fluctuate as GitHub experiments with traffic management.

Mitigation strategies include: running CI pipelines during off peak hours, implementing local validation before pushing, batching commits strategically, and monitoring GitHub status actively.

## What This Signals for the Industry

ChatGPT experienced a major outage on April 20 that affected projects and deleted ongoing work. Anthropic acknowledged "inevitable strain" on infrastructure that impacted reliability and performance.

The AI infrastructure layer is buckling across the industry. The irony is not lost: the tools accelerating software development are simultaneously threatening the stability of the platforms required to ship software.