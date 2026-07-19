# Unsolved: The Open Problems in AI Replay

**Brian Pelow &middot; July 2026**

*A companion to [AI-GOVERNANCE.md](./AI-GOVERNANCE.md). That document argues that every AI decision in a regulated context must be reconstructable. This document is about the parts of that argument I cannot yet fully deliver on, and what would have to be true to close each gap.*

---

## Why write this down

The framework makes a strong claim: an institution that deploys AI without replay capability is accumulating an existential liability. I stand behind it.

But there is a difference between a control that is hard to build and a control that nobody knows how to build. Conflating the two is how governance frameworks lose the engineers who have to implement them. If I tell a team that complete replay is achievable and they discover in month three that one of the requirements is an open research problem, I have spent credibility I will need later.

So: six problems. Some are engineering difficulty. At least two are genuinely unsolved, and one of them I think is structural rather than temporary.

---

## One: corpus versioning for retrieval

**The problem.** To replay a decision from a retrieval-augmented system, you need the exact corpus state at inference time. Not the documents that are in the index today &mdash; the documents that were in the index at 14:23 on the day of the decision, in the same chunking, with the same embeddings, ranked by the same index version.

Corpora change constantly. Documents are added, revised, and removed. Reindexing happens on a schedule nobody records against decision timestamps. Six months later the retrieval step is unreconstructable, which means the input state is unreconstructable, which means the decision is unreconstructable regardless of how carefully you logged the model version.

**Current state.** Snapshotting entire corpora at decision granularity is prohibitive. Content-addressed chunk hashing with retention of retrieved chunks is tractable and gets you most of the way &mdash; you record what was actually retrieved rather than what could have been. That is what I recommend today.

**Why it is still open.** Recording retrieved chunks proves what the model saw. It does not prove what the retrieval step *would* have returned, which is the question in any dispute about whether the system should have surfaced a document it did not surface. Answering that requires the full index state, and I do not know a practical way to preserve it at scale.

**What would have to be true.** Vector stores would need native time-travel with commit semantics, the way source control has. Some are moving in this direction. None are there.

---

## Two: the model is not deterministic either

**The problem.** Replay implies that identical inputs produce identical outputs. Models frequently do not cooperate.

At any temperature above zero, sampling is stochastic. Setting temperature to zero helps but does not fully resolve it: floating-point accumulation order varies with batch composition and hardware, so the same prompt on the same weights can produce different tokens depending on what else was in the batch. Mixture-of-experts routing introduces further variance.

**What this means for the framework.** Replay cannot mean *bit-identical output reproduction* for most production configurations. It has to mean something weaker and more defensible: complete reconstruction of the inputs, the model identity, and the parameters, plus the recorded output, such that a reviewer can verify what the system was asked and what it answered.

I have been imprecise about this distinction in the past, and it matters. An examiner asking "show me what happened" is satisfied by a complete record. An examiner asking "run it again and show me you get the same answer" cannot be satisfied at all for a stochastic system, and promising otherwise is a trap.

**What would have to be true.** Nothing, really &mdash; this is a definitional problem rather than a technical one. The correct move is to define replay as reconstruction rather than reproduction, and to say so explicitly before an examination rather than during one.

---

## Three: you cannot retain a model you do not control

**The problem.** Complete replay requires the exact model weights. If you serve your own models you can retain checkpoints, at a storage cost that is annoying but manageable.

If you call a hosted API, you cannot. The provider controls the weights. Versions are deprecated on the provider's schedule, not yours. You can record the version string, but a version string is a pointer, and in three years it may point at nothing.

Your decision record says the decision was made by a specific model version. Your retention obligation runs seven years. The provider retired that version after eighteen months. The record is now a citation to a document that no longer exists.

**Current state.** Record the version string, the full request and response payloads, and the provider's published behavior notes for that version. This gives you a complete record of the interaction even though you cannot re-execute it.

**Why it is still open.** That is evidence of what happened, not capability to reproduce it. Whether it satisfies a given examiner is untested in most jurisdictions, and I would not want to be the institution that finds out first.

**What would have to be true.** Providers would need to offer contractual version retention with defined windows, or escrow arrangements for regulated customers. Some enterprise agreements are beginning to address this. It is a procurement question as much as an engineering one, and it belongs in vendor negotiations before it belongs in an architecture diagram.

