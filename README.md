# Intelligent Experience Extractor

> Capture verified engineering experiences from troubleshooting, builds, and deployments — sanitized, deduplicated, and ready for reuse with a lightweight two-tier indexing architecture.

**Intelligent Experience Extractor** is an agent skill that teaches AI coding assistants how to extract structured, reusable engineering knowledge from completed work. It solves the critical problem of **context bloating** by introducing a **two-tier architecture (`experiences-index.md` + `experiences.md`)**: agents read a tiny index first, and only grep specific sections on demand, never dumping massive history into context.

## Why

Most engineering lessons are lost the moment a terminal window closes. Traditional knowledge bases quickly grow to hundreds of kilobytes, becoming so large that injecting them wastes tens of thousands of tokens per turn and causes model distraction. 

This skill keeps experience permanently searchable and lightweight:
1. **Lightweight Index (`experiences-index.md`)** — a concise lookup table mapping observable symptoms to exact grep keywords; read in milliseconds with negligible tokens.
2. **On-Demand Retrieval (`experiences.md`)** — agents grep only the matched entry slice, **never loading the entire body into context**.

## What It Does

- **Before troubleshooting** — scans the lightweight `experiences-index.md` first; on match, greps only the relevant entry section from `experiences.md`.
- **After verification** — evaluates whether completed work produced a durable lesson worth recording.
- **Two-step atomic sync** — writes/merges the structured chain (`symptom → root cause → solution → verification → prevention`) into `experiences.md` AND appends an index mapping to `experiences-index.md`.
- **Merge over append** — merges new evidence into existing topic entries instead of creating noisy duplicate logs.
- **Auto-redaction** — strips API keys, tokens, passwords, emails, private IPs, and customer data before preview.
- **Strict separation** — keeps debugging/troubleshooting lessons strictly in the experience base, preventing them from polluting project memory.

## The Experience Chain

Every recorded experience follows five fields:

| Step | Question Answered |
|---|---|
| **Problem** | What observable error, behavior, or impact did the user see? |
| **Root Cause** | What direct causal chain does the evidence support? (Unknown parts are declared unknown.) |
| **Solution** | What minimal steps, config, or commands reproduce the fix? |
| **Verification** | What tests, builds, or checks confirmed the fix? (Only actually-run verification.) |
| **Prevention** | What version pins, static checks, CI guards, or monitoring can reduce recurrence? |

## Hard Rules

- **Never full-load the experience body.** Always read the index first, then grep on demand.
- **Atomic index sync.** Every new or updated entry in `experiences.md` MUST have its corresponding entry in `experiences-index.md`.
- **No secrets in previews.** Redaction happens *before* the user sees the preview (`<REDACTED_TOKEN>`, `<PRIVATE_HOST>`).
- **No fabrication.** Commands, tests, metrics, and file paths are only recorded if they actually exist and were executed.
- **No unsolicited writes.** Writing requires explicit user authorization or a persisted `auto-save=on` magic comment.
- **Confidence is explicit.** Every entry carries a confidence label: `已验证` (verified), `部分验证` (partially verified), or `推测` (speculative).

## Structure

```
intelligent-experience-extractor/
├── SKILL.md                          # The skill definition (Chinese-first, works with any language)
├── agents/
│   └── openai.yaml                   # Agent interface metadata
├── assets/
│   ├── experiences-index.md          # Lightweight index template (symptom -> grep keyword)
│   ├── experience-entry.md           # Experience entry template
│   └── knowledge-base-readme.md      # Knowledge base index & rules template
├── README.md
├── LICENSE
└── .gitignore
```

## Relationship to Project Memory Curator

| | Project Memory Curator | Intelligent Experience Extractor |
|---|---|---|
| **Scope** | Project facts: tech stack, architecture boundaries, decisions, preferences, runbooks | Engineering experience: troubleshooting, debugging, build/deploy failures, performance |
| **Data Type** | Durable system context and rules | Actionable problem-solving chains (`symptom → root cause → solution → verification`) |
| **Retrieval** | Single-file read on demand (e.g. `decisions.md`) | Two-tier: read tiny index first, then grep entry slice |
| **Prohibition** | **Never stores troubleshooting/debugging logs** | **Never stores general project architecture or user preferences** |

## How to Use

Drop the entire `intelligent-experience-extractor/` directory into your agent's skills directory (e.g., `.claude/skills/` or `.agents/skills/`). The agent will automatically consult `experiences-index.md` whenever troubleshooting or ops tasks arise.

## License

MIT
