# SDLF — Stage 1: Classification

> Every record has a faceted label describing what kind of data it is. Classification informs policy — it does not enforce it directly.

---

## What It Is

Classification is a **combinatorial, faceted tagging system** applied to every data record in the system. A record carries one value (or none) per dimension, and the combination of facets describes the full nature of the data.

Classification is **descriptive, not prescriptive** — it tells other stages what the data is. Access control, privacy, sharing, and removal policies read the classification and act on it. This separation means policy can change without reclassifying data.

---

## Design Principles

- **Faceted, not hierarchical** — dimensions are independent; any combination is valid
- **Combinatorial** — a record carries multiple facets simultaneously
- **Auto-inferred at ingestion** — source adapters propose classification based on context
- **User-overridable** — you can reclassify any record you own; override is logged, not destructive
- **Extensible** — new dimensions added without migrating existing records; missing facets default to safest value
- **Private wins** — on any conflict or ambiguity, the most restrictive value propagates
- **Contamination rule** — derived records inherit the most restrictive facet value from all inputs, per dimension

---

## Facet Dimensions

### `sensitivity` — how protected the data must be
| Value | Meaning |
|---|---|
| `public` | Intentionally published; no protection required |
| `pseudonymous` | Linked to a pseudonym; re-identification is a risk |
| `private` | Personal; shared only by explicit action |
| `secret` | Highest protection; keys required for any access |

*Default if unset: `private`*

---

### `domain` — what area of life the data belongs to
`commercial` · `social` · `epistemic` · `health` · `financial` · `legal` · `system`

*Extensible with namespace prefix: `health.prescription`, `financial.tax`*

---

### `temporality` — relationship to time
| Value | Meaning |
|---|---|
| `realtime` | Live feed; value degrades rapidly |
| `current` | Reflects present state |
| `historical` | Past observation; append-only |
| `projected` | Forecast or estimate |

---

### `provenance` — how the data entered the system
`self-authored` · `scraped` · `imported` · `inferred` · `on-chain` · `sensor`

---

### `contestability` — how disputed the data is
| Value | Meaning |
|---|---|
| `verified` | Cryptographically or institutionally confirmed |
| `claimed` | Asserted but unverified |
| `disputed` | Active disagreement in the debate graph |
| `unknown` | No contestability information available |

*Default if unset: `unknown`*

---

### `mutability` — can this record change
| Value | Meaning |
|---|---|
| `immutable` | Cannot be changed after creation |
| `append-only` | New records can be added; existing records cannot change |
| `mutable` | Can be updated in place |

*Contamination: most restrictive propagates — immutable beats append-only beats mutable*

---

### `jurisdiction` — regulatory and legal scope
`local` · `national` · `EU` · `global`

*Extensible: `national.US`, `national.US.IL`*

---

### `identifiability` — re-identification risk
| Value | Meaning |
|---|---|
| `identified` | Directly linked to a real-world identity |
| `pseudonymous` | Linked to a pseudonym; re-identification possible |
| `anonymized` | Re-identification not feasible with known techniques |
| `aggregate` | Statistical summary; no individual record |

---

### `intent` — why this data was created
`observation` · `assertion` · `transaction` · `communication` · `metadata`

---

### `retention` — how long this data should live
| Value | Meaning |
|---|---|
| `ephemeral` | Delete after use (session or single operation) |
| `session` | Delete at end of session |
| `bounded` | Delete after N days (parameterized: `bounded:90`) |
| `permanent` | Retain indefinitely (explicit opt-in) |

*Default if unset: `bounded:365`*

---

## Contamination Rules (Derived Records)

When a derived record is produced from multiple inputs:

| Dimension | Rule |
|---|---|
| `sensitivity` | Most restrictive propagates (secret > private > pseudonymous > public) |
| `domain` | Union of all input domains |
| `temporality` | Most specific to the derivation context |
| `provenance` | `inferred` (always, for derived records) |
| `contestability` | Most uncertain propagates (unknown > disputed > claimed > verified) |
| `mutability` | Most restrictive propagates |
| `identifiability` | Most restrictive propagates |
| `retention` | Most restrictive (shortest) propagates |

---

## Reclassification

- Any record owner can reclassify at any time
- Reclassification is recorded as a new classification event in the audit log
- The full classification history is preserved; the current label is the latest entry
- Reclassification may trigger downstream policy re-evaluation (e.g. a record reclassified to `sensitivity:public` may become shareable)

---

## Interface Contract

```
classification.infer(record, source_context) → FacetSet
classification.apply(record_id, facets) → ClassifiedRecord
classification.override(record_id, facets, reason) → ClassifiedRecord
classification.history(record_id) → [ClassificationEvent]
classification.propagate(input_records[]) → FacetSet  # contamination rule
classification.query(facet_filter) → [record_id]
```

---

## Threat Model

| Threat | Mitigation |
|---|---|
| Misclassification at ingestion | User override; classification history preserved |
| Policy applied to wrong class | Classification and policy are separate; policy engine reads facets |
| Inference attack via classification metadata | Classification metadata itself carries a sensitivity facet |
| Cross-context linkage via shared classification | Classification is per-record; no global index exposed cross-context |

---

## Open Questions

- Should facet values be versioned? (a value's meaning could drift over time)
- Should the contamination rules themselves be user-configurable?
- How are classification conflicts resolved when two modules disagree on inferred facets?
