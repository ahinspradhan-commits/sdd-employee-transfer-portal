# Gate 2 Review — Employee Internal Transfer (Readiness / Pre-Code Review)

## Review Metadata

| Field | Value |
|---|---|
| Reviewed scope | Whole repository at `main` @ `b4e8e34` ("Worked on Plan, Spec & Tasks"), against `docs/INT SDD BluePrint - V1.0.pdf` (§12, §13, §18–§22, §27, §28, §30) |
| Reviewer | Gate 2 Reviewer — subhajit.mukherjee@intglobal.com |
| Review date | 2026-10-13 |
| Review type | Manual review |
| Related reviews | `GATE1-BRD-001-2026-09-15.md`, `GATE1-BRD-002-2026-09-28.md` |
| Outcome | **Changes Requested / Not Ready for Gate 2.** There is no implementation to review. Gate 2 should not have been opened and should be re-requested only after the entry conditions in §6 are met. |

## 1. Summary

Gate 2 is the post-generation code review (Blueprint §6, §30). The repository contains **no code and no tests**. It holds only SDD artefacts (BRD, decision register, constitution, specs, traceability, plan, tasks). The last commit adds plan and tasks drafts, and both state that they are not executable until Feature Specification Gate 1 is approved. The Gate 1 specification review has not been performed (`status.md`: "Requested, not yet reviewed").

The Gate 2 checklist therefore cannot be applied. Every checklist line that depends on a diff is **Not Verifiable**, not Pass. Instead, this review lists the findings that would be raised against the current state, including governance and repository-hygiene defects that can be fixed now.

## 2. Gate 2 Checklist (Blueprint §30)

| # | Item | Result | Evidence |
|---|---|---|---|
| 1 | Each AC verified individually against the diff, by ID | **Not verifiable** | No diff. No `src/`. |
| 2 | No AI-attribution in comments or commits | **Pass (partial)** | No code. Commit messages on `main` carry no attribution. Re-check on the first code PR. |
| 3 | Security checklist passed | **Not verifiable** | No code. SEC-09 (CSRF) also has no AC or TC (`traceability.md` §7). |
| 4 | architecture.md / ADR updated if warranted | **Fail** | `architecture.md` says "Not Started". The plan already proposes `RoutingPolicy`, `MasterDataProvider` and others. `decisions/ADR-NNNN-.md` is still the unfilled template. |
| 5 | Tests written first and confirmed Red before Green | **Fail / not evidenced** | No `tests/` content. No RED evidence. `status.md` row "Test-first evidence: Not started". |
| 6 | status.md and spec Status updated same day | **Fail** | `status.md` (last updated 2026-09-28) says Plan and Tasks are "Not started", but commit `b4e8e34` added plan v2.1 and tasks v2.1. |
| 7 | Standard PR review (readability, naming, DRY) | **Not verifiable** | No code. |

## 3. Findings

Severity: **Blocker** = must be fixed before Gate 2 can be requested. **Major** = fix before Gate 1 spec approval or the first PR. **Minor** = hygiene.

### Blockers

| ID | Finding | Blueprint ref | Required action |
|---|---|---|---|
| G2-01 | **No implementation and no tests exist.** `src/` and `tests/` are absent from the tree (they exist only as empty folders in the stale zip). There is nothing to review. | §6, §18, §27 DoD | Do not request Gate 2 until at least one task (T02/T03) is implemented with a RED→GREEN trail. |
| G2-02 | **Principle 1 is not met.** No approved spec exists. Feature Specification Gate 1 is still "Requested". The four slice specs are `Draft`. The constitution is "Proposed v1.0 — approve before Gate 1" and was never approved. | §5.1, §12, §27 DoR | Complete Gate 1 for the specs. Record the constitution approval as a dated, reviewed change. |
| G2-03 | **Gate 2 reviewer was never nominated, yet Gate 2 is being exercised.** `status.md`, `project_context.md`, the plan and all four specs list the Gate 2 reviewer as "TBD". This review's identity should be recorded in those files by the Project Owner. | §12.1, status.md "Governance Correction" | Project Owner records the named, independent reviewer in all artefacts. |
| G2-04 | **Plan is gated and not approved, yet tasks were generated from it.** Plan v2.1 states "not implementation-authorized" until A-001..A-003 are validated, BD-019 is closed and Gate 1 passes. Tasks were generated anyway. Open: A-001..003 (all unvalidated), 0 of 14 business decisions Confirmed, BD-015..019 Open, named approvers TBD. | §10, §13, §21.1 (states may not be skipped) | Keep T02..T10 Pending. Do not progress spec state past "Approved → Plan Drafted → Plan Reviewed → Tasks Generated" until the gates pass. Close A-001..003 and BD-019 first. |

