# Source: Why Open Source Maintainers Are Rejecting AI-Generated Pull Requests

**URL:** https://pickuma.com/posts/open-source-maintainers-rejecting-ai-pull-requests/
**Author:** Owen (Pickuma)
**Published:** 2026-05-21
**Fetched:** 2026-06-03

---

# Why Open Source Maintainers Are Rejecting AI-Generated Pull Requests

A wave of AI-generated PRs and hallucinated bug reports is straining open source maintenance. Here is what is driving the backlash, real project policies, and what responsible AI-assisted contribution looks like.

Owen — Editor — 2026-05-21 · 7 min read

The pull request is the basic unit of open source trust. You read the codebase, you understand the problem, you write a fix, and you hand it to a stranger on the internet — implicitly vouching that it is correct, tested, and worth their time to review. That social contract held for thirty years. AI coding agents are straining it in ways that have pushed some of the most prominent projects in the ecosystem to take defensive measures they never anticipated needing.

This is not a story about AI being bad at code. In controlled settings, AI-generated code can be perfectly functional. The problem is structural: agents generate PRs and bug reports at a rate that no volunteer review process can absorb, and the cost of evaluating a plausible-but-wrong contribution is almost always higher than the cost of creating it. That asymmetry has broken something.

## The Specific Problems Maintainers Are Naming

### Volume and the death of natural backpressure

Before coding agents became capable enough to open PRs autonomously, effort was a filter. Writing a patch required understanding a codebase — you had to clone it, read it, run it, and then produce something coherent. That friction was not perfect, but it produced a natural quality floor. Maintainers mostly reviewed contributions from people who had enough investment to show up.

Mitchell Hashimoto, the creator of Ghostty, described what changed in a January 2026 policy update that restricted AI-generated contributions to pre-approved issues and existing maintainers. His explanation was direct: agentic programming has removed the effort-based backpressure that previously limited low-quality submissions, making it too easy to produce large volumes of bad content with minimal cost. This is not an anti-AI argument — Hashimoto noted explicitly that Ghostty's own maintainers use AI daily. It is an argument about the economics of review.

tldraw's founder, Steve Ruiz, went further: he began auto-closing all external pull requests in January 2026, not just AI-generated ones. The volume of low-quality AI submissions had become indistinguishable from the rest of the external contribution queue, so the project stepped back from open contributions entirely — at least until GitHub provides better tooling to manage the flow.

Research published at MSR 2026 puts numbers to the intuition. Across a dataset of nearly 1,800 rejected PRs, AI-generated contributions were disproportionately closed for incomplete implementation and inadequate testing. A separate study found that code-review-agent-only PRs achieve a 45% merge rate compared to 68% for human-authored PRs — a gap that is not about the code quality in isolation, but about the full package: context, communication, and demonstrated understanding of what the project actually needs.

### Hallucinated security reports

The bug bounty collapse at curl is the clearest example of what hallucinated vulnerability reports cost a project in practice. Daniel Stenberg, curl's lead maintainer, ended the project's six-year HackerOne bug bounty program in January 2026 after AI-generated submissions flooded the security queue. By mid-2025, roughly a fifth of all submissions were what Stenberg called "AI slop" — reports that sounded technical but described vulnerabilities that did not exist in the code as written. A previous Register report noted that across curl's entire bounty history up to early 2025, around two-thirds of the 415 vulnerability reports received turned out to be worthless.

The security context makes this worse than ordinary low-quality PRs. Triaging a possible vulnerability is not a quick skim. You have to take the report seriously enough to actually investigate it, because the downside of dismissing a real vulnerability is catastrophic. AI-generated security reports exploit that asymmetry directly: they cost nothing to produce, they look credible, and they consume hours of expert time to disprove.

### Licensing and the DCO problem

Projects like QEMU and NetBSD have taken a more categorical stance, banning AI-generated code contributions outright. Their reasoning centers on the Developer Certificate of Origin — the standard sign-off mechanism that certifies a contributor created the code and has the right to submit it. The DCO explicitly requires the contribution to be created by the submitter. In most jurisdictions, AI-generated code is not a copyrightable work, which makes the assertion legally ambiguous at best.

