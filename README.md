# AnthraCanary 🐦

[![skills.sh](https://skills.sh/b/rafa57600/anthracanary)](https://skills.sh/rafa57600/anthracanary)

**Hostile red-team failure analysis for software systems.** Your canary in the coal mine — assume production failure within 3-6 months and reconstruct the causal chain backward.

AnthraCanary systematically analyzes your codebase across 8 failure dimensions (architecture, runtime, security, scalability, AI/agent behavior, integrations, DevOps, human/process factors), scores each risk by probability × impact × detectability, and produces a ranked fix plan.

## Installation

### Via the skill-installer (recommended)

```bash
python scripts/install-skill-from-github.py --repo rafa57600/anthracanary
```

### Manual installation

Clone the repo and copy the skill to your skills directory:

```bash
git clone https://github.com/rafa57600/anthracanary.git
# Copy SKILL.md and all subdirectories to your agent's skills directory
# Linux/Mac: ~/.config/anthracode/skills/anthracanary/
# Windows: %APPDATA%\anthracode\skills\anthracanary\
```

### From a direct URL (any agent)

If your agent supports installing skills from a GitHub URL, point it to:
```
https://github.com/rafa57600/anthracanary
```

## Usage

Invoke the skill by asking your agent:

> "Run a pre-mortem on this system"
> "AnthraCanary this codebase for failure risks"
> "Find the weakest points in our architecture"

AnthraCanary will:
1. Profile the target system (size tier, file inventory, credential scan)
2. Systematically identify risks across 8 coverage areas
3. Score each risk using the P × I × D formula
4. Produce a ranked fix plan with walk-away triggers
5. Log quality metrics for continuous improvement

## File Structure

```
anthracanary/
├── SKILL.md                  # Main skill definition and workflow
├── scripts/
│   └── risk_scorer.py        # Automated risk scoring engine
├── references/
│   ├── failure_taxonomy.md   # Failure mode taxonomy by coverage area
│   ├── risk_model.md         # Risk scoring model (P × I × D)
│   ├── output_template.md    # Standardized 7-section report template
│   ├── preflight_schema.json # JSON Schema for pre-flight validation
│   └── DESIGN_DECISIONS.md   # Architecture decisions and changelog
├── agents/
│   └── openai.yaml           # OpenAI agent configuration
├── risks.json                # Risk tracking database
└── README.md                 # This file
```

## Requirements

- Target system: any codebase (10–100K+ files)
- Python 3.8+ for automated scoring (falls back to manual tables)
- Read access to the target system's source files

## Version

Current version: **3.1.0**

## License

MIT
