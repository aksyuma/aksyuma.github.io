# CI/CD Pipeline Gap Analysis — Senior SRE Review

**Reviewer:** Senior SRE  
**Date:** 2026-08-07  
**Repository:** `aksyuma/aksyuma.github.io`  
**Pipeline:** GitHub Actions (CI → Deploy → Release)  
**Overall maturity:** Early-stage with strong security foundations, significant reliability and observability gaps

---

## Executive Summary

The pipeline demonstrates above-average security hygiene for a personal project: SHA-pinned actions, least-privilege permissions, SBOM generation, dependency auditing, and secret scanning. However, it has critical reliability gaps that would fail an SRE production readiness review. The most significant issue is the **deploy job rebuilds the artifact** rather than promoting the CI-validated output, meaning the deployed site is not provably the same artifact that passed checks.

**Risk rating:** Medium — acceptable for a personal static site, not acceptable for a platform aspiring to be an engineering knowledge base with integrity guarantees.

---

## Findings

### 🔴 Critical (P0) — Must fix before claiming trusted publication

| # | Finding | Impact | Recommendation |
|---|---------|--------|----------------|
| 1 | **Deploy rebuilds instead of promoting CI artifact** | The deployed site may differ from what CI validated. Any dependency resolution difference, build-time side effect, or runner state change produces a non-identical output. Violates artifact integrity principle. | CI should `upload-artifact` the built `dist/` directory. Deploy should download that exact artifact and publish it. Never rebuild. |
| 2 | **No artifact digest verification** | No checksum or digest links the CI build output to the deployed artifact. There is no way to prove what was deployed matches what was tested. | Generate and record a SHA-256 of the `dist/` directory or tarball. Compare at deploy time. Store in release notes. |
| 3 | **Known vulnerabilities unresolved (Astro 4.16.19)** | Three moderate XSS CVEs remain open. `continue-on-error: true` means the pipeline silently passes despite known vulnerabilities. | Upgrade Astro to ≥7.0.10. Remove `continue-on-error` from dependency audit once baseline is clean, or implement an allowlist for triaged findings. |

---

### 🟡 High (P1) — Should fix within current iteration

| # | Finding | Impact | Recommendation |
|---|---------|--------|----------------|
| 4 | **`workflow_run` coupling is fragile** | Deploy triggers on CI completion (any conclusion if dispatched manually). `workflow_run` events carry the context of the _triggering_ workflow, not the current commit. Race conditions are possible with rapid pushes. | Consider a single workflow with dependent jobs (`needs: build`) or use the `workflow_call` reusable workflow pattern for clearer data flow. |
| 5 | **No smoke test after deploy** | Deployment succeeds without verifying the site actually renders. A broken build that produces an empty `dist/` would still "deploy successfully." | Add a post-deploy curl check: `curl -sf https://aksyuma.github.io/ \| grep -q '<html'` or use a lightweight synthetic monitor. |
| 6 | **No rollback mechanism documented** | If a bad deploy occurs, there is no documented procedure to revert to the previous Pages artifact. | Document rollback: re-run previous successful deploy workflow, or maintain last-known-good artifact with retention. |
| 7 | **Security job failures are invisible** | Both `continue-on-error: true` steps mean the security job always shows green. There is no alert, annotation, or issue creation on failure. Findings are silently buried in logs. | Use `continue-on-error` but add a follow-up step that creates a GitHub annotation or opens an issue when the audit exits non-zero. Alternatively, use a separate required status check that warns but doesn't block. |
| 8 | **No caching** | Every job installs dependencies from scratch. For a Yarn PnP project with a lockfile, this wastes ~30s per job × 3 parallel runs. | Add `actions/cache` for Yarn's cache directory, or use `setup-node` with `cache: 'yarn'`. |
| 9 | **Node version not pinned with `.node-version`** | Node 22 is specified in workflows but not enforced locally. A local build with a different Node version could produce different output. | Add `.node-version` file with `22.x`. Reference it in workflows via `node-version-file`. |

---

### 🟢 Medium (P2) — Improve when capacity allows

