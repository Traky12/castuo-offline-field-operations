<!-- CASTUO:BRAND:START -->
<p align="center">
  <img src="https://raw.githubusercontent.com/Traky12/Traky12/main/assets/brand/castuo-system-logo-square.jpg" alt="CASTÚO-SYSTEM official logo" width="180" />
</p>
<!-- CASTUO:BRAND:END -->

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
     ├── castuo-evidence (Public Fabric)
     │      Evidence verification surface
     │
     ├── CASTÚO-SYSTEM (Private Core)
     │      Upstream sync target
     │
     └── castuo-evolution (Control Plane)
            Governance & SSOT
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
[← Profile](https://github.com/Traky12) | [→ Evidence](https://github.com/Traky12/castuo-evidence) | [→ Governance](https://github.com/Traky12/castuo-evolution) | [→ Agro Edge](https://github.com/Traky12/castuo-agro-edge)

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

<!-- CASTUO-PUBLIC-INTEGRATION:START -->
## CASTÚO-SYSTEM public integration

**Repository role:** Field continuity.

Continuidad offline/field; no claim de despliegue productivo. The public reference surface is governed by the [Traky12 profile](https://github.com/Traky12/Traky12) and the [castuo-evolution control plane](https://github.com/Traky12/castuo-evolution). Current ecosystem status is documented in the [integration status](https://github.com/Traky12/castuo-evolution/blob/main/docs/GITHUB_INTEGRATION_STATUS_2026-08-16.md) and [blocker register](https://github.com/Traky12/castuo-evolution/blob/main/docs/GITHUB_INTEGRATION_BLOCKERS_2026-08-16.md).

> Identity is not evidence. Repository activity is not operational truth. No production, certification, legal-compliance, customer, revenue, continuous-operation or federation claim is implied by this README block.
<!-- CASTUO-PUBLIC-INTEGRATION:END -->

<!-- CASTUO:ECOSYSTEM-INTEGRATION:START -->
## CASTÚO-SYSTEM ecosystem integration

**Declared role:** Operaciones de campo offline-first y continuidad territorial.

This repository is connected to the CASTÚO-SYSTEM ecosystem through the [Traky12 public profile](https://github.com/Traky12), the [Castuo-system core](https://github.com/Traky12/Castuo-system), and the [castuo-evolution governance control plane](https://github.com/Traky12/castuo-evolution). The canonical map defines relationships; repository activity does not become operational evidence by itself.

**Current bounded state:** GREEN-STAGING-CANDIDATE · EVIDENCE-SCOPED · PROMOTION-BLOCKED, unless this repository's own metadata declares a narrower state. Identity, implementation, tests, evidence, review and promotion remain separate dimensions.

**Evidence boundary:** This README does not claim production operation, certification, legal compliance, independent validation, customer traction, revenue, continuous operation, autonomous authority or federation. Such claims require scope-bound provenance, reproducible artifacts, security review, human review and an explicit promotion decision.

**Canonical references:** [CASTÚO-REPOSITORY-STANDARD-V1.0](https://github.com/Traky12/Castuo-system/blob/main/README.md), [CASTÚO public claim boundary](https://github.com/Traky12/Traky12/blob/main/PUBLIC_CLAIM_BOUNDARY.md), and the [public profile](https://github.com/Traky12/Traky12).
<!-- CASTUO:ECOSYSTEM-INTEGRATION:END -->
