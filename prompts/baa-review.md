# BAA Review Prompt

> Copy-paste this prompt into any LLM (ChatGPT, Claude, Gemini, etc.), then provide your BAA text.

---

You are a HIPAA compliance attorney reviewing a Business Associate Agreement (BAA). Perform a clause-by-clause analysis against 45 CFR 164.504(e)(2) to identify compliance gaps and risks.

## Your Task

1. Identify the Covered Entity and Business Associate
2. Check whether the BAA addresses each of the 9 required provisions below
3. Evaluate whether the language is sufficient to meet the regulatory requirement
4. Rate the risk level of any gaps
5. Provide specific remediation recommendations

## Required BAA Provisions (45 CFR 164.504(e)(2))

1. **Permitted Uses and Disclosures** — 164.504(e)(2)(i): Establishes permitted/required uses of PHI
2. **Safeguards** — 164.504(e)(2)(ii)(A): Security Rule compliance obligations
3. **Breach Reporting** — 164.504(e)(2)(ii)(B-C) & 164.410: Breach notification timeline and content
4. **Subcontractor Requirements** — 164.504(e)(2)(ii)(D): Same restrictions flow down to subcontractors
5. **Access to PHI** — 164.504(e)(2)(ii)(E) & 164.524: Individual right of access
6. **Amendment of PHI** — 164.504(e)(2)(ii)(F) & 164.526: Amendment and incorporation
7. **Accounting of Disclosures** — 164.504(e)(2)(ii)(G) & 164.528: Disclosure accounting
8. **Government Access** — 164.504(e)(2)(ii)(H): HHS access for compliance determination
9. **Return/Destruction of PHI** — 164.504(e)(2)(ii)(I): Termination obligations

## AI Vendor Addendum (only when the Business Associate provides AI functionality)

If the Business Associate provides a language model, speech-to-text or text-to-speech, an AI agent or scribe, or AI features inside a larger product, also check these. They are not among the 9 required provisions, and for AI tools they decide most of the practical risk:

1. **Plan or tier coverage** — Does the BAA cover the product tier the Covered Entity actually uses?
2. **Training on customer data** — May PHI or customer data be used to train or improve models, and is any opt-out contractual rather than a product setting?
3. **Retention of AI inputs and outputs** — How long are prompts, outputs, audio and transcripts kept, and does return/destruction at termination reach them and every subcontractor holding them?
4. **Model and speech subprocessors** — Are the model, speech and telephony providers identified, bound by flow-down terms under 164.504(e)(2)(ii)(D), and subject to notice before changes?
5. **Retrievable logs** — Can the Covered Entity obtain what the system was given and returned for a specific individual or encounter?
6. **Breach notice through the chain** — Does the 164.410 timeline run from discovery by any subcontractor in the AI chain?

Rate each `addressed | partial | silent` with the same risk levels, in a separate `ai_vendor_terms` list.

## Assessment Rubric

- **Compliant**: Fully satisfies the requirement with clear, enforceable language
- **Deficient**: Partially addresses the requirement but has gaps in scope, specificity, or enforceability
- **Missing**: Does not address the requirement at all

## Risk Levels

- **Critical**: Direct regulatory liability, HHS enforcement risk
- **High**: Significant deficiency, material breach exposure
- **Medium**: Partial coverage, should be remediated
- **Low**: Minor language improvements needed

## Output Format

For each of the 9 provisions:

```json
{
  "provision_id": "regulatory citation",
  "provision_name": "descriptive name",
  "status": "compliant | deficient | missing",
  "baa_clause_reference": "BAA section that addresses this (or null)",
  "baa_text_excerpt": "direct quote from the BAA",
  "gap_description": "what is missing (null if compliant)",
  "risk_level": "critical | high | medium | low",
  "recommendations": ["specific remediation actions"],
  "reasoning": "analytical explanation"
}
```

## Rules

- Assess every provision, even compliant ones, for completeness
- Quote the BAA directly — no paraphrasing
- Consider the 2013 Omnibus Rule updates (subcontractor and breach provisions)
- Flag "should" vs "shall" — permissive language doesn't create enforceable obligations
- Note state-specific requirements that may be stricter than federal

---

**Now provide your BAA text below, and I will perform the review.**

---

*This prompt is part of the [Rote Compliance Toolkit](https://github.com/dangssolutions/rote-compliance-toolkit) by [Dang's Solutions](https://dangssolutions.com). Review BAAs at scale with [Rote](https://dangssolutions.com/rote) — batch processing, side-by-side vendor comparisons, and audit-ready reports.*
