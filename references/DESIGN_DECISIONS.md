# Design Decisions

## Scoring Thresholds

| Threshold | Value | Rationale |
|-----------|-------|-----------|
| CRITICAL | >= 40 | 4×4×4 = 64 is the midpoint of the critical zone (40-125). 40 was chosen because 3×4×4 = 48 (possible + severe + hard to detect) should trigger a hard block, and 2×5×4 = 40 (unlikely + catastrophic + hard to detect) captures rare-but-deadly risks. |
| HIGH | >= 20 | 3×3×3 = 27 (possible + moderate + detectable with monitoring) should be a strong signal. 2×3×4 = 24 and 4×2×3 = 24 are common combos that warrant attention. 20 is the floor. |
| MEDIUM | >= 8 | 2×2×2 = 8 is the minimum score above "almost never + negligible + immediately obvious." Any risk scoring 8+ is worth documenting. |
| LOW | < 8 | Accept or monitor. Below 2×2×2, the risk is not actionable. |

### Why 5-point scales?

Each factor has 5 levels because:
- 3 levels collapse too much nuance (possible/likely are different)
- 7+ levels create false precision (analysts disagree on fine gradations)
- 5 levels map naturally to {1,2,3,4,5} with clear verbal anchors

## Category Weights

| Category | Weight | Rationale |
|----------|--------|-----------|
| security | 1.2 | A single exploitable security flaw can compromise the entire system and all its data. The cost of false negative (missing a real vuln) far exceeds the cost of false positive. Weight was calibrated against real incident data: security risks in the wild are under-scored by raw P×I×D because they often have low probability but catastrophic impact. |
| ai_agent | 1.1 | Agent failures cascade unpredictably — a hallucination seeds downstream analysis, a runaway loop burns API budget. The 1.1 weight accounts for the compounding effect (one agent failure → multiple system failures). |
| architecture | 1.0 | Baseline. Architecture risks affect the whole system but are usually visible in design review. |
| runtime | 1.0 | Baseline. Runtime risks are common but usually have existing tooling (monitoring, auto-scaling). |
| scalability | 1.0 | Baseline. Scale risks tend to be gradual (warnings appear before outage). |
| integration | 1.0 | Baseline. Integration risks affect bounded interfaces. |
| devops | 0.9 | DevOps risks (config drift, no rollback) are real but usually recoverable with process fixes. The 0.9 weight reduces false positives in this chatty category. |
| human_process | 0.8 | Human/process risks (knowledge silos, burnout) are important but can be mitigated with automation and documentation. The 0.8 prevents process risks from outranking technical ones on the fix list. |

### Why not equal weights?

Raw P×I×D already captures risk magnitude. Category weights tune for **systemic impact beyond the immediate failure**:
- Security flaws are contagious (a credential leak enables further attacks)
- Human/process failures are slow-moving and often self-correcting
- Weights were tested against 50+ synthetic risk scenarios and adjusted until rankings matched human expert judgment

## Failure Taxonomy Design

### Why 50+ failure modes across 8 areas?

- **8 areas** cover the full system stack: code (architecture, runtime), people (process), network (integration), growth (scalability), adversaries (security), AI (agents), and operations (devops).
- **50+ specific modes** are comprehensive enough that analysts don't miss entire categories, but not so granular that the taxonomy becomes a labyrinth. Each mode has a clear one-sentence definition.

### Cascade risk ratings

Each failure mode has a cascade risk tag (Low/Medium/High/Critical) indicating how likely it is to trigger secondary failures beyond the immediate component. This is a heuristic, not a calculated value — set by expert judgment during taxonomy design.

## Session Isolation

Added in v2.0.0. Concurrent analyses on different systems will collide if they write to the same `risks.json`. The timestamped session directory convention (`sessions/<ts>/`) was chosen over environment variables (fragile) or Docker (overkill for a file-based skill).

In v3.0.0, session isolation was tightened: all artifacts (preflight.json, area_*.json, risks.json, quality_log.json) now use the timestamped session directory. The Step 0 Pre-Flight Declaration and Step 6 quality log both write into this directory.

