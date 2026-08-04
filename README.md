# CASTÚO-SYSTEM Capability Specification
## Offline-First Field Operations

## Capability Metadata

- **Capability ID:** CAP-OFFLINE-FIRST-001
- **Version:** 1.4
- **Status:** N2 – Prepared
- **Target Maturity:** N5 – Pilot
- **Owner:** CASTÚO-SYSTEM
- **Domain:**
  - Edge Computing
  - AI
  - GIS
  - Communications
- **Dependencies:**
  - SABIONDA
  - Evidence Center
  - Digital Thread
  - Edge Gateway
- **Related Capabilities:**
  - MQTT Ingestion
  - Telemetry
  - Edge Synchronization
  - Asset Registry

## Capability Overview

Offline-First Field Operations enables resilient digital operations in environments where connectivity cannot be guaranteed.

The capability integrates:

- Offline Knowledge.
- Local AI.
- Offline GIS.
- Mesh Communications.
- Edge Computing.
- Evidence Collection.
- Digital Thread.

This ensures operational continuity while preserving traceability, reproducibility, and governance.

This capability is designed following an Evidence-Driven Engineering approach.

## Design Principles

The capability is designed according to the following principles:

- Offline-First by Default.
- Local Processing before Cloud.
- Human-in-the-Loop.
- Open Standards.
- Modular Components.
- Evidence Before Claims.
- Progressive Synchronization.
- Secure by Design.
- Privacy by Design.
- Observability by Default.

## Capability Canvas

- **Capability:** Offline-First Field Operations.
- **Purpose:** Resilient digital operations without guaranteed connectivity.
- **Owner:** CASTÚO-SYSTEM.
- **Consumers:** Field teams, rural deployments, distributed infrastructures.
- **Dependencies:** SABIONDA, Evidence Center, Digital Thread, Edge Gateway.
- **Inputs:** Documentation, GPS position, telemetry, user requests, sensor observations.
- **Outputs:** AI responses, navigation assistance, mesh messages, evidence packages, synchronization jobs.
- **KPIs:** Offline knowledge availability, AI resolution rate, message delivery rate, offline autonomy, synchronization rate, evidence completeness.
- **Evidence:** Logs, pilot reports, metrics, GPS tracks, release notes.
- **Risks:** Connectivity loss, battery depletion, GPS degradation, model hallucinations, evidence corruption.
- **Lifecycle:** Prepared → Deployed → Active → Degraded → Recovering → Synchronized → Archived.
- **Maturity Target:** N5 – Pilot.

## Context

Offline-First Field Operations is one capability within the CASTÚO-SYSTEM capability catalogue.

It depends on several ecosystem services but may also be deployed independently in isolated field environments.

The capability contributes to ecosystem resilience by ensuring continuity of operations when cloud connectivity cannot be guaranteed.

## Purpose

The Offline-First Field Operations capability enables CASTÚO-SYSTEM to provide resilient digital infrastructure for environments where Internet connectivity is intermittent, unavailable, or intentionally disabled.

Rather than relying on continuous cloud connectivity, this capability combines local knowledge, artificial intelligence, offline cartography, resilient communications, and evidence-driven engineering into a unified operational architecture.

Its objective is to support field teams, rural territories, and distributed infrastructures while maintaining technical traceability, operational continuity, and verifiable evidence.

## Capability Contract

### Inputs

- Offline documentation.
- GPS position.
- Local telemetry.
- User requests.
- Sensor observations.

### Outputs

- AI responses.
- Navigation assistance.
- Mesh messages.
- Evidence packages.
- Synchronization jobs.

### Dependencies

- SABIONDA.
- Evidence Center.
- Digital Thread.
- Edge Gateway.
- Kiwix.
- Organic Maps.
- Meshtastic.

### Events

- CapabilityStarted.
- WaypointCreated.
- EvidenceGenerated.
- SynchronizationCompleted.
- NodeDisconnected.
- MeshRecovered.
- PilotFinished.

### Failure Conditions

- Loss of Internet connectivity.
- Partial GPS availability.
- LoRa packet loss.
- Local AI unavailable.
- Storage exhaustion.
- Battery depletion.

### Recovery Behaviour

- Continue in degraded mode whenever possible.
- Queue evidence until synchronization is available.
- Preserve local records.
- Resume normal operation after connectivity returns.

### Quality Attributes

- Availability.
- Integrity.
- Security.
- Portability.
- Observability.
- Reproducibility.
- Interoperability.

## Capability Boundaries

The capability owns:

