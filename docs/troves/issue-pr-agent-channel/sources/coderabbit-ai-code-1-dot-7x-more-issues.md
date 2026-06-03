# Source: Our new report: AI code creates 1.7x more problems

**URL:** https://www.coderabbit.ai/blog/state-of-ai-vs-human-code-generation-report
**Author:** David Loker (CodeRabbit)
**Published:** 2025-12-17
**Fetched:** 2026-06-03

---

# Our new report: AI code creates 1.7x more problems

by David Loker — December 17, 2025 — 8 min read

*What we learned from analyzing hundreds of open-source pull requests.*

Over the past year, AI coding assistants have gone from emerging tools to everyday fixtures in the development workflow. At many organizations, a part of every code change is now machine-generated or machine-assisted.

But while this has been accelerating the speed of development, questions have been quietly circulating:

- Why are more defects slipping through into staging?
- Why do certain logic or configuration issues keep appearing?
- And are these patterns tied to AI-generated code?

It would appear like AI is playing a significant role. A recent report found that while pull requests per author increased by 20% year-over-year, thanks to help from AI, incidents per pull request increased by 23.5%.

This year also brought several high-visibility incidents, postmortems, and anecdotal stories pointing to AI-written changes as a contributing factor. These weren't fringe cases or misuses. They involved otherwise normal pull requests that simply embedded subtle mistakes. And yet, despite rapid adoption of AI coding tools, there has been surprisingly little concrete data about how AI-authored PRs differ in quality from human-written ones.

So, CodeRabbit set out to answer that question empirically in our State of AI vs Human Code Generation Report.

## Our State of AI vs Human Code Generation Report

We analyzed 470 open-source GitHub pull requests, including 320 AI-co-authored PRs and 150 human-only PRs, using CodeRabbit's structured issue taxonomy. Every finding was normalized to issues per 100 PRs and we used statistical rate ratios to compare how often different types of problems appeared in each group.

The results? Clear, measurable, and consistent with what many developers have been feeling intuitively: AI accelerates output, but it also amplifies certain categories of mistakes.

### Limitations of our study

Getting data on issues that are more prevalent in AI-authored PRs is critical for engineering teams but the challenge was determining which PRs were AI-authored vs human authored. Since it was impossible to directly confirm authorship of each PR of a large enough OSS dataset, we checked for signals that a PR was co-authored by AI and assumed that those that didn't have it were human authored, for the purposes of the study.

This resulted in statistically significant differences in issue patterns between the two datasets, which we are sharing in this study so teams can better know what to look for. However, we cannot guarantee all the PRs we labelled as human authored were actually authored only by humans.

## Top 10 findings from the report

No issue category was uniquely AI but most categories saw significantly more errors in AI-authored PRs. That means, humans and AI make the same kinds of mistakes. AI just makes many of them more often and at a larger scale.

1. AI-generated PRs contained ~1.7x more issues overall. Across 470 PRs, AI-authored changes produced 10.83 issues per PR, compared to 6.45 for human-only PRs. Even more striking: high-issue outliers were much more common in AI PRs, creating heavy review workloads.

2. Severity escalates with AI: More critical and major issues. AI PRs show ~1.4-1.7x more critical and major findings.

3. Logic and correctness issues were 75% more common in AI PRs. These include business logic mistakes, incorrect dependencies, flawed control flow, and misconfigurations. Logic errors are among the most expensive to fix and most likely to cause downstream incidents.

4. Readability issues spiked more than 3x in AI contributions. The single biggest difference across the entire dataset was in readability. AI-produced code often looks consistent but violates local patterns around naming, clarity, and structure.

5. Error handling and exception-path gaps were nearly 2x more common. AI-generated code often omits null checks, early returns, guardrails, and comprehensive exception logic, issues tightly tied to real-world outages.

6. Security issues were up to 2.74x higher. The most prominent pattern involved improper password handling and insecure object references. While no vulnerability type was unique to AI, nearly all were amplified.

7. Performance regressions, though small in number, skewed heavily toward AI. Excessive I/O operations were ~8x more common in AI-authored PRs. This reflects AI's tendency to favor clarity and simple patterns over resource efficiency.

8. Concurrency and dependency correctness saw ~2x increases. Incorrect ordering, faulty dependency flow, or misuse of concurrency primitives appeared far more frequently in AI PRs.

9. Formatting problems were 2.66x more common in AI PRs. Even teams with formatters and linters saw elevated noise: spacing, indentation, structural inconsistencies, and style drift.

10. AI introduced nearly 2x more naming inconsistencies. Unclear naming, mismatched terminology, and generic identifiers appeared frequently in AI-generated changes, increasing cognitive load for reviewers.

## Why these patterns appear

- AI lacks local business logic: Models infer code patterns statistically, not semantically. Without strict constraints, they miss the rules of the system that senior engineers internalize.
- AI generates surface-level correctness: It produces code that looks right but may skip control-flow protections or misuse dependency ordering.
- AI doesn't adhere perfectly to repo idioms: Naming patterns, architectural norms, and formatting conventions often drift toward generic defaults.
- Security patterns degrade without explicit prompts: Unless guarded, models recreate legacy patterns or outdated practices found in older training data.
- AI favors clarity over efficiency: Models often default to simple loops, repeated I/O, or unoptimized data structures.

## What engineering teams can do about it

Adopting AI coding tools isn't simply about speeding up development. It requires rethinking the guardrails that ensure all code entering production is safe, maintainable, and correct.

1. Give AI the context it needs — prompt snippets, repo-specific instruction capsules, configuration schemas.
2. Use policy-as-code to enforce style — CI-enforced formatters, linters, and style guides.
3. Add correctness safety rails — require tests for non-trivial control flow, mandate nullability/type assertions.
4. Strengthen security defaults — centralize credential handling, run SAST and security linters.
5. Nudge the model toward efficient patterns — guidelines for batching I/O, choosing appropriate data structures.
6. Adopt AI-aware PR checklists — reviewers should ask about error paths, concurrency primitives, configuration validation.
7. Get help reviewing and testing AI code — CodeRabbit helps by standardizing code reviews.

## The bottom line

AI coding tools are powerful accelerators, but acceleration without guardrails increases risk. Our analysis shows that AI-generated code is consistently more variable, more error-prone, and more likely to introduce high-severity issues without the right protections in place.

The future of AI-assisted development isn't about replacing developers. It's about building systems, workflows, and safety layers that amplify what AI does well while compensating for what it tends to miss.