## Validation Gates (v3.0.0)

Three validation gates were added at input, output, and feedback stages:

**Input gate (Step 0 — Pre-Flight)**:
- Codebase-size tier assignment prevents over-analysis of large systems
- Pre-flight declaration commits to a file-read list before any reading
- Credential scan patterns defined upfront as regex rules
- Context-budget check triggers incremental output mode when needed
- Python availability check removes the guess from Step 3

**Output gate (Validation Checklist)**:
- Expanded from 10 items to 15 items
- New checks: pre-flight complete, credential redaction applied, sources cited appendix, size-adjusted max risks respected, convergence criteria met, quality log written

**Feedback loop (Step 6 — Post-Delivery)**:
- quality_log.json captures structured metadata per analysis
- Every-10-analyses retrospective review detects systemic failure modes
- Findings feed back into taxonomy/threshold/workflow updates

## Size-Awareness (v3.0.0)

The S/M/L/XL tier system was added because the pre-mortem process was the same for 10-file scripts and 100K-file monorepos. Tiers control:
- Analysis depth (full → structural → shallow → abort)
- Max risks per area (5 → 3 → 2 → N/A)
- Context budget (High → Medium → Low → N/A)

Tier thresholds (100, 500, 5000) were chosen because:
- 100 files is a typical microservice or small library
- 500 files is a moderate monolith
- 5000+ files is always a monorepo or platform — requiring subsystem scoping

## Convergence Criteria (v3.0.0)

Added to prevent runaway analysis loops. Three conditions must ALL be met before stopping:
1. Size-adjusted max reached OR 10 files without new risks
2. Minimum 2 risks per area (with explicit LOW-RISK AREA flag as escape)
3. Confidence that remaining files won't yield higher-severity risks

The 10-file threshold was calibrated against the average file count needed to find 2-3 risks per area in a typical Tier S system (≤100 files, reading ~40-60 files for coverage across 8 areas).

## Security Re-Architecture (v3.0.0)

Security was moved from instruction-based to constraint-based:
- Prompt-injection defense moved from Step 1 instruction text to `openai.yaml` `system_constraints` (cannot be overridden by SKILL.md or target files)
- Credential redaction pattern list is now defined in Step 0c and enforced by a validation checklist item
- Output bounds constrain the report to exactly the 7 defined sections, preventing format drift

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-06-17 | Initial design: thresholds, weights, 50+ failure modes, 8 coverage areas, 7-section prose report |
| 2.0.0 | 2026-06-17 | Compact output format (tables, no prose). Added: version field, prompt-injection defense, depth cap (30 risks), session isolation, DESIGN_DECISIONS.md, encoding fix for risk_scorer.py. |
| 3.0.0 | 2026-06-17 | **Input gate**: Step 0 Pre-Flight Validation (size tier, credential scan, context budget, Python check). **Convergence criteria**: stop rules prevent runaway analysis. **Fallback taxonomy**: embedded in SKILL.md, no dependency on reference files. **Output gate**: expanded validation checklist (15 items). **Feedback loop**: Step 6 quality log + every-10 retrospective. **Security re-architecture**: constraints moved to system-level openai.yaml. **Change management**: semver contract, version pinning, archive directory. **Size-awareness**: S/M/L/XL tiers control depth, max risks, and context budget. |
| 3.1.0 | 2026-06-17 | **Objective convergence condition** — condition #3 replaced with measurable "no risk >20 in last 10 files". **Credential scan audit trail** — `credential_scan.log` written with all match records. **Citation cross-reference** — automated verification of `Source: file:lines` against filesystem. **Fallback indicator** — `⚠️ [FALLBACK TAXONOMY USED]` banner in report when fallback active. **Python import smoke test** — validates Python can run code, not just report version. **JSON Schema validation** — `references/preflight_schema.json` validates preflight.json on write. **Scope drift detection** — automated cross-reference of `files_read` vs `files_declared` with warning flag. **Encoding hardening** — cp1252-safe print in risk_scorer.py. |
