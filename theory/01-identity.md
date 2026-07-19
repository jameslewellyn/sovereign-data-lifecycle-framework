# SDLF — Stage 0: Identity

> Identity is the foundation every other stage runs on. You own it. No platform can revoke it.

---

## What It Is

Identity in SDLF is **self-sovereign** — you hold the private keys and no external party can revoke, freeze, or reassign your identity. It is built on **Decentralized Identifiers (DIDs)**, a W3C standard for cryptographically verifiable, platform-independent identifiers.

Identity is not a login. It is an authorization context that threads through every lifecycle stage, answering: *who is acting, in what context, with what verifiable claims.*

---

## Core Properties

**Self-sovereign** — private keys live on your hardware. Loss of keys = loss of identity; no recovery via platform.

**Context-segmented** — you present different identity facets to different contexts without linking them:
- Personal identity (household, health, private life)
- Commercial identity (purchases, subscriptions, price tracking)
- Social identity (public discourse, debate graph contributions)
- Professional identity (work, credentials)

Each facet is a separate DID. Cross-context linkage requires your explicit action.

**Verifiable without revelation** — claims about your identity (e.g. "this person is over 18", "this agent is authorized to read price data") can be cryptographically proven without revealing the underlying data. Zero-knowledge proofs are the mechanism.

**Modular implementation** — the DID method is swappable:
- `DID:key` — simplest, self-contained, no external resolution
- `DID:web` — anchored to a domain you control
- `DID:ion` — anchored to Bitcoin via Sidetree protocol
- Others added without changing the identity interface

---

## How It Works

Every actor in the system — a human user, an AI agent, a module, an external service — has a DID. Every operation carries a signed identity context.

```
Actor → signs operation with private key
     → presents DID + verifiable credential
     → access control layer verifies signature
     → operation proceeds or is rejected
```

Identity resolution is local-first: your DID document (public key, service endpoints) is cached locally. External resolution only occurs when interacting with external parties.

---

## Threat Model

| Threat | Mitigation |
|---|---|
| Platform revokes identity | DIDs are self-sovereign; no platform holds keys |
| Key compromise | Hardware security module (HSM) or air-gapped key storage; key rotation protocol |
| Context linkage (deanonymization) | Separate DIDs per context; no cross-context identifiers |
| Impersonation by agent | All agent operations require delegated credentials signed by user DID |
| Identity proofing fraud | Verifiable credentials from trusted issuers; zero-knowledge proofs for claims |

---

## Interface Contract

```
identity.create(context) → DID
identity.resolve(did) → DIDDocument
identity.sign(did, payload) → SignedPayload
identity.verify(signedPayload) → { valid: bool, did: DID }
identity.issue_credential(did, claim) → VerifiableCredential
identity.present_proof(credential, predicate) → ZKProof
identity.rotate_key(did) → DID  # same identifier, new key
```

---

## Open Questions

- Key recovery: what is the acceptable UX tradeoff between security and recoverability?
- Agent delegation: how granular should delegated credentials be (per-operation vs. per-session vs. per-module)?
- Cross-context interaction: when two of your own facets need to interact, what is the minimal linkage protocol?