### Major

| ID | Finding | Blueprint ref | Required action |
|---|---|---|---|
| G2-05 | **Plan Constitution Check is entirely unchecked** (7 boxes `[ ]`). Gate 1 requires the plan to be checked line by line against `constitution.md`. | §13, §8 | Complete the check with evidence per line. |
| G2-06 | **Task IDs do not follow `<slug>.T##`.** Tasks use bare `T01..T10`, and the one tasks file is `.tasks.md` instead of `<slug>.tasks.md`. Prompting by identity is not possible across four slices. The same applies to `plans/.plan.md`, `specs/.spec.md`, and `test_cases/.test_cases.md` (template stubs left in place). | §9, §16 | Rename to per-slug files, for example `employee-transfer-request-submission.tasks.md` with `employee-transfer-request-submission.T01`. Delete the stub placeholders. |
| G2-07 | **Per-feature `test_cases/<slug>.test_cases.md` do not exist.** The 38 test cases live inside the specs. `_integration.md` is an empty table, but the plan builds step 6 on it. | §19.1 | Create per-slug test case files and populate `_integration.md` before test-first work starts. |
| G2-08 | **Stale/contradictory status.** `status.md` says Plan "Not started" and Tasks "Not started" while both files exist. `project_context.md` says "Plan, tasks, implementation and Gate 2 evidence have not started". `architecture.md` says "Not Started" while the plan has an architecture section. | §21, §28 (stale `status.md`) | Update all three the same day as the artefact change. |
| G2-09 | **SEC-09 (CSRF) has no AC or TC.** This is a known, tracked gap, but it is a Gate 2 security item and will be unverifiable at code review. | §20, §30 Security Checklist | Close A-001 and add the AC/TC before T08. |
| G2-10 | **Rate-limit decision missing.** The Blueprint security checklist requires an explicit rate-limit decision for every new endpoint. Plan §5 lists three endpoints with no rate-limit decision, even "none, and why". | §8 example, §30 | Add a decision per endpoint to the plan. |
| G2-11 | **`.agent/` control plane is empty.** `int-standards.md`, `auto-log.md`, `code-review.md`, `generate-plan.md`, `generate-tests.md` each contain a single heading line. `.agentignore` is empty. So no always-on coding rules, no ignore rules and no review workflow exist for agents to follow. | §15, §17 | Populate the PHP/MySQL stack standards and the code-review workflow before code generation. Ignore `*.zip`, `docs/`, `*.xlsx`, and the `BRD.md_bkp_*` files in `.agentignore`. |
| G2-12 | **No `.gitignore`.** Binary and backup artefacts are committed freely. | §14 | Add one (see §4). |

### Minor

| ID | Finding | Required action |
|---|---|---|
| G2-14 | `BRD.md_bkp_160926` and `BRD.md_bkp_280926` are manual backup copies in `.ai-context/`. Git history already holds these versions, and the files add stale context for agents. | Remove and rely on git history, or move to `archive/`. |
| G2-15 | `Daily_activity_sheet.xlsx` (58 KB, root) and `docs/Requirement for SDD.docx` are in the repo. The docx is the source requirement and is reasonable to keep. The activity sheet looks like personal time reporting, not a project artefact. Confirm that it contains no personal or client data before it stays in a shared repository. | Confirm the purpose or remove. |
| G2-16 | `README.md` is 38 bytes and has no setup/run/deploy content. | Make it operational (§22). |
| G2-17 | `ADR-NNNN-.md` template stub is committed as if it were an ADR. | Remove or fill. |
| G2-18 | Constitution coverage floor is 70% (stricter alternatives exist for payment-type data). This is acceptable as a project choice, but record a one-line rationale. | Optional. |

## 4. Zip file committed to the repository (`emp-internal-transfer.zip`)