- Offline knowledge retrieval.
- Offline AI assistance.
- Local field navigation.
- Field messaging.
- Evidence generation.

The capability depends on:

- Edge hardware.
- Local storage.
- Power availability.

The capability does not own:

- Cloud analytics.
- Long-term archival.
- Emergency communications.

## Scope

This capability defines how CASTÚO-SYSTEM operates when cloud connectivity is unavailable or intentionally disabled.

It specifies the interaction between local knowledge repositories, AI-assisted support, offline cartography, resilient communications, evidence collection, and synchronization.

It does not prescribe specific hardware vendors or deployment environments.

## Out of Scope

This capability does not:

- Replace emergency communication systems.
- Replace GIS servers.
- Replace cloud infrastructure.
- Replace human operational decisions.
- Guarantee connectivity in every environment.

## Assumptions

This specification assumes:

- Portable edge hardware is available.
- Offline documentation is available.
- A local AI model is installed.
- LoRa infrastructure is available where needed.
- Operators are trained in field procedures.

## Constraints

Current implementation may depend on:

- Available storage.
- Battery capacity.
- Local CPU performance.
- Radio regulations.
- Local cartography availability.

## Strategic Objectives

This capability is designed to:

- Provide access to operational knowledge without Internet connectivity.
- Support AI-assisted decision-making using local knowledge bases.
- Enable offline navigation and asset localisation.
- Maintain communication through resilient off-grid networks.
- Collect operational evidence for validation and auditing.
- Synchronise information once connectivity becomes available.
- Improve resilience during degraded network conditions.

## Functional Requirements

The capability shall:

- Provide offline access to documentation.
- Answer operational questions using local AI.
- Support navigation without Internet.
- Exchange messages between field nodes.
- Maintain local evidence.
- Synchronize evidence when connectivity returns.
- Maintain traceability through the Digital Thread.

## Non-Functional Requirements

| Requirement | Target |
|---|---:|
| Availability | >=99% |
| Offline Autonomy | >=24 hours |
| Integrity | Evidence shall be immutable. |
| Security | Least privilege. |
| Performance | AI response <3 s. |
| Interoperability | Open standards. |
| Maintainability | Modular architecture. |
| Portability | Linux ARM64 and x86. |
| Observability | Metrics exposed through Prometheus. |
| Reproducibility | Every deployment linked to a Release. |

## Interfaces

| Interface | Type | Description |
|---|---|---|
| SABIONDA | Internal | AI assistance and decision support. |
| Evidence Center | Internal | Evidence repository and governance layer. |
| MQTT | Protocol | Telemetry and event exchange. |
| Meshtastic | Protocol | Mesh communication for off-grid coordination. |
| Organic Maps | External | Offline navigation and field reference. |
| Kiwix | External | Offline knowledge and documentation. |
| Edge Gateway | Internal | Synchronisation and local integration. |

## Interaction View

```text
Operator
   │
   ▼
Organic Maps
   │
Waypoint
   │
   ▼
SABIONDA
   │
Offline RAG
   │
   ▼
Kiwix
   │
Decision
   │
   ▼
Evidence Center
   │
Release
```

## Runtime View

```text
Portable Node

├── SABIONDA
├── Local LLM
├── Kiwix
├── Organic Maps
├── Meshtastic
├── MQTT Broker
├── Edge Gateway
└── Evidence Cache
```

## Deployment Profiles

### Profile A — Single Laptop

- Kiwix.
- Local AI.
- Organic Maps.

### Profile B — Vehicle Node

- Edge Gateway.
- Meshtastic.
- Sensors.

### Profile C — Village Deployment

- Multiple Edge Nodes.
- LoRaWAN.
- Central Evidence Server.

## Data Flow

```text
Knowledge
   ↓
RAG
   ↓
AI
   ↓
Decision
   ↓
Operator
   ↓
Evidence
   ↓
Synchronization
```

## Security View

The capability shall provide:

- Authentication.
- Authorization.
- Encryption.
- Evidence hashing.
- Local secrets management.
- Device identity.
- Audit logging.

## Capability API

The capability shall expose the following service intents:

- Offline Query.
- Offline Navigation.
- Waypoint Registration.
- Evidence Upload.
- Synchronization.
- Capability Health.

## Events

- CapabilityStarted.
- WaypointCreated.
- EvidenceGenerated.
- SynchronizationCompleted.
- NodeDisconnected.
- MeshRecovered.
- PilotFinished.

## Sequence Diagram

```text
Operator
   ↓
Organic Maps
   ↓
SABIONDA
   ↓
Offline RAG
   ↓
Evidence Center
   ↓
Release
```

