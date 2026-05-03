# AI Governance in Regulated Industries: The Replay Imperative

**Brian Pelow · May 2026**

---

## The Question Nobody Wants to Answer

When an AI system in a regulated institution makes a consequential wrong decision — a credit denial that was discriminatory, a fraud signal that was missed, a compliance check that passed when it should have failed — there is one question that every examiner, every plaintiff's attorney, every board member, and every acquirer's due diligence team will ask.

*Show me exactly what happened.*

Not approximately. Not a summary. Not a reconstruction from logs assembled after the fact. Show me what the system knew, what it was asked, what model made the decision, what parameters governed it, and what output it produced. Show me this for the specific decision in question, at the specific moment it was made, with complete fidelity.

The organization that can answer this question survives what comes next. The organization that cannot is in an existential crisis regardless of whether the underlying decision was actually wrong.

This is the replay imperative. It is not a best practice. It is not a regulatory preference. It is the foundational requirement that determines whether a regulated institution can operate AI systems at all — and most organizations building AI today are not meeting it.

---

## The Existential Stakes

Before describing what replay requires, it is worth being explicit about what the absence of it costs — because the cost is not a fine or a process improvement. It is the loss of the organization's ability to operate.

The regulated institution that deploys AI systems without replay capability is not making a governance trade-off. It is accumulating an existential liability that will be called due at a moment of the regulator's, the plaintiff's, the board's, or the acquirer's choosing — not the institution's.

The institutions that understand this are building replay capability as a prerequisite for AI deployment, not as a remediation after the fact. They are treating the decision record layer as a first-class infrastructure investment alongside the model itself. They are registering models, capturing context, verifying replay, and conducting drills before anyone asks them to. And they are building a governance advantage that competitors without replay capability cannot match quickly — because in a regulatory examination where both organizations are examined simultaneously, the one with complete decision records demonstrates a control environment that the other cannot. That disparity becomes a supervisory finding, a competitive differentiator, and eventually a market structure outcome.

The institutions that do not understand this are deploying AI systems that are, in governance terms, invisible — systems that produce decisions that cannot be examined, reconstructed, or defended. In a regulated environment, invisible is not neutral. Invisible is dangerous. Invisible is the organization that discovers, at the worst possible moment, that it cannot answer the one question that determines whether it stays in business.

*Show me exactly what happened.*

If you cannot answer that question, you are not governing AI. You are hoping nothing goes wrong. In regulated industries, hope is not a governance strategy.

---

## What Replay Actually Means

The word "explainability" has been used so loosely in AI governance discussions that it has lost its meaning. Feature importance scores, SHAP values, attention weights — these are useful for model development, but they are not replay. They describe how a model works in general. They do not reconstruct what a specific model did in a specific moment for a specific input.

Replay, as defined here, means the ability to reconstruct any AI decision with four components:

**The input state.** Every piece of data the model received at the moment of inference — the prompt, the context, the retrieved documents, the user history, the real-time signals. Not a summary. The exact input, preserved in full.

**The model identity.** The exact model version that produced the output — not just the model family or the provider, but the specific checkpoint, the specific fine-tuning run, the specific quantization. Model providers update models continuously. A model called by the same name today is not the same model that carried that name six months ago. If you cannot identify the exact model that made the decision, you cannot replay it.

**The inference parameters.** Temperature, top-p, top-k, system prompt, retrieval configuration, tool definitions — every parameter that governed the inference. A different temperature setting on the same model with the same input can produce a materially different output. These parameters are part of the decision.

**The output record.** The complete output the model produced — not the downstream processed version, not what was shown to the user after filtering, but the raw model output. If the system filtered, transformed, or acted on the model output, that chain of processing must also be captured.

With these four components, any decision can be reconstructed to the standard that a regulator, a plaintiff's attorney, a board, or an acquirer will accept. Without all four, reconstruction is incomplete — and incomplete reconstruction in a regulatory examination or litigation context is treated the same as no reconstruction at all.

---

## The Four Actors Who Will Demand It

Understanding why replay is existential requires understanding who will ask for it, in what context, and what happens when you cannot produce it.

**The regulator.** An OCC examiner, an FRB supervisory team, an FCA reviewer, or an ECB examiner conducting a supervisory examination will ask for the complete decision record for a sample of consequential AI decisions. This is current supervisory practice for model risk management examinations under SR 11-7 and its equivalents. The organization that cannot produce a complete decision record has a model risk management control failure. That failure becomes a matter of supervisory record. It may become a consent order. It may result in restrictions on AI deployment until the control is remediated. The regulatory cost of a replay failure is not a fine — it is the loss of operational freedom to deploy AI at all.

