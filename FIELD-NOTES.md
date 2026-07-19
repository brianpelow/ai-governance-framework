# Field Notes: What Building the Controls Taught Me

**Brian Pelow &middot; July 2026**

*A companion to [AI-GOVERNANCE.md](./AI-GOVERNANCE.md). That document argues that 1:1 replay is paramount to staying in business, and describes a four-layer architecture for achieving it. This document records what I learned after building tooling that implements and assesses those controls &mdash; including the places where the tools corrected me.*

---

## Why a second document

The framework was written from conviction and experience. It is an argument.

Since publishing it I have built two deterministic tools: one that audits repositories against governance and security expectations, and one that takes regulatory guidance and produces a remediation plan against a system inventory. Both are running. Both have produced output I did not predict.

Four things surfaced that the framework does not say, and two of them are things I had wrong. I would rather write them down than quietly fix them.

---

## One: the decision record is load-bearing, and that is no longer just my opinion

The framework asserts that the decision record layer is the foundational control &mdash; that model registry, context capture, and replay verification are all built on top of it, and an institution with limited capacity should build it first.

I believed that when I wrote it. I could not demonstrate it.

Building the impact assessment tool required constructing a taxonomy mapping regulatory subject areas to the controls that satisfy them. Ten domains: model risk, automated decisioning, explainability, record retention, fair lending, data governance, third-party risk, change management, incident notification, human oversight. I built each domain independently, working from what the subject area actually demands rather than from what my framework argues.

When the taxonomy was complete, one control appeared in more independent domains than any other.

**Record retention requires a decision record. Automated decisioning requires a decision record. Fair lending requires a decision record.**

Three domains with different regulatory origins, different enforcement mechanisms, and different primary concerns &mdash; recordkeeping, consumer notice, and disparate impact respectively &mdash; converge on the same artifact. Not because I designed them to. Because each one independently needs to know what the system saw and what it decided.

That is a stronger form of the claim than I was able to make before. The decision record is not foundational because it sits at the bottom of an architecture diagram I drew. It is foundational because independent regulatory pressures converge on it.

**The practical consequence:** if you are choosing one control to build this quarter, this is the one that discharges obligations across the widest regulatory surface. That was my recommendation before. It is now a recommendation with a reason behind it that does not depend on trusting my architecture.

---

## Two: governance tooling that cries wolf gets disabled

This is the lesson I did not see coming, and it is the one I would most want a governance leader to take away.

I built a repository auditor that scores security, compliance, and technical debt deterministically. Its first act, once it worked, was to audit itself.

It returned **grade F, 54 out of 100, with two critical findings.**

Every finding was a false positive. The test fixtures contained a fake credential, because a test asserting that credentials are detected has to contain one. The rule definitions contained `eval(` and `exec(`, because those are the regex patterns that detect them. The README contained `shell=True`, because it documents the rule.

The tool had no concept of the difference between a file that *contains* a dangerous pattern and a file that is *about* a dangerous pattern.

Fixing it was straightforward. What matters is what would have happened if I had not.

Had this tool been deployed as a deployment gate, it would have flagged every consumer's test suite. Engineers would have hit a blocking gate with a critical finding pointing at their own test fixtures. They would have investigated once, discovered it was noise, and investigated less carefully the second time. Within a month someone would have added a bypass flag, and within a quarter the bypass would be the default.

**A false positive in a gating control is more damaging than a missed finding, because it degrades the control's authority rather than its coverage.** A missed finding is a gap. A false positive is a training exercise in ignoring the gate.

This does not appear in the framework, and it should have. The framework is careful about what controls to build and largely silent about what makes a control survive contact with the engineers who have to live with it. A governance control that engineers route around is not a governance control. It is documentation of an intention.

