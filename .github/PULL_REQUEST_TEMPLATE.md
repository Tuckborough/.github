## 📋 Pull Request Summary

**Linked Issue(s):** Closes #___

### 🎯 Description & Motivation
<!-- Brief summary of what changes are introduced by this PR and why. -->

---

## 👥 Role-Based Work Verification

### 🎨 Design & UI (if UI changed)
- [ ] Matches Figma specification in [Cleva — Écrans de l'app](https://www.figma.com/design/vJVrwyLcEqnQvFjYYBnznh/Cleva-%E2%80%94-%C3%89crans-de-l-app)
- [ ] Responsive across target viewports (Desktop Web, iOS iPhone, Android)
- [ ] Dark mode & Light mode both verified

### 💻 Development & Engineering
- [ ] Unit / integration tests added or updated
- [ ] Local build and TypeScript compilation pass with zero errors
- [ ] No direct push to `main`; branch follows naming conventions (`feat/`, `fix/`, `enabler/`)
- [ ] Clean commit history adhering to Conventional Commits

### 🧪 QA & Verification
- [ ] Tested against Gherkin Acceptance Criteria in linked User Story
- [ ] Edge cases (empty data state, error handling, network timeout) tested
- [ ] Manual smoke test performed on target device / simulator

---

## 🏛️ Mandatory Domain Gates

Before merging, all applicable domain gates must be satisfied:

- [ ] **Québec Law 25 / Privacy Gate**: No unencrypted personal identification data (NAS/SIN, banking details) is stored or logged. Client consent flow respected.
- [ ] **Sovereign AI Gate**: All LLM calls route strictly through the Cleva Sovereign Token Gateway. Human-in-the-loop validation is enforced for broker communications.
- [ ] **Centris MLS Gate**: Listing query intervals adhere to Centris/MLS rate-limiting policies.

---

## 🔒 DevSecOps Quality Checklist

- [ ] Secret scanning: No API keys, PATs, or credentials in commit history
- [ ] SAST: Semgrep security scan is green
- [ ] SCA: No high/critical vulnerable dependencies introduced