**The plaintiff's attorney.** An adverse AI decision — a credit denial, an insurance claim rejection, a fraud flag that freezes an account — is increasingly the subject of litigation. The plaintiff's attorney will seek discovery of the complete decision record for the adverse decision affecting their client. If that record does not exist, or cannot be produced in complete and unaltered form, the legal doctrine of spoliation applies: the court may instruct the jury to infer that the missing evidence would have been unfavorable to the institution. In a class action context, a spoliation finding is potentially catastrophic. The plaintiff does not need to prove the AI was wrong. They need to show you cannot prove it was right.

**The board.** After a significant AI-related loss event — a model that missed a fraud pattern, a compliance check that approved a series of violations, a credit model that produced systematically biased decisions — the board will conduct an internal investigation. If the engineering organization cannot produce the complete decision record for the relevant decisions, the investigation cannot be completed. The board cannot determine what went wrong, who is accountable, or what remediation is required. In this scenario the CTO who cannot produce the replay record is accountable for the failure to maintain it. The consequence is not a process improvement. It is personal accountability for the failure to govern the AI systems under their stewardship.

**The acquirer.** M&A due diligence in regulated financial services and manufacturing now includes AI system review as a standard workstream. An acquirer's technical and legal team will ask for complete governance documentation for every AI system in production — including the decision record architecture, the replay capability, and a sample of historical decision records. The organization that cannot demonstrate replay capability is presenting an acquirer with an unknown liability: every AI decision made without a complete record is a potential regulatory finding, a potential litigation exposure, and a potential operational failure that the acquirer cannot quantify. Unknown liabilities become deal-breaking discounts or deal-breaking conditions. The organization without replay capability is not just operationally exposed — it is financially exposed at its most important liquidity event.

---

## Why Most Organizations Are Not Ready

The gap between what regulated institutions are building and what replay requires is wider than most technology leaders recognize, for three reasons.

**Logging is not replay.** Most AI systems in production today generate logs. These logs typically capture the final output, the user identifier, the timestamp, and perhaps a request identifier. They do not capture the complete input state, the exact model version, the inference parameters, or the raw model output before downstream processing. Logs that capture what the system produced but not what it was given and how it was configured are insufficient for replay. They are a record of outcomes, not a record of decisions.

**Model versioning is not practiced.** Model providers update models continuously, often without changing the model name or providing explicit versioning at the API level. Fine-tuned models are updated, retrained, and replaced on development timelines that frequently outpace governance processes. The organization that deployed a model six months ago and has not maintained an explicit record of which model version was in production at which time cannot reconstruct the model identity component of any historical decision. This is not a hypothetical gap — it is the current state of most enterprise AI deployments.

**The context window is ephemeral.** Retrieval-augmented generation systems construct context windows at inference time from document stores, databases, and real-time signals. That context — the specific documents retrieved, the specific data pulled, the specific signals incorporated — is typically not preserved after the inference is complete. Reconstructing the input state for an RAG decision made six months ago, in a system where the document store has been updated since then, is not possible without explicit capture at inference time. The ephemeral nature of the context window is the most common and most consequential replay gap in production AI systems today.

---

## The Governance Architecture

Building replay capability into an AI system is not an afterthought — it is an architectural decision that must be made before the system is deployed. Retrofitting replay into a production AI system is significantly more expensive and disruptive than building it correctly from the start.

The governance architecture has four layers.

**The decision record layer.** Every inference produces a decision record — a structured artifact that captures all four replay components: input state, model identity, inference parameters, and raw output. This record is written to an immutable, append-only store at the moment of inference, before any downstream processing. Integrity is protected by a hash-based mechanism applied at write time — the hash of the complete record is stored alongside it, enabling detection of any subsequent alteration. It is not derived from logs. It is not reconstructed after the fact. It is a first-class output of every inference, produced by the inference infrastructure, not by the application layer.

**The model registry.** Every model version deployed to production is registered in a model registry at the moment of deployment, with its full provenance — the base model, the fine-tuning data, the training configuration, the evaluation results, and the deployment authorization. The model registry is the source of truth for model identity. Every decision record references the model registry entry for the model that produced it. Model updates require a new registry entry and a new deployment authorization — they cannot silently replace an existing model in production.

**The context capture layer.** For retrieval-augmented systems, the complete context window constructed at inference time — including the retrieved documents, the retrieval query, the retrieval configuration, and the real-time signals incorporated — is captured as part of the decision record. The context capture layer must operate at the retrieval layer, not the application layer, to ensure completeness. Documents retrieved from the context store are identified by version — the specific version of each document that was retrieved at inference time, even if that document has been updated since.

**The replay verification layer.** The replay capability is not useful if it has never been tested. The governance architecture must include periodic replay verification — the process of selecting a sample of historical decision records and confirming that they contain sufficient information to reconstruct the decision to the required standard. A successful replay verification has a defined acceptance criterion: a reviewer with no prior knowledge of the decision must be able to reconstruct the complete input state, identify the exact model version, confirm the inference parameters, and reproduce the output chain using only the preserved decision record and the model registry. If any component cannot be reconstructed from the preserved artifacts alone, the verification fails. Replay verification runs on a defined cadence — monthly at minimum for high-consequence systems — produces a compliance artifact, and feeds into the model risk management reporting structure.