| Fact | Detail |
|---|---|
| File | `emp-internal-transfer.zip`, 3,388,530 bytes, 185 entries |
| Introduced | Commit `94c25bb` "Gate 1 review modification" (author `ahins.pradhan`), alongside a 613-line BRD rewrite and a docs move. The commit message does not mention it. |
| Content | A **stale snapshot of the whole project from 2026-09-09**, including a **full nested `.git/` directory** (config, hooks, objects, logs), the older BRD, the superseded specs, empty `src/` and `tests/`, the source docx and the Blueprint PDF. |
| Why it is a problem | (1) Duplicates every artefact in an outdated version, so any agent or reviewer reading the repo (or grepping it) may pick up superseded specs. The archived v1.0 `employee-internal-transfer.spec.md` is in the zip as the live spec. (2) It violates the single-source-of-truth principle of SDD and the context-scoping rules (§17, §28: stale docs "actively mislead"). (3) A nested `.git/` inside a repo exposes the earlier history, the remote URL, reflogs and commit identities, and it can't be diffed or reviewed. (4) Binary blobs can't be code-reviewed and bloat every clone permanently, even after deletion, because git keeps it in history. (5) The zip's contents were never reviewed at Gate 1. |
| Secrets check | I listed the archive and read only `.git/config` inside it. The remote URL is `https://github.com/ahinspradhan-commits/sdd-employee-transfer-portal.git` with **no embedded credentials or tokens**. I did not extract the archive or scan the other files. Run a secrets scan before the history is shared further. |

**Question to the author (Ahin Subhra Pradhan), please answer in the PR/review thread:**

1. Why was `emp-internal-transfer.zip` committed? Was it intended as a backup, a handover to the Gate 1 reviewer, or an accidental `git add .`?
2. Is anything in the zip *not* already in the repo at a newer version? If not, can it be removed?
3. If it was meant for hand-over, why not share it outside git, or tag a release?

**Required action:** remove the file with `git rm`, add `*.zip` to `.gitignore`, and decide whether history needs rewriting. Because the file is already in remote history, `git rm` alone leaves it in every clone. Rewriting shared history is the owner's decision. I have not done it.

## 5. What is good (to preserve)

1. Governance discipline is strong: Baseline vs Blocked tagging in specs, decisions held in a register, and the constitution limited to engineering constraints.
2. `traceability.md` is complete for the baseline (BR → SR → AC → TC), with an explicit orphan check and a declared known gap (SEC-09).
3. The plan and tasks honestly state they are gated and not implementation-authorized.
4. The governance correction (Project Owner is not the sole Gate 2 approver) is the right call.
5. Test-first is stated clearly with an RED/GREEN evidence rule in tasks.

## 6. Entry conditions to request Gate 2 again

- [ ] Feature Specification Gate 1 approved for the slices in scope (recorded in `reviews/`)
- [ ] Constitution approved and dated
- [ ] A-001..A-003 validated with evidence; BD-019 and the BDs blocking the in-scope tasks Confirmed with named approvers
- [ ] Plan Constitution Check completed; rate-limit decision per endpoint (G2-05, G2-10)
- [ ] Per-slug tasks and test_cases files, `<slug>.T##` IDs (G2-06, G2-07)
- [ ] Named independent Gate 2 reviewer recorded everywhere (G2-03)
- [ ] `src/` and `tests/` with RED evidence then GREEN for the first task
- [ ] SAST and dependency scan results attached
- [ ] Zip removed and `.gitignore` added (§4)
- [ ] `status.md`, `architecture.md`, `project_context.md` current

## 7. Gate 2 Recommendation (verbatim decision)

**Changes Requested. Not ready for Gate 2.** There is no code to approve. The project may continue with Gate 1 specification review, assumption validation and decision closure. Re-request Gate 2 when the entry conditions in §6 are met. The zip file question in §4 needs an answer from the author.

---

# Re-verification — 2026-10-15

**Reviewed:** commit `07b574d` "Gate2 review correction" (author `ahins.pradhan`) against this review and `GATE2-REMEDIATION-2026-10-13.md`.
**Result:** **Partially fixed. Gate 2 remains Not Ready.** The document-level findings are mostly resolved. The repository-level findings are not, and a new zip file was committed.

## Important discrepancy

`GATE2-REMEDIATION-2026-10-13.md` says `.gitignore` was added, `.agent/` was populated, the README was made operational, and the BRD backups and ADR stub were removed. **None of this is in the repository.** The remediation was done on an uploaded zip copy, and only the `.ai-context/` document changes were merged. The remediation record therefore overstates what was fixed. Please correct it or apply the missing changes.

## Finding-by-finding

