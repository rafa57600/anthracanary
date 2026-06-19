# Pre-Mortem Report — Compact Output Format

## Principle

Every section must be scannable in under 5 seconds. No prose paragraphs.
Use tables, bullet lists, and single-line summaries only.
The user reads this to know **what to fix**, not to read a story.

---

## Section 1: System Snapshot

A single-line header with the essential facts:

```
System: <name> | Stack: <lang/framework/db> | Deploy: <cadence> | Team: <size>
Crit Risks: <N> | High Risks: <N> | Med Risks: <N> | Low Risks: <N>
```

---

## Section 2: What Kills It (1-line trigger)

A single sentence that describes the failure scenario without narrative build-up:

```
<Trigger> → <cascade> → <final state>
```

Example:
```
Config typo in production → all instances fail health check → no rollback mechanism → 4h downtime
```

---

## Section 3: Ranked Risks

A single unified table with severity color-coding in the ID column.
**Every row MUST include a source citation.** If unverifiable, mark as `[UNVERIFIED]` in the Source cell.

```
| ID | Risk | Score | P | I | D | Source | Fix |
|----|------|-------|---|---|---|--------|-----|
| ARCH-01 | [CRIT] Single Redis is SPOF | 60 | 3 | 5 | 4 | config/redis.yml:12 | Add replica + failover |
| AI-001 | [HIGH] No output validation | 36 | 3 | 4 | 3 | service/agent.py:88-95 | Add JSON schema check |
```

Max 30 rows. If you have more, run a triage pass.

---

## Section 4: Root Causes

Grouped root causes as a compact list:

```
- **No HA design**: ARCH-01, ARCH-03 — system assumes single-instance, no redundancy pattern anywhere
- **No observability**: OPS-01, RUNT-02 — no metrics, no tracing, no paging path
- **No fallback modes**: INT-01, INT-03 — every external call is synchronous, no circuit breaker
```

---

## Section 5: Warning Signals

Each signal is one line with the metric and linked risk:

```
- P95 latency >500ms for 5min → ARCH-01 (circuit may be open)
- Error rate >1% on /checkout → INT-01 (downstream degraded)
- Reports missing kill criteria → AI-002 (format degradation)
```

---

## Section 6: Fix Plan

A table ordered by impact/effort (highest return first).

| # | Fix | Risks Fixed | Effort | Impact | Description |
|---|-----|-------------|--------|--------|-------------|
| 1 | Add circuit breaker to /checkout | INT-01, INT-03 | Low | High | ... |
| 2 | Add read replica + failover | ARCH-01 | Medium | High | ... |

---

## Section 7: Walk-Away Triggers

Three measurable conditions that mean "stop fixing, redesign or kill":

```
- **Kill if**: <measurable condition>
  - **When**: <time threshold>
  - **Then**: <fallback action>

- **Kill if**: <measurable condition>
  - **When**: <time threshold>
  - **Then**: <fallback action>
```

---

## Bottom Line (one word or short verdict)

```
Verdict: Ship with mitigations / Redesign / Kill
```

---

## Required Pre-Delivery Check

- [ ] System snapshot present (1 line)
- [ ] Ranking table has source citations on every row
- [ ] Fix plan table sorted by impact/effort ratio
- [ ] Walk-away triggers are measurable, not vague
- [ ] All 8 coverage areas checked
- [ ] Verdict is one of the three options