There is also a downstream concern. AI models are trained on code released under a wide range of licenses, some of which are incompatible with each other and with the target project's license. A snippet that appears clean may reproduce a fragment of GPL code in a project that is MIT-licensed — and because the model does not maintain provenance, there is no way for the contributor to certify otherwise. NetBSD's new contributor rules require prior written approval from its Core Team for any AI-generated code. QEMU's policy, committed directly to the repo, declines any contribution where AI use is known or suspected, citing GitHub Copilot, ChatGPT, and similar tools by name.

Creative Commons Technology team reached the same conclusion independently, stating that generative AI development tooling does not pass a cost/benefit analysis for their contribution workflows and will not accept submissions that include AI-generated content.

## How Bad Is the Broader Picture?

GitHub product manager Camilla Moraes opened a community discussion in early 2026 framing the problem as "a critical issue affecting the open source community." The thread described maintainers dedicating substantial time to contributions that fail to follow project guidelines, are frequently abandoned after submission, and are often AI-generated. One cited data point from a cloud infrastructure head: roughly one in ten AI-created PRs is legitimate and meets the standards needed to open a review in the first place.

GitHub is reportedly considering giving maintainers the option to disable pull requests entirely, restrict them to collaborators, or use more granular permission controls. The fact that the platform is considering a "kill switch" for its core collaboration primitive is a signal of how serious the operational burden has become.

## What Good AI-Assisted Contribution Looks Like

None of this means AI has no place in open source contribution. The distinction that matters is between using AI as a tool that helps you contribute and using AI as a substitute for the understanding that contribution requires.

**Disclosure and transparency.** Ghostty required AI disclosure in pull requests before it tightened its policy further. Several project guidelines now explicitly ask contributors to note substantial AI assistance.

**Human comprehension as a prerequisite.** You should be able to explain every line of AI-generated code you submit, answer follow-up questions from maintainers, and debug it without the AI present. If you cannot, you are submitting code you do not understand to people who will have to maintain it.

**Scope-appropriate use cases.** AI is genuinely useful for documentation improvements, generating test scaffolding for issues you understand, catching typos, and helping you navigate unfamiliar codebases. Pointing an agent at a list of open GitHub issues and letting it generate fifty PRs is subtractive.

**Matching the project's stated policy.** Check the contributing guide before you open anything. An increasing number of projects have explicit AI policies.

The OCaml incident from late 2025 illustrates what happens without these guardrails: a contributor submitted a pull request with more than 13,000 lines of AI-generated code. Maintainers rejected it citing copyright concerns, lack of review resources, and misalignment with the project's development practices.

Note from original article: The MSR 2026 dataset found that among agentic PRs rejected by maintainers, only about 36% reflected clear failures in the agent's code. Another 31% were rejected due to workflow constraints — the right contribution at the wrong time, or submitted without the expected groundwork. Agents do not attend community meetings, follow mailing lists, or know which directions a project has already decided not to pursue. That context is not in the repository.

## The Underlying Tension

Open source runs on volunteer time that is genuinely finite. The review burden is already one of the leading causes of maintainer burnout. What AI-generated contributions have done is dramatically increase the supply side of the PR queue without adding any corresponding capacity on the review side.

That is the actual friction. Not that the code is always bad, but that the economics of evaluation have shifted in a direction that is unsustainable for unpaid maintainers of critical infrastructure. curl is used in virtually every piece of connected software. Its security review process was funded by a bug bounty program that AI-generated reports effectively destroyed.

The projects that have responded with explicit policies — Ghostty, tldraw, curl, QEMU, NetBSD, Creative Commons — are not being anti-technology. They are responding rationally to an incentive structure that has changed.

## FAQ

Does disclosing AI use in a PR description satisfy most project policies? It depends on the project. Disclosure is a minimum floor that some projects now require explicitly, but others — QEMU and NetBSD among them — ban AI-generated code regardless of disclosure.

Can AI-assisted bug reports be submitted responsibly? Yes, but only after you have verified the issue yourself with a manual repro.

Are these bans permanent, or are they a response to current tooling limits? Most maintainers describe the current rules as a response to current conditions. tldraw noted its auto-close policy is temporary pending better platform tooling. GitHub is exploring contribution controls.