---

## Four: agentic trajectories multiply the problem

**The problem.** Everything above concerns a single inference. An agent executing thirty steps, calling six tools, each returning state from systems that have since changed, is a different order of difficulty.

Replaying step nineteen requires the state of the world as of step eighteen &mdash; including whatever an external API returned, which depended on data that has since been updated. You can record every tool response, which is the right move and is tractable. But the recording grows large quickly, and the question of what constitutes a complete record of a long trajectory does not have a settled answer.

**Current state.** Record the full trajectory: every prompt, every tool call, every tool response, every intermediate output, with sequence and timestamps. Treat the trajectory as the atomic unit of the decision record rather than the individual inference.

**Why it is still open.** I am confident this is *necessary*. I am not confident it is *sufficient*, and I have not seen an agentic system in a regulated production context survive a real examination yet. The precedent does not exist. Anyone claiming to know exactly what regulators will require here is guessing, including me.

---

## Five: artifact rot over a seven-year retention window

**The problem.** Retention obligations commonly run seven years. Model artifacts do not age gracefully.

A serialized model from 2019 may require a framework version that requires a Python version that requires an operating system that no longer receives security updates. Loading it in 2026 is a build archaeology exercise. Loading it in 2031 may be impossible without a preserved container image, and preserving container images for seven years has its own decay problems.

**Current state.** Preserve the container image alongside the weights, not just the weights. Include the full dependency manifest. Test the restore path periodically rather than assuming it.

**Why it matters more than it sounds.** This is the failure that will not surface until the moment it is needed. Every year the restore path silently rots, and nobody notices, because nobody tries. The institution discovers it has an unloadable artifact during the examination that required it.

**What would have to be true.** Nothing exotic &mdash; this is discipline rather than research. Schedule a restore drill. If you cannot load a two-year-old model artifact today, you will not load a seven-year-old one later.

---

## Six: the decision record is a comprehensive PII store

**The problem, and I think this is the sharpest one.** A decision record for a consumer credit decision contains the exact inputs at inference time. For a consumer decision, the inputs are the consumer's personal and financial data.

Building complete replay capability means building a comprehensive, immutable, long-retention store of personal data about every consumer you have made a decision about. You are constructing this for compliance reasons. It is also, simultaneously, a significant privacy liability and an attractive target.

There is a genuine tension between retention obligations and erasure rights. A consumer exercising a deletion right under a privacy regime encounters a record you are separately obligated to preserve. Both obligations are real. The resolution is jurisdiction-specific, frequently unclear, and in some framings unresolved.

**Current state.** Encrypt at rest with independent key management, apply strict access controls with full audit, minimize what is captured to what replay actually requires, and get counsel to opine on the retention-versus-erasure conflict for your specific jurisdictions before you build rather than after.

**Why I call this structural.** The other five problems are engineering difficulties that better tooling will erode. This one is a conflict between two legitimate regulatory objectives, and better tooling does not resolve a conflict between objectives. It can only make the tradeoff more explicit.

I do not have a clean answer. I am fairly confident nobody does, and I am suspicious of anyone who claims otherwise.

---

## What this does not change

None of this weakens the core argument. An institution that records nothing is in a categorically worse position than one that records what it can. The gap between complete replay and no replay is far larger than the gap between complete replay and the achievable version of it.

The framework's recommendation stands: build the decision record layer first, register your models, capture retrieval context, and run a replay drill before your first examination. Every one of those is achievable today with known techniques.

But build it knowing which parts are hard engineering and which parts are open questions. When an examiner asks about corpus reconstruction for a retrieval system, the credible answer is a precise account of what you preserve, what you cannot, and why &mdash; not a claim of completeness that collapses on the second question.

**Governance credibility is built on precision about limits, not on confidence about coverage.** I would rather publish a document naming six problems I cannot fully solve than one implying I have solved them.

---

*Brian Pelow is an engineering leader specializing in platform engineering, agentic systems, and regulated industry transformation. This document reflects his personal views developed through work in financial services and manufacturing. It is not legal advice. All referenced portfolio work is available at github.com/brianpelow.*