| ID | Status | Verified evidence |
|---|---|---|
| G2-01 No implementation or tests | **Open (expected)** | Still no `src/` or `tests/`. Honestly stated in `status.md`. Nothing to fix until Feature Gate 1 passes. |
| G2-02 No approved spec or constitution | **Open** | `constitution.md` v1.1 says "Approval pending, approver TBD". Specs are still Draft. Not fabricated, which is correct. |
| G2-03 Gate 2 reviewer | **Partially fixed** | Name now appears in `status.md`, `project_context.md`, the plan and the specs, each with "owner confirmation pending". The Project Owner must confirm the nomination and independence. A reviewer should be nominated by the owner, not copied from the review's own metadata. |
| G2-04 Plan and tasks before gates | **Fixed (artefact state)** | All tasks in the per-slice files are Pending or Blocked and say "not executable until Feature Gate 1". A-001..A-003 and BD-019 remain open and are shown as open. |
| G2-05 Constitution check | **Fixed (draft)** | `plans/employee-internal-transfer.plan.md` §11 now has a line-by-line table with evidence and limits. Re-check after the constitution is approved. Note that "No secrets committed" is ticked although no secret scan has run. |
| G2-06 Task IDs and stub files | **Partially fixed** | Per-slice tasks files with `<slug>.T##` IDs exist. **The stubs are still tracked**: `plans/.plan.md`, `specs/.spec.md`, `tasks/.tasks.md`, `test_cases/.test_cases.md`. `.plan.md` still contains the superseded plan. Delete them. |
| G2-07 Per-feature test cases | **Fixed** | Four per-slice files, plus `_integration.md` and `_security.md` populated. Inventory only, not executable tests. |
| G2-08 Stale status files | **Fixed** | `status.md`, `project_context.md` and `architecture.md` (v0.2) are consistent with the plan and tasks. Keep them in step with future changes. |
| G2-09 SEC-09 AC and TC | **Fixed (draft)** | `SEC-09-AC-01` and `SEC-09-TC-01` are in the slice map, `traceability.md` and `test_cases/_security.md`. Needs Gate 1 review, and execution after A-001. |
| G2-10 Rate-limit decision | **Fixed as provisional** | Plan §5 has a per-endpoint table. The decision is "reuse existing throttling if present", which defers the question. Accepted for a draft. A-001 must record what the portal actually does. |
| G2-11 Empty `.agent/` | **Not fixed** | `int-standards.md` is 16 bytes (`# INT Standards`), `auto-log.md` 11 bytes, and the three workflows 24–27 bytes. `.agentignore` is 0 bytes. |
| G2-12 `.gitignore` | **Not fixed** | `.gitignore` does not exist in the repository. |
| G2-14 BRD backups | **Not fixed** | `BRD.md_bkp_160926` and `BRD.md_bkp_280926` are still tracked. |
| G2-15 `Daily_activity_sheet.xlsx` | **Open** | Still tracked. No answer on its purpose or contents. The remediation note says it was "not verifiable", but it is in the repository. |
| G2-16 README | **Not fixed** | `README.md` is still the 38-byte skeleton (Setup / Run / Deploy headings, no content). |
| G2-17 ADR stub | **Partially fixed** | `ADR-0001-routing-policy-boundary.md` was added and is good. `ADR-NNNN-.md` is still tracked. |
| G2-18 Coverage rationale | **Fixed** | One-line rationale added in `constitution.md` §2. |

## Zip file: not resolved, and repeated

- **Fixed:** `emp-internal-transfer.zip` was removed from the working tree in `07b574d`.
- **New problem:** the same commit adds **`.ai-context.zip`** (98,664 bytes, 48 files). It is a snapshot of `.ai-context/` taken on 2026-10-13. It repeats the problem raised in §4: a duplicate copy of every artefact, including the superseded `.plan.md`, the stub files and the BRD backups. It will go stale at the next change.
- **Still unanswered:** none of the three questions in §4 was answered. In particular, no explanation was given for committing zips.
- **History:** the original zip, with its nested `.git/`, remains in history from `94c25bb`. The owner has not decided on a history rewrite. The remediation record acknowledges this.
- **Required:** `git rm .ai-context.zip`, add `*.zip` to a real `.gitignore`, and send the files to reviewers by branch, PR or tag instead of an archive.

## Re-verification outcome

**Changes Requested (second round).** Do not re-request Gate 2 until:

1. `.ai-context.zip` is removed and `.gitignore` exists (G2-12).
2. `.agent/` files are populated (G2-11).
3. The four stub files, the BRD backups and `ADR-NNNN-.md` are removed (G2-06, G2-14, G2-17).
4. README has real content (G2-16), and the purpose of the activity sheet is confirmed (G2-15).
5. `GATE2-REMEDIATION-2026-10-13.md` is corrected to match the repository.
6. The Project Owner confirms the Gate 2 reviewer (G2-03), and the §6 entry conditions are met (G2-01, G2-02).

---

# Re-verification (round 3) — 2026-10-16

**Reviewed:** commit `7302ee1` "Gate 2 second-round remediation (repository-level findings)", checked against the working tree, not against the remediation record.
**Result:** **All repository-level findings from round 2 are now fixed.** Gate 2 remains **Not Ready**, because the gating conditions that cannot be fixed by editing files are still open (see the end).

| ID | Status | Verified evidence |
|---|---|---|
| G2-06 Stub files | **Fixed** | `plans/.plan.md`, `specs/.spec.md`, `tasks/.tasks.md`, `test_cases/.test_cases.md` are no longer tracked (`git ls-files`). |
| G2-11 `.agent/` control plane | **Fixed** | `int-standards.md` (PHP/MySQL: gate discipline, test-first, PDO prepared statements, CSRF, rate limits, no PII in logs, commit hygiene), `auto-log.md`, `code-review.md` (entry check, security checklist, scans), `generate-plan.md`, `generate-tests.md` and `.agentignore` (190 bytes) are populated. Content is consistent with the constitution. |
| G2-12 `.gitignore` | **Fixed** | Present. Ignores `*.zip` and other archives, `*_bkp_*`, `*.xlsx`, secrets and `.env`, vendor and build output. |
| G2-14 BRD backups | **Fixed** | Both `BRD.md_bkp_*` files are removed from the index. |
| G2-16 README | **Fixed** | Real content: current state, layout, planned setup, run, test and deploy sections, and contribution rules. It states honestly that there is no code yet. |
| G2-17 ADR stub | **Fixed** | `ADR-NNNN-.md` is removed. `ADR-0001-routing-policy-boundary.md` remains. |
| Zip file (§4) | **Fixed in the tree** | `.ai-context.zip` is removed. There are no tracked archives. `.gitignore` and `int-standards.md` §6 prohibit them. |
| Remediation record | **Fixed** | `GATE2-REMEDIATION-2026-10-13.md` now has a correction and per-finding statuses that match the repository. `auto-log.md` now requires a commit hash or file path as evidence for each "Fixed" claim. Minor: the old 2026-10-13 table is kept at the bottom of that file, and it still says the activity sheet "was not present". It is labelled as the zip-copy record, but could be deleted to avoid confusion. |
| `status.md` | **Fixed** | Updated in the same commit and shows G2-15 as open. |

## Still open

| ID | Why | Who |
|---|---|---|
| G2-15 `Daily_activity_sheet.xlsx` | Still tracked at the repo root. `*.xlsx` in `.gitignore` does not untrack an existing file. No purpose or content confirmation was given. | Owner: confirm it holds no personal or client data, or `git rm` it. |
| §4 zip questions | Q1 "why was a zip committed?" is still marked "Author to answer". Q2 was answered for `.ai-context.zip` (extract-and-diff, nothing newer). Both zips remain in git history, including the nested `.git/` in the first one. | Author: answer Q1 and confirm the old-zip snapshot is superseded. Owner: decide whether to rewrite history. |
| G2-03 Gate 2 reviewer | Name still comes from the review metadata. No dated nomination by the Project Owner. | Project Owner. |
| G2-01 No code or tests | Intentional, gated. | Start only after Feature Gate 1. |
| G2-02 Approvals | Constitution approval and Feature Specification Gate 1 are still pending. | Authorized approvers and Sourav Kumar Maity. |
| G2-09, G2-10 follow-ups | `SEC-09-TC-01` and the rate-limit decision depend on A-001..A-003 validation, which has no evidence yet. | Project Owner and portal technical owner. |
| G2-05 | Constitution check must be repeated once the constitution and specs are approved. | Project Owner. |

## Outcome

**Documentation and repository hygiene: accepted.** Seven of the eight round 2 items are closed with verified evidence, and the remediation record now matches the repository.

**Gate 2: still Not Ready.** There is no code, no RED or GREEN evidence, no scans, and the approval gates are open. Remaining actions: G2-15 (one `git rm` or an explanation), the author's answer to the zip question, the reviewer nomination, then Feature Gate 1 and the §6 entry conditions.
