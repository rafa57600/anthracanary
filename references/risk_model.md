# Risk Scoring Model

## P x I x D Formula

Each risk is scored using three factors on a 1-5 scale:

| Factor | 1 | 2 | 3 | 4 | 5 |
|--------|---|---|---|---|---|
| **Probability** | Almost never | Unlikely | Possible | Likely | Almost certain |
| **Impact** | Negligible | Minor | Moderate | Severe | Catastrophic |
| **Detectability** | Immediately obvious | Detected quickly | Detectable with monitoring | Hard to detect | Undetectable until failure |

**Score = Probability x Impact x Detectability** (range: 1-125)

## Severity Thresholds

| Score | Severity | Action Required |
|-------|----------|-----------------|
| >= 40 | CRITICAL | Must fix before production |
| >= 20 | HIGH | Should fix before production |
| >= 8 | MEDIUM | Fix if time permits |
| < 8 | LOW | Accept or monitor |

## Category Weights

Certain failure domains are weighted up or down to reflect their systemic
impact on modern distributed and AI systems:

| Category | Weight | Rationale |
|----------|--------|-----------|
| security | 1.2 | Single security flaw can compromise entire system + data |
| ai_agent | 1.1 | Agent failures cascade unpredictably and cost tokens/data |
| architecture | 1.0 | Baseline |
| runtime | 1.0 | Baseline |
| scalability | 1.0 | Baseline |
| integration | 1.0 | Baseline |
| devops | 0.9 | Usually recoverable with process fixes |
| human_process | 0.8 | Can be mitigated with automation |

**Weighted Score = Raw Score x Category Weight**

## Working with risk_scorer.py

The bundled `scripts/risk_scorer.py` automates scoring and ranking.

### Quick start
```bash
# Pipe JSON
cat risks.json | python scripts/risk_scorer.py

# From file
python scripts/risk_scorer.py --risks-file risks.json

# Interactive mode (good for live sessions)
python scripts/risk_scorer.py --interactive
```

### JSON input format
```json
{
  "risks": [
    {
      "id": "ARCH-001",
      "title": "Brief risk title",
      "probability": 4,
      "impact": 5,
      "detectability": 3,
      "category": "architecture",
      "description": "Full description of the risk and its mechanics",
      "warning_signals": ["Alert if X rises above Y"],
      "mitigations": ["Add circuit breaker to Z"]
    }
  ]
}
```

### Output
The script produces a ranked Markdown report grouped by severity, with:
- Summary counts by severity level
- Per-risk scoring breakdown
- Early warning signals and mitigations (if provided in input)
- Category weight adjustments noted where applied