I now think tolerance for false positives is a first-class design parameter for any gating control, and that the correct posture is conservative detection with clear escape hatches rather than aggressive detection with a bypass. The correction is documented as [ADR 0003](https://github.com/brianpelow/code-compliance-auditor/blob/main/docs/adr/0003-exclude-tests-and-rule-definitions.md) in the auditor.

---

## Three: scope before obligation

The first complete run of the impact assessment tool bound **all seven systems** in the reference inventory, including a deterministic payments ledger with no model in it and an internal documentation search tool.

The document being assessed opened with a section declaring it applicable to systems informing consequential decisions. My tool read every obligation in the document and ignored the sentence that said who the document was for.

The result was an assessment claiming that an internal search tool required adverse action reason code mapping.

I added a stage that runs before anything else: read the document's own applicability statements, extract the qualifiers they declare, and exclude systems that do not clear all of them. The same document now reaches four systems. The payments ledger is out because there is no model. The search tool is out because it is advisory and internal.

**The governance lesson is about credibility, not correctness.**

An engineering team that receives a compliance assessment claiming their internal search tool needs adverse action notices will conclude that compliance does not understand what they build. They will be right. And they will apply that conclusion to the next assessment, which might be accurate.

Governance functions that cannot say *this does not apply to you* spend their credibility at the same rate as their budget. The ability to scope precisely is what makes the in-scope findings believable.

This is the same failure mode as the false positive problem, arriving from a different direction. Both are about the control's standing with the people subject to it.

---

## Four: if everything is urgent, nothing is

After scope detection and the shared-control correction, the assessment produced fifty-two gaps. Forty-four of them landed in the zero-to-thirty-day window.

The cause was a rule I had written deliberately: escalate priority for consequential systems. Most systems in a regulated inventory are consequential. So most work escalated.

Eighty-five percent of the remediation plan was immediate. That is not a plan. It is a list with a deadline attached, and it is unactionable in exactly the way that causes remediation programs to stall in month two.

I narrowed the rule: criticality moves work by one step, and only downward. Advisory systems relax. Supporting systems relax. Consequential systems take the regulatory domain's base priority unchanged, because **urgency is a property of the obligation, not of the system.** The taxonomy already knows that record retention is urgent and vendor risk is not. Letting system criticality override that discards the judgment encoded in the taxonomy.

Sixteen of fifty-two are now immediate. The three windows carry distinct meaning and a reader can act on the first one.

**The framework tells you what to build. It does not tell you what to build first when everything is required.** Sequencing is where governance programs actually fail &mdash; not in identifying obligations, but in producing an order of operations that a team can execute without burning out in the first month. I underweighted that.

---

## What I would change in the framework

Nothing structural. The four layers are right, and the argument that replay is the load-bearing capability has held up under every test I have put it through.

What I would add is a section on **control durability**: the properties that determine whether a governance control is still functioning eighteen months after it is deployed, as distinct from whether it is correct on the day it ships. False positive tolerance, scoping precision, and executable sequencing are all durability properties. All three are invisible in an architecture diagram. All three determine whether the architecture survives.

The framework is a good argument about what to build. It is thin on what makes the thing you built still be there later.

---

## The pattern across all four

Every one of these surfaced the same way: I built the thing, ran it against real inputs, and read the output carefully enough to notice it was wrong.

None of them surfaced from writing. I had written a governance framework, had it reviewed, revised it, and published it. The framework did not contain these lessons because writing does not produce them. Running the tool against real inputs and being willing to disbelieve a confident, well-formatted result produces them.

That is the argument for building the tooling rather than specifying it. Not that specification is worthless &mdash; the framework came first and the tools implement it. But a specification cannot tell you that your priority rule collapses into a single bucket, or that your scanner flags its own test fixtures, or that your impact model charges four systems to build one thing.

Only the running system tells you that, and only if you read what it says.

---

*The tools are [code-compliance-auditor](https://github.com/brianpelow/code-compliance-auditor) and [regulatory-change-impact-agent](https://github.com/brianpelow/regulatory-change-impact-agent). Each modeling correction described here is documented as an ADR in the relevant repository rather than silently fixed.*

*Brian Pelow is an engineering leader specializing in platform engineering, agentic systems, and regulated industry transformation. This document reflects his personal views developed through work in financial services and manufacturing. All referenced portfolio work is available at github.com/brianpelow.*