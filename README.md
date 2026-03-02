# Rote Compliance Skills

A peer network of compliance skills for AI-powered regulatory analysis. Each skill is a self-contained markdown file usable as a system prompt in any LLM.

## Philosophy

These skills are a **leaderless network** — no flagship, no hierarchy. Each skill contributes equally to a single mission: making compliance automatic and integrated. The collection represents the *thinking methodology* for compliance work.

Use standalone for quick assessments. Use with [Rote](https://rotelabs.co) for automated workflows at scale — dynamic framework injection, document parsing, persistent storage, and agentic orchestration.

## Skills

| Skill | Description |
|-------|-------------|
| [risk-assessment](skills/risk-assessment/SKILL.md) | Framework-directable risk assessment with 3x3 likelihood/impact matrix and risk treatment options |
| [baa-review](skills/baa-review/SKILL.md) | HIPAA Business Associate Agreement clause-by-clause compliance review |
| [compliance-qa](skills/compliance-qa/SKILL.md) | RAG-enhanced Q&A with regulatory interpretation guardrails and escalation triggers |
| [control-assessment](skills/control-assessment/SKILL.md) | Individual framework control assessment with evidence extraction and gap classification |
| [framework-mapping](skills/framework-mapping/SKILL.md) | Bidirectional document-to-control mapping with relevance scoring |
| [hipaa-gap-analysis](skills/hipaa-gap-analysis/SKILL.md) | HIPAA Security/Privacy Rule gap analysis with confidence scoring |

## Framework Appendices

Skills that support multiple frameworks use the **framework appendix pattern**: concatenate a file from `frameworks/` to direct the skill at a specific framework.

```
# Example: Risk assessment against ISO 27001
cat skills/risk-assessment/SKILL.md frameworks/iso-27001-controls.md | llm-cli
```

Available framework appendices:

| Framework | File |
|-----------|------|
| NIST CSF 2.0 | [nist-csf-2.0-controls.md](frameworks/nist-csf-2.0-controls.md) |

More framework appendices will be added over time. Contributions welcome.

## Usage

### Standalone (any LLM)

Each skill is a single markdown file. Use it as a system prompt:

```python
import anthropic

client = anthropic.Anthropic()

# Load the skill
with open("skills/risk-assessment/SKILL.md") as f:
    system_prompt = f.read()

# Optionally append a framework appendix
with open("frameworks/nist-csf-2.0-controls.md") as f:
    system_prompt += "\n\n" + f.read()

message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=8000,
    system=system_prompt,
    messages=[{
        "role": "user",
        "content": "Perform a risk assessment on: Our web application stores customer PII in an unencrypted PostgreSQL database accessible from the public internet on port 5432."
    }]
)
print(message.content[0].text)
```

### With Rote Platform

When used inside [Rote](https://rotelabs.co), each skill gains:

- **Dynamic framework injection** — Select any framework from your library; Rote appends the controls automatically
- **Document parsing** — Upload PDFs/DOCX and Rote extracts the text for the skill
- **Persistent storage** — Results are stored, searchable, and exportable
- **Agentic orchestration** — Chain skills together (e.g., framework-mapping -> control-assessment -> risk-assessment)
- **Multi-tenant workspaces** — Collaborate with your team on shared assessments

## Properties of Each Skill

- **Self-contained** — single markdown file, usable as system prompt in any LLM
- **Peer in the network** — each skill is independent but designed to compose with others
- **YAML frontmatter** — name, description, version, default_framework (where applicable)
- **Framework appendix pattern** — concatenate a file from `frameworks/` to direct any framework-aware skill
- **No platform dependencies** — no LangGraph, no database, no imports

## Contributing

1. Fork this repo
2. Add your skill in `skills/<skill-name>/SKILL.md`
3. Include YAML frontmatter with `name`, `description`, and `version`
4. Add at least 2 few-shot examples
5. Submit a PR

## License

MIT - See [LICENSE](LICENSE)

Built by [Rote](https://rotelabs.co)
