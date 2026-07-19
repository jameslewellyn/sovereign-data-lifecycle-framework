# SDLF — Overview

> A modular, privacy-first, adversarially-hardened framework for owning and controlling data across its entire lifecycle. Data is an asset. You define the rules at every stage.

---

## The Problem

Modern data infrastructure is built for platforms, not people. Data about you is collected, processed, monetized, and shared by parties whose interests conflict with yours — often without your knowledge and always without meaningful control.

The Sovereign Data Lifecycle Framework (SDLF) inverts this: **you own the data, hold the keys, define the trust model, and set policy at every boundary.**

---

## What SDLF Is

A stack of modular, composable contracts — one per stage of the data lifecycle. Each stage is a defined interface with swappable implementations. The framework is not an application; it is infrastructure that applications run on top of.

Every stage is:
- **Modular** — the implementation is swappable without affecting other stages
- **Extensible** — new capabilities added without breaking existing records or pipelines
- **Privacy-enabled** — data minimization and consent tracking built in
- **Security-enabled** — encryption, key ownership, and audit logging at every boundary
- **Adversarially hardened** — zero trust at every interface; no implicit assumptions
- **Decentralized** — no required central node at any stage

---

## The Lifecycle Stages

```
Identity (foundation)
    │
    ▼
Classification ──────────────────────────────────┐
    │                                             │
    ▼                                             │
Storage ← → Access Control                       │
    │                                             │
    ▼                                             │
Ingestion → Manipulation                         │
    │                                             ▼
    ▼                                      Epistemic Backbone
Filtering ← Service Subscription                 │
    │                                             │
    ▼                                             │
Privacy → Sharing                                │
    │                                             │
    ▼                                             │
Removal ◄────────────────────────────────────────┘
```

| # | Stage | Core Concern |
|---|---|---|
| 0 | Identity | Who is acting; self-sovereign, context-segmented DIDs |
| 1 | Classification | What kind of data this is; faceted, combinatorial labels |
| 2 | Storage | Where data lives; encrypted, backend-agnostic |
| 3 | Access Control | Who can read/write/execute on any data segment |
| 4 | Ingestion | What enters the system; normalized, sandboxed, provenanced |
| 5 | Manipulation | How data is transformed; auditable, replayable pipelines |
| 6 | Filtering | What reaches you; adversarially scored, confidence-gated |
| 7 | Service Subscription | External stream and decider contracts; auditable, revocable |
| 8 | Privacy | What is exposed; minimized, consent-ledgered, inference-controlled |
| 9 | Sharing | What you push out; share tokens, scoped, revocable |
| 10 | Removal | Right to delete; cascade, revocation, retention policy enforcement |
| — | Epistemic Backbone | Shared truth substrate; debate graph, deciders, personalized truth |

---

## Cross-Cutting Principles

1. **Data sovereignty** — you own it, hold the keys, set the rules
2. **Zero trust** — every boundary is adversarial until demonstrated otherwise
3. **Modularity** — every stage is a swappable interface, not a locked implementation
4. **Decentralization** — no required central node at any stage
5. **Append-only history** — what happened is preserved; current state can change
6. **Minimum exposure** — share the least, retain the most, revoke easily
7. **Human override** — outputs are recommendations, never decisions
8. **Permanent contestation** — truth is a live, weighted, personal view; never settled
9. **Private wins** — on any conflict or ambiguity, the most restrictive classification applies

---

## What SDLF Is Not

- Not an application — it is a framework of modular contracts
- Not a platform — no central server, no operator, no terms of service
- Not a consensus engine — it does not seek or enforce global truth
- Not surveillance infrastructure — adversarially designed against surveillance at every stage

---

## Example Use Case

The **Personal Economics Engine** — tracking item prices, inventory, and household economics — is a reference implementation that exercises every stage of the lifecycle. See `12-example-economics.md`.