## Operational Modes

```text
ONLINE
   ↓
HYBRID
   ↓
OFFLINE
   ↓
RECOVERY
   ↓
SYNCHRONIZED
```

## Failure Modes

Possible degraded conditions include:

- Loss of Internet connectivity.
- Partial GPS availability.
- LoRa packet loss.
- Local AI unavailable.
- Storage exhaustion.
- Battery depletion.

The capability shall continue operating whenever possible using degraded-mode procedures.

## Capability States

```text
PREPARED
   ↓
DEPLOYED
   ↓
ACTIVE
   ↓
DEGRADED
   ↓
RECOVERING
   ↓
SYNCHRONIZED
   ↓
ARCHIVED
```

## Capability Components

### Knowledge Layer

The Knowledge Layer provides access to technical documentation, operational manuals, and educational resources using Kiwix and curated offline repositories.

Primary objective:
Ensure knowledge availability without cloud dependency.

### AI Assistance Layer

The AI Assistance Layer provides contextual assistance through SABIONDA, local language models, and Offline RAG.

The AI layer is intended to:
- Search documentation.
- Summarise procedures.
- Assist troubleshooting.
- Support operational decisions.

Human supervision remains mandatory for critical decisions.

### Field Operations Layer

The Field Operations Layer supports navigation and operational awareness through:

- Organic Maps.
- GIS datasets.
- Asset inventories.
- Offline waypoints.
- Environmental observations.

### Communication Layer

The Communication Layer provides resilient communications through:

- Meshtastic.
- LoRa.
- MQTT.
- Edge gateways.

This layer maintains basic operational coordination when Internet services are unavailable.

### Evidence & Governance Layer

All operational activities generate verifiable evidence through the Evidence Center.

Evidence includes:

- Deployment records.
- Operational logs.
- KPI reports.
- Screenshots.
- GPS tracks.
- Photographs.
- Pilot documentation.
- Validation reports.

Every evidence package is linked to a Release and follows the Digital Thread.

## Operational Workflow

```text
Design
   ↓
Implementation
   ↓
Tests
   ↓
CI
   ↓
Deployment
   ↓
Pilot
   ↓
Evidence
   ↓
Release
   ↓
Monitoring
   ↓
Continuous Improvement
```

## Pilot Lifecycle

### Phase 1 — Preparation
- Define operational objectives.
- Select pilot area.
- Prepare offline documentation.
- Download cartography.
- Configure Edge devices.
- Configure Meshtastic network.
- Prepare Evidence Center dossier.

### Phase 2 — Controlled Pilot
- Deploy portable node.
- Validate Offline AI.
- Verify navigation.
- Test communications.
- Register operational events.
- Collect telemetry.

### Phase 3 — Evidence Collection
Produce:

- Logs.
- Metrics.
- GPS tracks.
- Screenshots.
- Photographs.
- Test reports.
- Pilot report.
- Release notes.

### Phase 4 — Validation
Evaluate:

- Offline knowledge accessibility.
- AI usefulness.
- Navigation performance.
- Communication reliability.
- Operational continuity.
- Evidence completeness.
- User feedback.

## Success Criteria

The capability is considered successfully validated when:

- Pilot objectives are achieved.
- Mandatory KPIs meet target values.
- Evidence dossier is complete.
- Release is reproducible.
- Traceability is complete.

## KPI Framework

| KPI | Target | Source | Evidence |
|---|---:|---|---|
| Offline Knowledge Availability | >=95% | Kiwix logs | KPI Report |
| Offline AI Resolution Rate | >=90% | SABIONDA | Metrics |
| Message Delivery Rate | >=99.5% | Meshtastic | Logs |
| Offline Autonomy | >=24 h | Edge Gateway | Pilot Report |
| Waypoint Synchronization | >=99% | Sync Service | Evidence Center |
| Evidence Completeness | 100% | Validation | Release |

## Evidence Quality Levels

| Level | Description |
|---|---|
| EQ1 | Screenshot |
| EQ2 | Logs |
| EQ3 | Automated Test |
| EQ4 | KPI Measurement |
| EQ5 | Independent Validation |
| EQ6 | Continuous Monitoring |

## Evidence Requirements

| Artefact | Mandatory |
|---|---|
| Tests | ✅ |
| CI | ✅ |
| Pilot Protocol | ✅ |
| KPI Report | ✅ |
| Screenshots | Optional |
| Telemetry | ✅ |
| Release | ✅ |

## Traceability Matrix

