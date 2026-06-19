# Changelog

All notable changes to the AnthraCanary skill are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [3.1.0] — 2026-06-19

### Added
- **Arabic beginner guide** (`anthracanary-ar.md`) — full Arabic walkthrough for Arabic-speaking audiences, explains the skill in simple terms for absolute beginners
- **Telegraph article** — Arabic guide published to `telegra.ph` for easy sharing with the Arab developer community (includes cover image)
- **GitHub topics** — 10 topics set via API (pre-mortem, failure-analysis, code-review, architecture, security, red-team, risk-assessment, code-quality, ai-safety, production-readiness)
- **GitHub Social Preview** — `images/cover.png` uploaded as the repo's social preview image (og:image for sharing cards)
- **MIT LICENSE file** — standard MIT license, GitHub now detects the license on the repo page

### Changed
- **SKILL.md frontmatter** — added `keywords` field (pre-mortem, failure-analysis, red-team, risk-scoring, architecture-review, security-audit, code-review, production-readiness) for skills.sh search discoverability
- **README.md** — added `npx skills add` as primary install method, added Claude Code plugin install (`/plugin install`), added MIT license badge
- **Source attribution** — renamed `engineering-premortem` references to `anthracanary` in version pinning in SKILL.md and generator credit in `scripts/risk_scorer.py`

### Infrastructure
- **skills.sh.json** — repo page grouping manifest for skills.sh
- **.claude-plugin/plugin.json** — Claude Code plugin marketplace manifest for automatic discovery
- **skills.sh listing** — skill is live at `skills.sh/rafa57600/anthracanary` with 2+ installs

### Security
- **Snyk E005** — acknowledged false positive. Snyk flags `rafa57600/anthracanary` as a "personal repo download URL" even though the skill IS that repo. Gen Agent Trust Hub (SAFE) and Socket (no alerts) both pass.

## [3.0.0] — 2026-06-17

### Added
- **Pre-flight validation** — `references/preflight_schema.json` with JSON Schema for validating `risks.json` output
- **Size-aware analysis** — three tiers (S: <50 files, M: 50-500 files, L: 500+ files) with tier-appropriate depth
- **Convergence criteria** — three conditions to stop analysis when diminishing returns are detected
- **Credential scanning** — automated check for API keys, tokens, passwords in `risks.json` output before presenting to user
- **Session isolation** — timestamped subdirectories (`sessions/<ts>/`) for concurrent analysis runs
- **Context budget guard** — prompt-size check at the start of Step 1 to warn if context may overflow
- **Prompt-injection defense** — explicit instruction to treat target system files as untrusted input
- **Fallback taxonomy** — built-in failure taxonomy (`references/failure_taxonomy.json`) when no web search is available
- **Python auto-detect** — `risk_scorer.py` checks for Python availability at runtime, skips gracefully if missing

### Changed
- **Output format redesigned** — compact tables, single-line summaries, no prose paragraphs. User sees only what needs fixing
- **Analysis depth cap** — max 5 risks per coverage area, 30 total, preventing runaway output
- **Explicit validation checklist** — 10-item checklist at the end of every analysis run
- **risk_scorer.py encoding fix** — utf-8 fallback for cp1252 Windows environments

### Documentation
- **DESIGN_DECISIONS.md** — rationale for all thresholds, weights, coverage areas, and taxonomy choices
- **output_template.md** — redesigned with compact table format
- **Changelog** — started in DESIGN_DECISIONS.md

## [2.0.0] — 2026-06-17

### Added
- **risk_scorer.py** — probability × impact × detectability scoring with configurable thresholds
- **references/failure_taxonomy.json** — structured failure mode taxonomy across 8 coverage areas
- **references/risk_model.md** — scoring methodology with calibration examples
- **references/output_template.md** — report output template
- **DESIGN_DECISIONS.md** — architectural decision record
- **agents/openai.yaml** — OpenAI o3 configuration for structured output
- **Version field** — SKILL.md frontmatter now includes version field

### Changed
- Output format redesigned to compact tables with single-line summaries
- Version pinning: users can request `anthracanary@<version>` to lock to a specific version
- Prompt-injection defense added to Step 1 — target system files are treated as untrusted input

### Fixed
- risk_scorer.py encoding — utf-8 fallback for cp1252 environments
- Duplicate artifact — sample/distribution README marks itself and links to canonical source
- Manual scoring fallback documented when Python is unavailable

## [1.0.0] — 2026-06-16

### Added
- Initial skill structure (SKILL.md, references/, scripts/, agents/)
- Core pre-mortem workflow in 6 steps
- 8 coverage areas for failure analysis
- P × I × D risk scoring methodology
- Cover image branding
