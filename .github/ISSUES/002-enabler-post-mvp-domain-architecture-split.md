---
title: "Enabler: Transition Database Architecture from Monolith to Domain-Segregated Microservices"
labels: ["type:enabler", "status:backlog", "area:architecture", "post-mvp"]
author: "Kevin Nguyen"
date: "2026-10-03"
---

### Enabler Type
Architecture / Data Model / Distributed Systems

### Technical Need / Background
The Cleva backend MVP is built as a modular PostgreSQL monolith with a schema-per-tenant pattern (`tenant_template` cloned per agency/solo broker) and a shared control plane (`public`). While optimal for initial MVP launch velocity and ACID transaction consistency during real estate contracts, scaling the platform post-MVP to multi-agency enterprises across Quebec requires partitioning the monolithic database into domain-segregated data stores to support an eventual microservices architecture.

Specific limitations of staying on a monolith at high scale:
1. **Coupled Database Scaling**: The sovereign LLM document extraction pipeline and vector embeddings (`pgvector`) have significantly higher I/O and compute demands than the CRM and Transaction services.
2. **Blast Radius Isolation**: Failure or heavy migration locks in document staging should never impact broker contact lookups or deadline checks.
3. **Law 25 Audit WORM Compliance**: Legal audit chaining requires append-only write isolation separate from operational CRM workloads.

### Proposed Solution & Approach
Implement the 4-phase physical database and domain segregation playbook documented in [RFC-002](https://github.com/Tuckborough/cleva-backend/blob/main/docs/RFC-002-post-mvp-domain-segregation-playbook-en.md):

1. **Phase 1: Pre-Split Database Softening**:
   - Verify all cross-domain SQL foreign keys are replaced with indexed logical `UUIDv4` references (already compatible with current models).
   - Implement the **Transactional Outbox Pattern** (`outbox_event` table) to eliminate the need for distributed 2-phase commit (2PC) transactions.

2. **Phase 2: Asynchronous Event Decoupling**:
   - Introduce an asynchronous event bus (Apache Kafka or RabbitMQ).
   - Publish domain events for cross-boundary side effects:
     - `GedDocumentCommittedEvent`: Triggers condition generation in the Transaction Service.
     - `ConditionMilestoneUpdatedEvent`: Triggers compliance recalculation.
     - `Law25ErasureRequestedEvent`: Triggers multi-domain PII scrubbing while observing the 6-year OACIQ exemption.

3. **Phase 3: Database Segregation into 5 Domain Stores**:
   - **Identity & Organization DB**: `tenant`, `app_user`, `circle`, `circle_member`, `minimized_card`.
   - **CRM & Client DB**: `contact`, `household`, `buyer_profile`, `consent`.
   - **Transaction & Dossier DB**: `mandate`, `dossier`, `dossier_condition`, `task`, `reminder_action`.
   - **GED & Extraction DB**: `document`, `document_chunk`, `ged_extraction_staging`, plus S3 object storage (OVHcloud Beauharnois, QC).
   - **Compliance & Audit DB**: `audit_event` (SHA-256 chained), `oaciq_checklist_requirement`.

4. **Phase 4: API Gateway & Service Cutover**:
   - Deploy an API Gateway (Traefik / Envoy) routing traffic from iOS (`CleveKit`) and Web clients to individual domain microservices.

### Expected Deliverables
- [ ] Technical Playbook: [RFC-002: Post-MVP Domain-Segregated Architecture Playbook](https://github.com/Tuckborough/cleva-backend/blob/main/docs/RFC-002-post-mvp-domain-segregation-playbook-en.md) (English) & [Version Française](https://github.com/Tuckborough/cleva-backend/blob/main/docs/RFC-002-post-mvp-domain-segregation-playbook.md).
- [ ] Migration scripts: Zero-downtime ETL scripts to extract tenant tables into domain-segregated databases.
- [ ] Outbox CDC worker implementation in `cleva-backend`.
- [ ] Domain event schemas (JSON Schema / Protobuf).
- [ ] Verification tests validating unbroken SHA-256 audit chaining across microservice boundaries.

---
**Author**: Kevin Nguyen
