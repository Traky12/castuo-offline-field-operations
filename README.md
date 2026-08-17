# 📴 castuo-offline-field-operations — Resilient Field Resilience

![Status](https://img.shields.io/badge/Status-Active%20Engineering-blue)
![Maturity](https://img.shields.io/badge/Maturity-Implementado-informational)
![License](https://img.shields.io/badge/License-AGPL--3.0-yellow)

> **Offline-first field operations, local assistance, and resilient communications for CASTÚO-SYSTEM™**

---

## 1. Purpose & Scope
**castuo-offline-field-operations** focuses on the human and operational aspects of field work in disconnected environments. It provides the tools and protocols necessary to maintain operational integrity when cloud access is unavailable.

Its scope covers:
- **Local Knowledge Base:** Offline RAG and AI assistance (SABIONDA).
- **Resilient Communications:** Local mesh and peer-to-peer protocols (Meshtastic).
- **Offline Navigation:** GIS and spatial data handling without external maps (Organic Maps).
- **Field Evidence Collection:** Capturing and buffering operational data for later synchronization.

---

## 2. Ecosystem Position
castuo-offline-field-operations acts as the **EDGE** layer for human operations, complementing the automated IoT stack.

```text
castuo-offline-field-operations (Edge)
     │
     ├── CASTÚO-SYSTEM (Core)
     │      Upstream sync target
     │
     ├── castuo-agro-edge (Edge)
     │      Related field stack
     │
     └── GOLDfish (Assurance)
            Security & validation gate
```

---

## 3. Capability Metadata
- **Capability ID:** CAP-OFFLINE-FIRST-001
- **Version:** 1.4
- **Status:** N2 – Prepared
- **Target Maturity:** N5 – Pilot
- **Owner:** CASTÚO-SYSTEM
- **Domain:** Edge Computing, AI, GIS, Communications
- **Dependencies:** SABIONDA, Evidence Center, Digital Thread, Edge Gateway

---

## 4. Engineering & Evidence
Following the **Evidence-First** principle, this repository provides evidence of operational resilience.
- **Implemented:** Local knowledge base integration and offline navigation prototypes.
- **Planned:** Full mesh networking protocols and federated field evidence sync.

Every operational event is logged with provenance data for later validation in **CASTÚO-EVOLUTION**.

---

## 5. Navigation
[← Ecosystem Profile](https://github.com/Traky12) | [→ Core Platform](https://github.com/Traky12/Castuo-system) | [→ Agro Edge](https://github.com/Traky12/castuo-agro-edge) | [→ Governance](https://github.com/Traky12/castuo-evolution)

---

## 🌐 Connect
- 🌍 [Website](https://castuo-system.es/)
- 📴 [Field Operations](https://github.com/Traky12/castuo-offline-field-operations)

**Build · Validate · Observe · Document · Evolve**

## Architecture governance boundary

This repository is governed through the CASTÚO-SYSTEM evidence chain. Its current role, visibility boundary, required provenance, security baseline and promotion rules are defined in [`docs/CASTUO_ARCHITECTURE_GOVERNANCE.md`](docs/CASTUO_ARCHITECTURE_GOVERNANCE.md). A repository artifact or green workflow proves only the declared scope; it does not by itself prove certification, production operation, funding, customer contracts or commercial success.

## Private-cloud and evidence boundary

This repository is part of the CASTÚO-SYSTEM private-cloud target architecture. Its repository scope does not by itself prove cloud provisioning, DNS, production operation, customer traction, financing, certification or independent validation. The service identity is a governed target boundary until a deployment record, access control, health check, observability, backup, restore, rollback, owner and dated Evidence Center record are published.

The public state model is `DOCUMENTED` → `IMPLEMENTED_LOCAL` → `TESTED` → `VALIDATED` → `OPERATIONAL`. OpenClaw and n8n, where referenced, are optional compatibility adapters and not the sovereign governance control plane.\n

<!-- CASTUO-GOVERNED-README-BLOCK:START -->
## CASTÚO-SYSTEM governed operating model

This repository is part of the CASTÚO-SYSTEM evidence operating system. Its status is governed by implementation, evidence and promotion gates; repository presence or vendor language is not evidence of operational maturity.

### Three-plane architecture

| Plane | Role | Repository boundary |
|---|---|---|
| Internal control plane | Capabilities, evidence, claims, gates, passports and N3/N4/N5/N6 maturity | This repository's contracts and governed records |
| Competitive intelligence | 1/0/?/N/A comparison, 1D/1V/1R semantics, scenarios and sensitivity | Comparative records remain bounded by provenance |
| External validation | Independent review, reproducible benchmark, field pilot, KPIs and economic evidence | Promotion requires reviewable external evidence |

### Claim discipline

`CAPABILITY` is not `EVIDENCE`; `EVIDENCE` is not `MATURITY`; `MATURITY` is not `CLAIM`; and `CLAIM` is not `COMPETITIVE ADVANTAGE`. The binary matrix uses `1D` for primary-source declaration, `1V` for reproducible verification, `1R` for independent reproduction, `?` for unknown, `0` for absent in the tested boundary and `N/A` for non-comparable scope. Unknown is never silently converted into absence or proof.

### Reproducibility benchmark

The current competitive protocol is **S-001 Evidence-Ready Field Operations**: the same operational task, inputs and connectivity failure condition are replayed through CASTÚO and an alternative implementation. Its metrics cover continuity, recovery, provenance, evidence completeness, reviewability and claim generation. `P2` versions the fixture, `E3` requires independent replay and `N5` requires a signed field pilot with KPIs. A local fixture result is labelled `LOCAL REPRODUCTION / NO FIELD CLAIM`.

### Implemented progress surface

The governed integration currently covers the following evidence-scoped capabilities:

| Capability | Current state | Boundary |
|---|---|---|
| Secure SaaS connectors | Vault-first intents, rotation, revocation, owner isolation, least-privilege scopes and redacted audit | Real provider selection remains `SECURITY_HOLD` until dual approval |
| Quantum Decision Lab | Deterministic local simulator with evidence budget, heuristic confidence and factor readouts | `LOCAL RESULT / NO CLAIM`; no field or economic evidence implied |
| Assurance P0/P1/P2 | Roadmap, Trust Passports, AI Security Passport, SLO/observability contracts and open-gate register | External review, production restore and remote assurance remain pending |
| Competitive intelligence | 1/0/?/N/A matrix, weighted coverage, evidence completeness and 17 capability passports | `?` is uncertainty; it is never silently converted to 0 or 1 |
| S-001 reproducibility benchmark | Same task, inputs and failure condition; continuity, recovery, provenance, completeness, reviewability and claim generation | P2 fixture, E3 independent replay and N5 field/economic evidence are separate gates |
| Supply-chain controls | Secret scan, SBOM, dependency scan and local dependency result of 0 advisories | Local green status does not prove remote GitHub Security and quality is 0 |
| Traky12 integration | 16 remote repositories classified; 14 governed README PRs open and traceable | Protected main branches require review/checks; forks are excluded |

### Current boundary

Claims remain evidence-scoped. Do not describe this repository as production-validated, best-in-class, independently reviewed, commercially superior or N5/N6 unless the corresponding passport, evidence package, signed review and gate record are present. The open-gate register is authoritative for vault approval, GitHub security access, remote alerts, production restore, diagnostics and external validation.

### Traceability

| Artifact | Purpose |
|---|---|
| `TRAKY12-README-INVENTORY.json` | Repository surface, sensitivity and evidence classification |
| `Competitive Capability Passport` | Capability state, provenance, maturity and forbidden claims |
| `S-001 benchmark` | Reproducible comparison protocol and promotion boundary |
| P0/P1/P2 roadmap | Exit criteria and rollback responsibility |
| `Dashboard checkpoint` | Restorable implementation state and validation result |
| `CASTUO-SYSTEM-OPERATING-INDEX.md` | Master operating plan, task registry, gates, claims boundary, checkpoints and repository traceability |

The master operating index is available at [`docs/governance/CASTUO-SYSTEM-OPERATING-INDEX.md`](https://github.com/Traky12/Castuo-system/blob/main/docs/governance/CASTUO-SYSTEM-OPERATING-INDEX.md). This block is a governed integration reference. Repository-specific build, deployment, security and operational instructions remain authoritative in the rest of this README.
<!-- CASTUO-GOVERNED-README-BLOCK:END -->
