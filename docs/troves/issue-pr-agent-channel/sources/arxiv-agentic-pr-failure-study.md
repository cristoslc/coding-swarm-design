# Source: Where Do AI Coding Agents Fail? — An Empirical Study of Failed Agentic Pull Requests in GitHub

**URL:** https://arxiv.org/html/2601.15195v1
**Authors:** Ramtin Ehsani, Sakshi Pathak, Shriya Rawal, Abdullah Al Mujahid, Mia Mohammad Imran, Preetha Chatterjee
**Published:** 2026-01 (MSR '26: Proceedings of the 23rd International Conference on Mining Software Repositories)
**Fetched:** 2026-06-03

---

# Where Do AI Coding Agents Fail? An Empirical Study of Failed Agentic Pull Requests in GitHub

## Abstract

AI coding agents are now submitting pull requests (PRs) to software projects, acting not just as assistants but as autonomous contributors. As these agentic contributions are rapidly increasing across real repositories, little is known about how they behave in practice and why many of them fail to be merged. In this paper, we conduct a large-scale study of 33k agent-authored PRs made by five coding agents across GitHub. (RQ1) We first quantitatively characterize merged and not-merged PRs along four broad dimensions: 1) merge outcomes across task types, 2) code changes, 3) CI build results, and 4) review dynamics. We observe that tasks related to documentation, CI, and build update achieve the highest merge success, whereas performance and bug-fix tasks perform the worst. Not-merged PRs tend to involve larger code changes, touch more files, and often do not pass the project's CI/CD pipeline validation. (RQ2) To further investigate why some agentic PRs are not merged, we qualitatively analyze 600 PRs to derive a hierarchical taxonomy of rejection patterns. This analysis complements the quantitative findings in RQ1 by uncovering rejection reasons not captured by quantitative metrics, including lack of meaningful reviewer engagement, duplicate PRs, unwanted feature implementations, and agent misalignment. Together, our findings highlight key socio-technical and human-AI collaboration factors that are critical to improving the success of future agentic workflows.

## 1. Introduction

AI coding agents such as GitHub Copilot and OpenAI Codex are rapidly becoming active contributors to open-source repositories, often assisting with or directly authoring new pull requests (PRs). Beyond offering inline code suggestions, these tools now generate code changes, respond to reviewer feedback, and participate in the software lifecycle as autonomous agents.

Prior work shows that PR acceptance depends on factors such as technical correctness, problem scope, and contributor reputation. PRs are more likely to be merged when they pass tests and CI pipelines, address high-priority or well-scoped problems, and introduce localized and incremental code changes rather than broad or invasive modifications. While these factors characterize the success of human-authored PRs, their relevance and applicability to agent-authored PRs are not yet well understood.

While prior work evaluates agents in isolated tasks, we lack a systematic assessment of how agents perform when integrated into real development workflows involving CI validation, code review, and iterative revision. In this paper, we conduct a large-scale empirical study on agent-authored pull requests using the AIDev-pop dataset, which comprises over 33k PRs submitted by five major coding agents across GitHub projects with more than 100 stars.

## 3. Results

### RQ1: Quantitative characterization

Of the 33,596 agentic pull requests, the majority originate from OpenAI Codex (21,799). Copilot and Devin follow with 4,970 and 4,827 PRs, while Cursor contributes 1,541, and Claude Code represents the smallest share with 459 PRs.

Across all agents, 71.48% of PRs (24,014) are successfully merged. Despite handling the largest volume of contributions, OpenAI Codex achieves the highest merge rate, with 82.59% (18,004) of its PRs being accepted. Cursor follows with a 65.22% merge rate (1,005), then Claude Code at 59.04% (271), and Devin at 53.76% (2,595). Copilot exhibits the lowest merge rate, with only 43.04% (2,139) of its PRs being merged.

Across agents, tasks with consistently higher merge rates include documentations (84%), CI (79%), and build (74%). In contrast, performance (55%) and fix (64%) display the lowest merge rates overall. Not merged PRs tend to introduce larger modifications in the number of files and LOC than merged ones. Not-merged PRs show a noticeably heavier tail of CI failures. Each additional failed CI check decreases the odds of a merge by about 15%.

### RQ2: Rejection taxonomy

Reviewer-level abandonment is the most frequent rejection pattern, accounting for 228 PRs (38%) — PRs closed with no meaningful human interaction.

Pull request-level reasons form the second-largest group, comprising 188 PRs (31%):
- Duplicate PRs: 142 PRs (23%)
- Unwanted features: 24 PRs (4%)
- Non-functional PRs: 13 PRs (2%)
- Wrong task description: 7 PRs (1%)
- Wrong branch: 2 PRs (<1%)

Code-level reasons: 133 PRs (22%):
- CI/test failure: 99 PRs (17%)
- Incorrect implementation: 19 PRs (3%)
- Incomplete implementation: 15 PRs (2%)

Agentic-level issues: 13 PRs (2%):
- Misalignment (agent fails to follow reviewer instructions): 9 PRs (1%)
- License issues: 4 PRs (<1%)

## 4. Conclusion

Maintainer feedback frequently emphasizes that PRs should be small, focused, and limited to a single coherent change, and discourages agentic submissions that combine substantive modifications with unrelated edits.

Improving the success of future agentic-AI workflows would require improving agents' ability to identify existing or ongoing work, adhere to project contribution norms, decompose tasks into localized changes, and validate submissions against CI pipelines before opening new PRs.