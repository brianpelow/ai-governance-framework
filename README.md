# ai-governance-framework

> AI governance in regulated industries: the replay imperative — decision record architecture, model registry, and the four actors who will demand accountability.

## Read the document

[AI-GOVERNANCE.md](./AI-GOVERNANCE.md)

## Field notes

[FIELD-NOTES.md](./FIELD-NOTES.md) &mdash; What building the controls taught me.

Four lessons from implementing this framework as running tooling, including two
places the tools corrected me: a scanner that graded itself F on its own test
fixtures, and an impact model that bound every system in the inventory because
it ignored the document's own scope statement.

The framework tells you what to build. These notes are about what makes the
thing you built still be there eighteen months later.

## Core conviction

1:1 replay is paramount to staying in business.

A regulated institution that deploys AI systems without replay capability is not making a governance trade-off. It is accumulating an existential liability that will be called due at a moment of the regulator's, the plaintiff's, the board's, or the acquirer's choosing — not the institution's.

## What replay requires

Every AI decision must be reconstructable from four components:

- The input state — exact inputs at the moment of inference
- The model identity — exact model version, checkpoint, and configuration
- The inference parameters — temperature, system prompt, retrieval configuration
- The output record — raw model output before downstream processing

## The four actors who will demand it

| Actor | What they ask for | Cost of failure |
|-------|------------------|-----------------|
| Regulator | Complete decision record for sampled decisions | Consent order, restricted AI deployment |
| Plaintiff's attorney | Discovery of adverse decision records | Spoliation inference, class action liability |
| Board | Complete reconstruction of loss event | CTO accountability, failed investigation |
| Acquirer | Governance documentation for all AI systems | Deal-breaking discount or failed transaction |

## The governance architecture

Four layers: decision record layer, model registry, context capture layer, replay verification layer. Each is described in full in the document.

## Related work

This document is the third in a trilogy on engineering leadership in regulated industries:

| Repo | What it covers |
|------|---------------|
| [platform-engineering-thesis](https://github.com/brianpelow/platform-engineering-thesis) | Why platform engineering is the prerequisite for AI in regulated industries |
| [engineering-operating-model](https://github.com/brianpelow/engineering-operating-model) | How to build and lead the organization that executes that strategy |
| [ai-governance-framework](https://github.com/brianpelow/ai-governance-framework) | How to govern AI systems so every decision can be replayed and defended |

## License

Creative Commons Attribution 4.0 International