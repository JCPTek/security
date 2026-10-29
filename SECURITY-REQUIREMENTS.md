# SECURITY-REQUIREMENTS: security

What this repo must do to be secure, and the test that proves each requirement. A requirement with no test is a gap, not a requirement.
Layers: **baseline** (every repo) + **language profiles** (this repo's languages) + **repo-specific** (from what this repo holds and who would attack it).
Source: the consolidated suite file (planning-tools `SECURITY-REQUIREMENTS.md`, 2026-10-28) and the dev-standards template. Update this file when the code's security surface changes, and at least every major release.

**Enforcement legend:** ✅ **Blocks merge** (required check or ruleset rule) · 🟢 **Runs in CI** (not required yet) · 🟡 **Procedural** (planner checks the merge line) · 🔴 **Gap** (missing)

## 1. Baseline security tests (every repo)

| ID | Requirement | Test / check | Standard |
|---|---|---|---|
| B1 | No secrets in code or history | gitleaks (full history, `secrets` job) + GitHub secret scanning with push protection, non-provider and AI patterns | C1 |
| B2 | No new critical/high vulnerable dependency | OSV-Scanner (every lockfile) + the ecosystem audit (npm audit / NuGet vulnerable) | C3, C5 |
| B3 | No code-level weaknesses at CRITICAL/HIGH/ERROR | Semgrep OSS (registry + `security/semgrep` rules, no `--severity` filter, coverage gate) + the CodeQL code-scanning merge rule where CodeQL runs | C5 |
| B4 | Workflows are safe | zizmor (medium+), actionlint, SHA-pinned actions, `permissions: contents: read` default | C4 |
| B5 | IaC and config are safe | Trivy (config/misconfig) | C5 |
| B6 | Dependencies are locked and vetted | Lockfile-presence gate, locked restore, `--ignore-scripts`, force-evaluate ban | C3, J6 |
| B7 | Licenses are allowed | License allowlist check, fail-closed | I1 |
| B8 | Third-party versions are old enough | Release at least 14 days old; 72 h for an advisory fix, with a decision note | C3 (PR #86) |
| B9 | No instance values in the repo | Instance-value guard (no real domain, host or account names) | H1 |
| B10 | Security controls fail closed on bad config | Unit tests for each control's bad-config path | C6 |
| B11 | Authorization is tested negatively | Denial tests for every per-owner / permissioned resource | J7 |
| B12 | Security-relevant events are logged, without secrets | Log-redaction tests, audit-event tests | D1 |
| B13 | Security docs exist | `SECURITY.md`, `SECURITY-RUNBOOK.md` (before first deploy), this file | J5 |
| B14 | Inherited findings have deadlines | Critical 7 days / high 30 days, in the ledger | C5 (PR #86) |
| B15 | Merge only with the security jobs green | Required status checks `secrets` + `security` with no bypass, plus the CodeQL code-scanning merge rule where CodeQL runs | C5 (PR #86) |
| B16 | Scan evidence names tool, version and scope | Merge-line block | C5 (PR #86) |

## 3. Repo-specific security tests

Public repo: no product internals here. The only repo-specific rule: no self-hosted runner may serve this repo (the org's Default runner group has public repositories disabled).

## 4. Enforced today (2026-10-28)

Read from GitHub's active rules on `main`. A rule here blocks the merge; nobody can bypass it unless a bypass is listed.

| Control | Enforced by | Detail |
|---|---|---|
| Changes reach `main` only by pull request | ruleset `require-pr-default-branch` | 0 approvals required |
| Required check `secrets` | ruleset `required-security-checks` | must pass on the PR head |
| Required check `security` | ruleset `required-security-checks` | must pass on the PR head |
| Force-push blocked | ruleset `require-pr-default-branch` | |
| Branch deletion blocked | ruleset `require-pr-default-branch` | |

**Bypass:** none on any rule above.

**Dependabot:** github-actions `/` (14-day cooldown).

## 5. Accepted risks and exceptions

| Risk | Why accepted | Owner | Review date |
|---|---|---|---|
| (none recorded yet) | | | |