| # | Finding | Impact | Recommendation |
|---|---------|--------|----------------|
| 10 | **No link checking** | Internal and external links are not validated. Broken navigation (`/about`, `/projects`) ships to production without detection. | Add `lychee-action` or equivalent link checker as a CI step. |
| 11 | **No accessibility checks** | The site has no automated accessibility testing. WCAG violations ship undetected. | Add `pa11y-ci` or `axe-core` against the built site. |
| 12 | **No build performance tracking** | No visibility into build duration trends. A dependency that doubles build time would go unnoticed. | GitHub Actions provides timing; consider adding a step that annotates duration or fails if build exceeds a threshold (e.g., 120s). |
| 13 | **Semantic-release on a static site** | Versioning a documentation site with semver creates misleading release signals. A typo fix triggers the same release machinery as a new article. | Evaluate whether releases add value. If retained, configure commit types so only `feat`/`fix` trigger releases, not `docs` or `chore`. |
| 14 | **SBOM not attached to releases** | The SBOM is a workflow artifact (expires in 90 days) but not attached to the GitHub Release, making long-term provenance inaccessible. | Configure `anchore/sbom-action` with `upload-release-assets: true` or copy the SBOM into the release step. |
| 15 | **Dependabot PRs have no auto-merge policy** | Dependabot opens PRs but they sit unmerged, accumulating. For a single maintainer this creates backlog fatigue. | Enable auto-merge for patch-level updates that pass CI, using `dependabot-auto-merge` action or GitHub's built-in auto-merge with branch protection. |
| 16 | **No timeout on jobs** | A hung build would consume runner minutes indefinitely (up to GitHub's 6h default). | Add `timeout-minutes: 10` to each job. |

---

## What's Done Well

| Practice | Assessment |
|----------|-----------|
| Actions pinned to commit SHA | ✅ Excellent — prevents supply-chain hijacking via tag mutation |
| `permissions: {}` default with job-level grants | ✅ Best practice — zero-trust baseline |
| Inline permission justification comments | ✅ Above average — aids audit and onboarding |
| SBOM generation (SPDX) | ✅ Forward-thinking — few personal projects do this |
| Secret scanning (gitleaks) | ✅ Good — catches credential leaks in PRs |
| Dependabot for both npm and Actions | ✅ Good — automated supply-chain hygiene |
| Concurrency control on deploy | ✅ Prevents parallel deploys clobbering each other |
| Immutable install (`yarn install --immutable`) | ✅ Ensures lockfile integrity |
| Top-level workflow annotations | ✅ Good documentation practice |

---

## Recommended Priority Order

```
P0 (this sprint):
  1. Promote artifact instead of rebuilding in deploy
  2. Record artifact digest
  3. Upgrade Astro to resolve CVEs

P1 (next sprint):
  4. Add post-deploy smoke test
  5. Surface security findings visibly (annotations/issues)
  6. Add caching
  7. Pin Node via .node-version file
  8. Document rollback procedure

P2 (backlog):
  9.  Add link checker
  10. Add accessibility checks
  11. Attach SBOM to releases
  12. Add job timeouts
  13. Evaluate semantic-release value
```

---

## Architecture Recommendation

The current three-workflow chain should evolve toward:

```
┌─────────────────────────────────────────────────────────┐
│  CI (on push/PR)                                        │
│  ┌─────────┐  ┌──────────┐  ┌──────┐  ┌─────────────┐ │
│  │  Build  │→ │  Test /  │→ │ SBOM │→ │  Upload     │ │
│  │         │  │  Lint    │  │      │  │  Artifact   │ │
│  └─────────┘  └──────────┘  └──────┘  └──────┬──────┘ │
│                                                │        │
│  ┌──────────────────────────────────────────┐  │        │
│  │  Security (parallel)                     │  │        │
│  │  audit + gitleaks + link-check           │  │        │
│  └──────────────────────────────────────────┘  │        │
└────────────────────────────────────────────────┼────────┘
                                                 │
┌────────────────────────────────────────────────▼────────┐
│  Deploy (on main, after CI passes)                      │
│  ┌───────────────┐  ┌──────────────┐  ┌─────────────┐  │
│  │  Download     │→ │  Verify      │→ │  Publish    │  │
│  │  Artifact     │  │  Digest      │  │  to Pages   │  │
│  └───────────────┘  └──────────────┘  └──────┬──────┘  │
│                                               │         │
│  ┌──────────────────────────────────────────┐ │         │
│  │  Post-deploy smoke test                  │ │         │
│  └──────────────────────────────────────────┘ │         │
└───────────────────────────────────────────────┼─────────┘
                                                │
┌───────────────────────────────────────────────▼─────────┐
│  Release (optional, on tag or manual)                   │
│  ┌───────────────────────────────────────────────────┐  │
│  │  Tag + Changelog + SBOM attached to release       │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

**Key principle:** Build once, verify, promote the exact artifact. Never rebuild between stages.

---

## Conclusion

The pipeline is in the **top 20% for security posture** among personal/small-team projects but in the **bottom 40% for reliability engineering**. The single most impactful change is eliminating the duplicate build in the deploy step. This one fix transforms the pipeline from "builds and ships" to "validates and promotes" — which is the foundation of trustworthy delivery.

The security controls already in place (SHA pinning, SBOM, least-privilege, secret scanning) are genuinely good engineering. The gap is making them _actionable_ — surfacing findings instead of silencing them, and connecting the artifact chain from build to deployment with cryptographic proof.

---

*Generated as part of the Engineering Platform Programme — Phase 2 security hardening review.*
