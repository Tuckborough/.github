# Tuckborough & Cleva — Branching & Integration Strategy

**Author**: Kevin Nguyen  
**Status**: Active Standard  
**Applies to**: All Tuckborough repositories (`cleva-backend`, `cleva-ios`, `cleva-web`, `cleva-devops`, `.github`)

---

## 1. Core Philosophy: Trunk-Based Development with Rebase Only

Tuckborough adheres strictly to **Trunk-Based Development** paired with a **Strict Linear Rebase Workflow**. 

All engineers merge into a single trunk (`main`) using short-lived branches. To keep git history clean, bisectable, and auditable under Québec Law 25 and regulatory compliance requirements:
- **Direct pushes to `main` are strictly prohibited across all repositories.**
- **Integration into `main` is strictly done via Pull Request (PR) only.**
- **Merge commits are strictly prohibited.**
- **Branches must stay continuously rebased on top of `origin/main`.**
- **Pull Requests must be merged using Rebase and Merge (or Squash and Merge for multi-commit exploratory spikes).**

```
main:        o---o---o---o---o---o (clean linear line)
                  \         /
branch:            A---B---C  (rebased on top of origin/main before merge)
```

---

## 2. Strict Branch Naming Standard

To ensure consistency across teams and prevent divergent practices, every branch created in any Tuckborough repository **must** strictly adhere to the following convention:

$$\text{Format: } \mathbf{<type>/<issue-id>-<short-slug-in-kebab-case>}$$

### Allowed Branch Types

| Branch Type | Usage / Purpose | Example |
| :--- | :--- | :--- |
| `enabler/` | Technical foundations, database schemas, CI/CD, spikes, architectural enablers | `enabler/1-database-schema-ged-extraction` |
| `feat/` | New user-facing features or user stories | `feat/12-oaciq-client-onboarding` |
| `fix/` | Bug fixes and defect remediations | `fix/45-law25-consent-checkbox-validation` |
| `docs/` | Documentation, architectural RFCs, compliance guides | `docs/8-database-schema-uml-spec` |
| `refactor/` | Code refactoring without behavior or contract change | `refactor/23-extract-pydantic-validators` |
| `chore/` | Tooling maintenance, dependency upgrades, repo hygiene | `chore/99-bump-ruff-and-mypy` |
| `test/` | Adding missing tests, test infrastructure improvements | `test/33-add-oaciq-pdf-fixtures` |
| `sec/` | Urgent security patch or Law 25 compliance fix | `sec/101-sha256-audit-chain-hardening` |

### Rules & Constraints:
1. **Lowercase only**: All characters in the slug must be lowercase alphanumeric and hyphens.
2. **Issue ID required**: Must reference the GitHub Issue number being resolved (e.g., `1-`, `45-`). If no issue exists, create the issue first.
3. **Kebab-case slug**: Use hyphens (`-`) between words, no underscores (`_`), no slashes (`/`) in the slug itself.
4. **Max length**: The slug should be concise (typically 2 to 5 words).

---

## 3. Rebase Workflow Protocol (Step-by-Step)

### Step 1: Create short-lived topic branch from latest `main`
```bash
git checkout main
git pull --rebase origin main
git checkout -b <type>/<issue-id>-<short-slug>
```

### Step 2: Develop and commit using Conventional Commits
Ensure commits are signed and follow conventional commits:
```bash
git add <files>
git commit -m "<type>(<scope>): <summary>"
```
*Always configure git author identity to **Kevin Nguyen** (`kn.nguyen355@gmail.com`).*

### Step 3: Keep your branch up to date using Rebase
Before pushing or opening a PR, rebase onto the latest remote `main`:
```bash
git fetch origin
git rebase origin/main
```
If conflicts occur:
```bash
# Resolve conflicts in editor
git add <resolved-files>
git rebase --continue
# DO NOT run git merge origin/main!
```

### Step 4: User Approval Gate Before Push
> [!IMPORTANT]
> **Always obtain explicit user / lead approval before pushing any branch to remote (`origin`).**
> Never push speculatively, unprompted, or without explicit review and consent.

### Step 5: Push to Topic Branch
```bash
# First push
git push -u origin <type>/<issue-id>-<short-slug>

# If rebased after previous push
git push --force-with-lease origin <type>/<issue-id>-<short-slug>
```

### Step 6: PR Review & Linear Merge
- Open a Pull Request targeting `main`.
- Pass all automated DevSecOps and QA checks.
- On merge, select **Rebase and merge** (or **Squash and merge** for single-feature squashing).
- Delete the remote topic branch immediately after merge.

---

## 4. Anti-Patterns (Strictly Prohibited)

- ❌ **`git merge main` into a topic branch**: Generates messy merge bubbles that obscure git blame and Law 25 auditability.
- ❌ **Direct pushes to `main`**: All changes must go through a branch and PR.
- ❌ **Long-lived branches**: Topic branches should live no longer than 1–2 days.
- ❌ **Arbitrary branch names** (e.g. `test`, `temp`, `kevin/work`, `feature_login`): Breaches automated pipeline triggers and tracking.

---
**Author**: Kevin Nguyen
