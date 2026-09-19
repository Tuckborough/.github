# Tuckborough & Cleva — Contributing & Engineering Governance Handbook

Welcome to the **Tuckborough** development team! This handbook defines the engineering standards, role workflows, Kanban practices, and DevSecOps policies for all contributors working on **Cleva**.

---

## 1. Safety & Branching Policy (DevSecOps Standard)

> [!CAUTION]
> **Direct pushes to `main` are strictly prohibited across all organization repositories.**
> All changes must be introduced via topic branches and pull requests subject to automated DevSecOps validation.

### Branch Naming Conventions
- **`feat/<issue-id>-<short-description>`**: New user story or feature (e.g. `feat/1-brief-metrics`)
- **`fix/<issue-id>-<short-description>`**: Defect remediation (e.g. `fix/14-centris-sync-timeout`)
- **`enabler/<issue-id>-<short-description>`**: Technical foundation, architecture spike, or CI update
- **`docs/<issue-id>-<short-description>`**: Documentation or business plan updates
- **`sec/<issue-id>-<short-description>`**: Vulnerability fix or security hardening

---

## 2. Multi-Role Collaboration Framework

We distinguish work items and tasks using a structured taxonomy to keep the Kanban board actionable:

### Role Responsibilities

| Role | Focus Area | Primary Responsibilities |
| :--- | :--- | :--- |
| **🎨 Design** (`role:design`) | Figma & UX | Maintain master Figma frames ([Cleva — Écrans de l'app](https://www.figma.com/design/vJVrwyLcEqnQvFjYYBnznh/Cleva-%E2%80%94-%C3%89crans-de-l-app)), design tokens, light/dark modes, and mobile layouts. |
| **💻 Development** (`role:dev`) | Frontend / Backend / Mobile | Implement OpenAPI contracts, UI views, data persistence, and sovereign AI endpoints. |
| **🧪 QA & Testing** (`role:qa`) | Verification & Test Plans | Validate Gherkin acceptance criteria, execute regression testing, verify simulator/device layouts. |
| **🔒 DevOps / SecOps** (`role:devops`) | CI/CD & Security | Maintain reusable pipelines in `cleva-devops`, Semgrep rules, secret scanning, and cloud infrastructure. |
| **📋 Product** (`role:product`) | Backlog & Scope | Author user stories, define business value, prioritize sprints, and ensure Law 25 compliance. |

---

## 3. Work Item Types & Kanban Flow

When opening an issue, GitHub will prompt you to select the appropriate work item form:

1. **💡 Feature / Epic (`type:feature`)**: High-level capability spanning multiple stories or components.
2. **📖 User Story (`type:user-story`)**: End-user capability following "As a... I want... So that..." with Gherkin acceptance criteria.
3. **⚙️ Enabler / Spike (`type:enabler`)**: Technical debt, architecture changes, or DevOps pipeline foundations.
4. **🐛 Bug Report (`type:bug`)**: Reproducible defect with environment, severity, logs, and steps to reproduce.
5. **📋 Task / Chore (`type:task`)**: Routine maintenance, dependency bumps, or small technical tasks.

### Lifecycle Stages on the Kanban Board:
```
[status:backlog] ➔ [status:ready-for-dev] ➔ [status:in-progress] ➔ [status:in-review] ➔ [status:ready-for-qa] ➔ [status:in-qa] ➔ [status:done]
```

---

## 4. Definition of Ready (DoR) vs Definition of Done (DoD)

### Definition of Ready (DoR) — Before Dev Begins:
- [ ] Figma reference link attached (desktop + mobile layouts, light + dark modes).
- [ ] Gherkin acceptance criteria defined (`Given... When... Then...`).
- [ ] API contract or schema established (if backend integration required).
- [ ] Domain gate impact declared (Law 25 PII or AI requirements identified).

### Definition of Done (DoD) — Before Merge to `main`:
- [ ] Peer code review approved by at least 1 team member.
- [ ] Unit & integration tests pass.
- [ ] Reusable DevSecOps pipeline is 100% green (Secrets, SAST, SCA, Compliance).
- [ ] QA validation passed against acceptance criteria on target devices.
- [ ] Domain Gates signed off in the Pull Request template.

---

## 5. Mandatory Domain Gates

Cleva operates in the regulated Québec real estate market:

1. **🏛️ Québec Law 25 & Privacy Gate**:
   - Explicit client consent must be recorded before collecting personal information.
   - All uploaded documents (GED) must be encrypted at rest and in transit.
   - Social Insurance Numbers (NAS/SIN) and sensitive financial details must never appear in unmasked logs.
2. **🤖 Sovereign AI Safety Gate**:
   - All LLM inference calls must route through the sovereign gateway (preserving Canadian data sovereignty).
   - Every AI-generated communication (client email drafts, briefing summaries) requires **human-in-the-loop validation** by the broker before transmission.
3. **🏠 Centris MLS Gate**:
   - Automated listing alerts must strictly respect Centris/MLS API rate limits and data distribution licensing.

---

## 6. Centralized DevSecOps Workflows (`cleva-devops`)

Security pipelines are maintained centrally in `Tuckborough/cleva-devops`.
To activate security scanning on a new repository, add this 10-line caller in `.github/workflows/ci.yml`:

```yaml
name: CI & DevSecOps Gate

on:
  pull_request:
    branches: [ main ]
  push:
    branches: [ main ]

jobs:
  security-gate:
    uses: Tuckborough/cleva-devops/.github/workflows/reusable-devsecops.yml@main
    with:
      language: 'generic' # or python / typescript
    secrets: inherit
```

---

## 7. Commit Standards (Conventional Commits)

Commit messages must follow the Conventional Commits specification:

```
<type>(<scope>): <summary>

[optional body]

[optional issue reference: closes #12]
```

- **`feat`**: New feature
- **`fix`**: Bug fix
- **`enabler`**: Architecture or tooling
- **`docs`**: Documentation changes
- **`refactor`**: Code restructuring
- **`test`**: Testing changes
- **`sec`**: Security patch