---

## The Organizational Model

The governance architecture described above requires a working relationship between the governance function and the engineering function that most regulated institutions have not built.

The decision record is owned by the governance function. It defines the retention period, the access control model, the integrity protection requirements, and the production standard for regulatory examination. The engineering function builds the infrastructure that produces the record to the governance function's specification. Neither function can discharge this responsibility alone — governance without engineering produces a standard nobody can implement, engineering without governance produces a log that does not satisfy the standard.

The model registry is owned jointly. The governance function defines what must be registered and what constitutes a deployment authorization. The engineering function operates the registry and enforces the requirement that no model reaches production without a registry entry. Disputes about what constitutes a material model change requiring a new registry entry — a fine-tuning run, a prompt change, a retrieval configuration update — are escalated to the Chief Risk Officer for resolution. Engineering judgment alone is not sufficient for this determination in a regulated environment.

The replay drill is owned by the governance function and conducted with engineering support. The governance function selects the sample, conducts the reconstruction attempt, and documents the result. Engineering provides access to the preserved artifacts and assists with technical reconstruction questions. The governance function owns the finding if the verification fails — which means the governance function has an organizational incentive to ensure the architecture is correct before the drill, not after.

---

## The Regulatory Landscape

The replay imperative is not ahead of the regulatory curve. In several respects, it is behind it.

SR 11-7, the Federal Reserve and OCC guidance on model risk management, has required complete model documentation and decision record retention for quantitative models in banking since 2011. The extension of this framework to AI systems — explicitly addressed in OCC guidance on AI and machine learning — means that AI systems making consequential decisions in regulated banking are already subject to model risk management expectations that include decision record retention.

The EU AI Act, which applies to high-risk AI systems including those used in credit decisioning, employment, and critical infrastructure, requires logging of system operation sufficient to enable post-hoc monitoring and investigation of incidents. The direction is unambiguous: high-risk AI systems in the EU must maintain records sufficient for regulatory examination.

The CFPB's guidance on automated decisioning in consumer credit requires that adverse action notices provide specific reasons for adverse decisions made by automated systems. Providing specific reasons requires having a record of what the system considered. The adverse action notice requirement is, in effect, a limited replay requirement for consumer credit AI systems — and it is enforceable today.

The direction of regulatory travel is consistent across jurisdictions: AI systems making consequential decisions in regulated industries must maintain records sufficient for reconstruction. The organizations building replay capability now are building to a standard that regulation is moving toward. The organizations that are not are building a liability that will become more expensive to remediate with every passing quarter.

---

## Implementation: Where to Start

The full replay architecture is not built overnight. The sequencing matters — and for organizations with constrained governance capacity, the triage framework matters equally.

**Triage by consequence, not by system count.** Not every AI system requires the same replay standard immediately. Any AI system whose output could be the subject of a regulatory examination, a litigation discovery request, a board investigation, or an acquirer's due diligence review requires full replay architecture. Systems whose output is purely advisory and not connected to a consequential external decision can operate with enhanced logging as a sequencing decision — not a permanent carve-out. The distinction between advisory and consequential is narrower than it appears: a system that seems advisory today becomes the system that informs a consequential decision tomorrow. Treat the lighter standard as a temporary starting point with a defined timeline for full compliance.

**Start with the model registry.** The model registry is the lowest-cost, highest-impact governance control available. Every organization deploying AI can implement a model registry today, before the next model update, at modest engineering cost — typically two to four weeks of focused engineering effort for a basic implementation. Implement the registry first, register every model currently in production retroactively with best-available provenance, and require a registry entry for every future deployment. This single action closes the most universal replay gap in enterprise AI.

**Build the decision record layer for highest-consequence systems.** For the systems identified in the triage as requiring full replay architecture, the decision record layer is the next investment. The engineering cost is typically four to eight weeks of focused engineering effort per system. At current storage pricing, retaining complete decision records for a high-volume AI system costs in the range of tens of thousands of dollars per year per system. Frame it to the board as insurance with a known premium against a liability with an unknown but potentially existential cost.

**Conduct a replay drill before the first examination.** Select ten historical decisions from the highest-consequence system, attempt full reconstruction using only the preserved artifacts, document what can and cannot be reconstructed, and treat the gaps as remediation items. The worst time to discover that the replay architecture is incomplete is during a regulatory examination. Find the gaps first.

---

*Brian Pelow is an engineering leader specializing in platform engineering, agentic systems, and regulated industry transformation. This framework reflects his personal views developed through work in financial services and manufacturing. All referenced portfolio work is available at github.com/brianpelow.*