| Requirement | KPI | Evidence |
|---|---|---|
| Offline AI | KPI-002 | Pilot Report |
| Offline Navigation | KPI-005 | GPS Track |
| Communications | KPI-003 | Logs |
| Synchronization | KPI-006 | Metrics |
| Evidence Completeness | KPI-007 | Release |

## Architectural Decisions

| ADR | Decision |
|---|---|
| ADR-001 | Offline-first architecture. |
| ADR-002 | Evidence Center as source of truth. |
| ADR-003 | Local AI before Cloud AI. |
| ADR-004 | OpenStreetMap as mapping base. |
| ADR-005 | Meshtastic for off-grid communication. |

## Capability Roadmap

```text
N2
   ↓
N3
Implementation Complete
   ↓
N4
Integration Validation
   ↓
N5
Field Pilot
   ↓
N6
Operational Deployment
   ↓
N7
Multi-site Deployment
   ↓
N8
Regional Infrastructure
   ↓
N9
Federated European Network
```

## Capability Metrics

The capability shall monitor technical and operational metrics including:

- CPU usage.
- RAM usage.
- Storage consumption.
- LoRa RSSI.
- Packet loss.
- Battery level.
- AI tokens.
- Inference time.
- Synchronization queue depth.

## Interoperability

The capability is designed to interoperate with:

- OpenStreetMap.
- OpenTelemetry.
- MQTT.
- LoRaWAN.
- Meshtastic.
- GeoJSON.
- GPX.
- Prometheus.
- OpenAPI.

## Governance

Each validated capability shall maintain:

- Capability ID.
- Technical Owner.
- Repository Reference.
- Documentation.
- Automated Tests.
- CI Pipeline.
- Release.
- Deployment.
- Pilot Protocol.
- KPI Report.
- Evidence Dossier.

## Risks

- Loss of offline documentation.
- Hardware failure.
- Battery depletion.
- GPS degradation.
- LoRa interference.
- Model hallucinations.
- Evidence corruption.
- Delayed synchronization.
- Operator misuse.

## Mitigations

- Redundant storage of critical documentation.
- Portable and replaceable hardware.
- Power budgeting and battery monitoring.
- GPS fallback procedures.
- Communication retries and mesh redundancy.
- Human review of AI outputs.
- Hashing and immutability controls for evidence.
- Queue-based delayed synchronization.
- Clear operator procedures and training.

## Alignment

This capability contributes to:

- AI Act.
- Cyber Resilience Act.
- NIS2.
- ISO 27001.
- ISO 25010.
- OpenTelemetry.
- OpenAPI.
- OpenStreetMap.
- Open Standards.

This does not imply certification. It documents reference frameworks considered during design.

## Responsible Technology Principles

### Educational Principles

This capability supports:

- Offline learning.
- Digital autonomy.
- Critical thinking.
- Information verification.
- Data literacy.
- Evidence-based decision-making.
- Territorial digital resilience.

### Ethical Principles

Development follows:

- Privacy by Design.
- Data Minimization.
- Human Oversight.
- Transparent AI Assistance.
- Responsible Location Management.
- Secure-by-Default Engineering.
- Open Standards.
- Interoperability.
- Public Value.

Artificial Intelligence supports operational decisions but never replaces human judgement.

## Digital Thread

Every operational activity should remain traceable.

```text
Capability
   ↓
Implementation
   ↓
Tests
   ↓
CI
   ↓
Artifact
   ↓
Deployment
   ↓
Pilot
   ↓
Measured Results
   ↓
Evidence Center
   ↓
Release
```

## Future Evolution

- Federated RAG.
- Digital Twin integration.
- Autonomous mesh routing.
- Satellite synchronization.
- Distributed evidence ledger.
- Federated AI.
- Autonomous edge clusters.

## Glossary

| Term | Meaning |
|---|---|
| AI | Artificial Intelligence. |
| RAG | Retrieval-Augmented Generation. |
| GIS | Geographic Information System. |
| LLM | Large Language Model. |
| KPI | Key Performance Indicator. |
| ADR | Architecture Decision Record. |
| Digital Thread | End-to-end traceability across lifecycle. |

## Expected Outcomes

This capability strengthens three strategic pillars of CASTÚO-SYSTEM:

- **Offline-First Digital Infrastructure**, ensuring resilient operation in connectivity-constrained environments.
- **Evidence-Driven Field Operations**, enabling measurable, auditable, and reproducible pilot deployments.
- **Digital Territorial Resilience**, integrating local knowledge, artificial intelligence, navigation, communications, and governance into a unified operational model.
