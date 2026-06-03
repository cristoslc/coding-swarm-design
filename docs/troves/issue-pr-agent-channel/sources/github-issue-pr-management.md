# Source: GitHub Agentic Workflows — Issue & PR Management

**URL:** https://github.github.com/gh-aw/blog/2026-01-13-meet-the-workflows-issue-management/
**Author:** Don Syme, Peli de Halleux, Mara Kiefer (Microsoft Research / GitHub Next)
**Published:** 2026-01-13
**Fetched:** 2026-06-03

---

# Meet the Workflows: Issue & PR Management

Jan 13, 2026 — Don Syme, Peli de Halleux, Mara Kiefer

Ah! Let's discuss the art of managing issues and pull requests at Peli's Agent Factory! A most delicious topic indeed!

In our previous post, we explored documentation and content workflows. Now let's talk about the daily rituals of software development: managing issues and pull requests. GitHub provides excellent primitives for collaboration, but there's ceremony involved — linking related issues, merging main into PR branches, assigning work, closing completed sub-issues, optimizing templates. These are small papercuts individually, but they can add up to significant friction.

## Issue & PR Management Workflows

These agents enhance issue and pull request workflows:

- **Issue Arborist** — Links related issues as sub-issues — 77 discussion reports and 18 parent issues created
- **Issue Monster** — Assigns issues to the asynchronous GitHub Copilot coding agent one at a time — task dispatcher for the whole system
- **Mergefest** — Automatically merges main branch into PR branches — orchestrator workflow
- **Sub Issue Closer** — Closes completed sub-issues automatically — orchestrator workflow

The Issue Arborist is an organizational workflow that has created 77 discussion reports and 18 parent issues to group related sub-issues. It keeps the issue tracker organized by automatically linking related issues, building a dependency tree we'd never maintain manually.

The Issue Monster is the task dispatcher — it assigns issues to the GitHub platform's asynchronous Copilot coding agent one at a time. It doesn't create PRs itself, but enables every other agent's work by feeding them tasks. This prevents the chaos of parallel work on the same codebase.

Mergefest is an orchestrator workflow that automatically merges main into PR branches, keeping long-lived PRs up to date without manual intervention. It eliminates the "please merge main" dance.

Sub Issue Closer automatically closes completed sub-issues when their parent issue is resolved, keeping the issue tracker clean.

Issue and PR management workflows don't replace GitHub's features; they enhance them, removing ceremony and making collaboration feel smoother.