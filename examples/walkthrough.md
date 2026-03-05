# Walkthrough: Using the Rote Compliance Toolkit

This guide walks through using each skill with example scenarios.

## 1. HIPAA Gap Analysis

**Scenario**: You have an information security policy and need to assess HIPAA coverage before an audit.

### Claude Code

```
/rote-compliance-toolkit:hipaa-gap-analysis

Then provide your document:
- Paste the policy text directly, or
- Reference a file: "Analyze the document at ./policies/infosec-policy.pdf"
```

### Any LLM

1. Copy the prompt from `prompts/hipaa-gap-analysis.md`
2. Paste it into your LLM
3. Follow up with your policy text

### Sample Output

See [`sample-gap-analysis-output.json`](./sample-gap-analysis-output.json) for a complete example showing:
- 5 controls assessed
- 2 covered, 2 partial, 1 gap
- Evidence citations, confidence scores, and remediation recommendations

---

## 2. BAA Review

**Scenario**: A vendor sent you a BAA and you need to verify it meets HIPAA requirements before signing.

### Claude Code

```
/rote-compliance-toolkit:baa-review

Then provide the BAA text or file path.
```

### Any LLM

1. Copy the prompt from `prompts/baa-review.md`
2. Paste into your LLM
3. Provide the BAA text

### Sample Output

See [`sample-baa-output.json`](./sample-baa-output.json) for a complete example showing:
- All 9 required provisions assessed
- 5 compliant, 3 deficient, 1 missing
- Risk-scored findings with specific contract language recommendations

---

## 3. Compliance Q&A

**Scenario**: You need to quickly check what your BAA says about breach notification timelines.

### Claude Code

```
/rote-compliance-toolkit:compliance-qa

Then ask: "What is the breach notification timeline in our BAA with Acme?"
And provide the BAA text as context.
```

### Any LLM

1. Copy the prompt from `prompts/compliance-qa.md`
2. Paste into your LLM
3. Provide document context, then ask your question

### Sample Output

```markdown
**Summary Answer:**
The BAA with Acme Cloud Services requires breach reporting but does not specify a timeline.

**Detailed Analysis:**
- Section 5 states: "Business Associate shall report to Covered Entity any Security
  Incident or Breach of Unsecured PHI of which it becomes aware." [Acme BAA, Section 5]
- No specific timeline (e.g., 30 days, 60 days) is defined in the agreement
- HIPAA requires notification within 60 days of discovery per 164.410(a)

**Context Gaps / Caveats:**
The BAA does not define "discovery" or specify the content requirements for breach notifications.

**Confidence / Escalation:**
High — The absence of a timeline is clearly demonstrated by the text. Recommend amending the BAA to include a specific notification deadline.
```

---

## 4. Framework Mapping

**Scenario**: You have an access control policy and need to know which NIST 800-53 controls it covers.

### Claude Code

```
/rote-compliance-toolkit:framework-mapping

Then: "Map this document to NIST 800-53 Rev 5" and provide the policy.
```

### Any LLM

1. Copy the prompt from `prompts/framework-mapping.md`
2. Paste into your LLM
3. Specify the framework and provide the document

### Sample Output

The skill produces two complementary views:
- **Per-section mappings**: Each document section mapped to relevant controls with relevance scores
- **Per-control coverage**: Each framework control with its coverage status and the sections that address it

This bidirectional mapping makes it easy to identify both unmapped sections (potential waste) and uncovered controls (gaps).

---

## 5. Control Assessment

**Scenario**: An auditor asks about your organization's account management practices (NIST AC-2).

### Claude Code

```
/rote-compliance-toolkit:control-assessment

Then: "Assess NIST 800-53 AC-2 (Account Management) against this document"
and provide your policy.
```

### Any LLM

1. Copy the prompt from `prompts/control-assessment.md`
2. Paste into your LLM
3. Specify the control and provide the document

### Sample Output

The assessment includes:
- Evidence chain with exact quotes and section references
- Gap description identifying unaddressed sub-controls
- Severity classification (critical/high/medium/low)
- Remediation recommendations with timelines

---

## Tips for Best Results

1. **Provide complete documents.** The more text the LLM can search, the more accurate the assessment.
2. **Be specific about the framework.** Say "NIST 800-53 Rev 5" not just "NIST."
3. **One document at a time.** For cross-document analysis, use Compliance Q&A with multiple documents as context.
4. **Review the output.** These are AI-generated assessments — always have a qualified compliance professional review findings before acting on them.
5. **Iterate.** If the initial assessment misses something, provide additional context and ask for a re-assessment.

---

## Want More?

These skills give you the methodology. For production-grade compliance analysis at scale, check out [Rote](https://dangssolutions.com/rote):

- Upload documents (PDF, DOCX) and have them parsed and indexed automatically
- Run gap analysis across hundreds of controls in minutes
- Get RAG-powered Q&A grounded in your entire document corpus
- Generate audit-ready reports with executive summaries
- Collaborate with your team on findings and remediation tracking

[Learn more](https://dangssolutions.com/rote) | [Join the waitlist](https://dangssolutions.com/rote)
