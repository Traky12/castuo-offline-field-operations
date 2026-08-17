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

<!-- CASTUO:PUBLIC-SURFACE -->
## CASTÚO integration boundary

This repository exposes only a bounded public integration surface. Its role, current state and claims are subordinate to the `Traky12/castuo-evolution` control plane.

This repository does not by itself claim production operation, certification, independent validation, customer contracts, revenue, autonomous authority, global federation or legal compliance. Do not publish secrets, credentials, private endpoints, customer data, private evidence or unpublished security findings.

See [`docs/CASTUO_PUBLIC_SURFACE.md`](docs/CASTUO_PUBLIC_SURFACE.md) for the public boundary. `Claim != Evidence`; `CURRENT != TARGET`; promotion requires control-plane authorization.
<!-- CASTUO:PUBLIC-SURFACE-END -->

<!-- CASTUO:PUBLIC-GOVERNANCE:START -->
## CASTÚO-SYSTEM governed public surface

**Repository role:** `FIELD APPLICATION`<br>
**Scope statement:** Bounded field workflows, offline continuity and evidence export; measured operation remains pending.
**Public state:** `EVIDENCE-SCOPED · STAGING-CANDIDATE · PROMOTION-BLOCKED`

This repository is a governed surface of CASTÚO-SYSTEM. The canonical governance source is [`castuo-evolution`](https://github.com/Traky12/castuo-evolution); the public Knowledge & Evidence Index is a read-model. Repository activity, a README, commit, pull request or passing local workflow does not by itself establish remote operation, production, certification, customers, revenue or regulatory conformity.

**Boundary:** Field capability / pilot evidence pending.

| Layer | Public meaning |
|---|---|
| Documented | Scope, design or policy is described. |
| Implemented local | Implementation exists within declared local scope. |
| Tested local | A local test passed within its declared scope. |
| Evidence-scoped | Evidence surfaces and limitations are identified. |
| Operational / production | `NOT_CLAIMED` unless separately evidenced and reviewed. |

For public navigation use the [Traky12 profile](https://github.com/Traky12/Traky12), the [Evidence Center](https://github.com/Traky12/Traky12/tree/proof-matrix-profile/docs/evidence) and the [Public Knowledge & Evidence Index](https://castuo-system.es/).

`Identity != Documentation != Evidence != Execution != Review != Promotion`
<!-- CASTUO:PUBLIC-GOVERNANCE:END -->
