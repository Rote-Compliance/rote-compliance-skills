---
name: compliance-posture-intake-agentic
description: >
  Comprehensive HIPAA compliance posture assessment for agent and API contexts.
  Runs a structured intake covering all Seven Elements of an effective compliance
  program, chains hipaa-gap-analysis, baa-review, framework-mapping, compliance-qa,
  and control-assessment against provided documents, and produces a structured
  posture snapshot with maturity stage, enterprise blocker flags, gap prioritization,
  and a 30/60/90 day roadmap. Compatible with any agent context that has access
  to the rote-compliance-toolkit tools — via Claude Code plugin, Rote MCP server,
  or direct API integration.
argument-hint: Start the compliance posture intake — answer orientation questions, then optionally provide documents for analysis
allowed-tools: Read, Glob, Grep, WebFetch, WebSearch, Write
version: 1.0
author: Rote Compliance
license: Apache-2.0
---

# Compliance Posture Intake

## Purpose

Produce a structured compliance posture snapshot by combining self-reported
questionnaire data with document-level evidence analysis. Output is a single
markdown file the user can reference, share, or feed into Rote as a baseline.

This skill orchestrates the full rote-compliance-toolkit. It does not replace
individual tools — it sequences them and synthesizes their output into a
unified posture picture.

## Invocation Contexts

This skill can run in any agent context that has access to the toolkit tools.
The document analysis steps in Step 3 invoke those tools — the syntax depends
on how the toolkit is available in your environment:

| Context | Tool invocation syntax | Example |
|---------|----------------------|---------|
| Claude Code plugin | Slash command | `/rote-compliance-toolkit:hipaa-gap-analysis` |
| Rote MCP server | MCP tool call | `rote_hipaa_gap_analysis` (tool name per server spec) |
| Direct API / custom agent | Function call | Per the toolkit's API or function schema |

The methodology, question set, scoring logic, and output structure are
identical across all invocation contexts. Only the tool call syntax differs.
If the toolkit tools are not available in your context, fall back to the
inline analysis methodology in the Cowork SKILL.md instead.

---

## Execution Flow

### Step 1 — Orientation (always run first)

Ask all 10 orientation questions before doing anything else. Present them in
two groups to avoid overwhelming the user.

**Group A — Organizational context (ask together):**

1. What is your organization's role under HIPAA?
   (Covered Entity / Business Associate / Both / Unsure)

2. How many employees handle PHI, directly or indirectly?
   (<10 / 10–50 / 50–200 / 200+)

3. What best describes your company's stage?
   (Pre-revenue or seed / Early growth Series A–B / Established Series B+ / Enterprise)

4. Who is your primary customer in healthcare?
   (Small practices / Mid-market health systems / Enterprise health systems /
   Payers or insurers / Multiple / Not sure yet)

5. Which frameworks are you expected to comply with?
   (HIPAA / HITRUST CSF / SOC 2 / NIST 800-53 / ISO 27001 / Multiple / Not sure)

6. What is your primary goal today?
   (Prepare for an enterprise compliance review / Pass a vendor security
   questionnaire / Build a program from scratch / Understand current state / Other)

7. Do you have compliance documents to share?
   (Policies and procedures / Business Associate Agreement / Risk assessment /
   Training records / State license or business registration / None yet — list all that apply)

11. Briefly describe what your product or service does — what problem it solves
    and the types of data or workflows it touches. (1–2 sentences)

**Group B — Risk profile (ask together after Group A):**

8. Do you handle any extra-protected categories of PHI — behavioral health,
   substance use disorder records (42 CFR Part 2), HIV/AIDS status, or
   pediatric records? (Yes / No / Unsure)

9. Have you completed any third-party compliance certifications?
   (SOC 2 Type I / SOC 2 Type II / HITRUST CSF / ISO 27001 /
   None yet / In progress — specify)

10. Do any subcontractors, offshore development partners, or outsourced team
    members have access to PHI or PHI-containing environments? (Yes / No / Unsure)

12. In which states do you operate or serve customers? Every state has data
    privacy and breach notification requirements that layer on top of HIPAA.
    List all states where you have customers or operations.

**After receiving answers:** Generate a one-paragraph orientation summary and
confirm it with the user before proceeding. Example format:

> You are a [size]-person [role] at [stage] stage, selling to [customer type].
> You [brief business description from Q11]. [Framework] compliance is your
> primary obligation[, with [framework] as a likely future requirement].
> You [do/do not] handle extra-protected PHI categories. You [have/have not]
> completed third-party certifications. [X] subcontractors/offshore partners
> [do/do not] have PHI access. You operate in [states from Q12 — state law
> research completed before Step 2; findings held for State Law Considerations].
> Your primary goal is [goal]. You have [documents] available for analysis.

**Conditional trigger logic — determine now and carry forward:**
- Q2 ≥ 50 employees → background checks question is active in Element 3
- Q5 includes HITRUST or SOC 2, OR Q6 is enterprise review → pen testing
  question is active in Element 5
- Q3 is Series B+ or Established → board reporting question is active in Element 2
- Q9 confirms SOC 2 Type II or HITRUST → note existing certification;
  adjust maturity baseline upward and roadmap accordingly
- Q10 is Yes → flag subcontractor BAA coverage in Element 3; elevate BAA
  Review in Step 3 if a BAA is provided
- Q12 names any states (or a state license is listed in Q7) → run web searches
  before beginning Step 2. For each state identified:
  - `"[state] health data privacy law obligations for [business type from Q11] 2026"`
  - `"[state] data protection requirements [business description from Q11]"`
  - `"[state] breach notification law healthcare timeline"`
  Hold findings; include State Law Considerations section in output populated from
  search results. Universal breach notification note always included regardless of
  whether any states were named.

---

### Step 2 — Posture Assessment (Seven Elements)

Present questions element by element. For each element, state the element name
and guiding question, then ask the applicable questions.

Score each answer: Yes = 1, No = 0. Uncertain answers = 0, flagged as
"unverified — review recommended."

**Score tracking:** Maintain a running count of Yes answers and total
applicable questions. Express the final score as a percentage.

---

#### Element 1: Written Standards and Procedures
*Do you have documented policies that guide compliant behavior?*

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 1.1 | We have written HIPAA policies and procedures | ✓ |
| 1.2 | Our policies are accessible to all employees who need them | |
| 1.3 | Our policies are reviewed and updated at least annually or when regulations change | ✓ |

---

#### Element 2: Oversight by High-Level Personnel
*Is there clear accountability for your compliance program?*

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 2.1 | We have a designated Privacy Officer or Security Officer (or both) | ✓ |

**Conditional — ask only if Q3 is Series B+ or Established:**

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 2.C | Board or senior management receives regular compliance updates | |

---

#### Element 3: Due Care in Delegation
*Do you screen and authorize people with access to sensitive data?*

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 3.1 | We have documented processes for granting and revoking system access | ✓ |
| 3.2 | We screen vendors and subprocessors before they handle PHI | ✓ |
| 3.3 | We have executed BAAs with all vendors, subcontractors, and offshore partners who access PHI or PHI-containing environments | ✓ |

**Conditional — ask only if Q2 is 50–200 or 200+:**

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 3.C | We conduct background checks on employees who handle PHI | |

**If Q10 is Yes:** After 3.3, add a note in your internal tracking: "Subcontractor BAA coverage — verify in Phase 3 BAA Review."

---

#### Element 4: Effective Communication and Training
*Do your people know what's expected of them?*

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 4.1 | All employees who handle PHI have completed HIPAA training | ✓ |
| 4.2 | We maintain records of training completion with dates | ✓ |
| 4.3 | New hires receive compliance training during onboarding | |
| 4.4 | We provide annual refresher training or updates when policies change | |

---

#### Element 5: Monitoring, Auditing, and Risk Assessment
*Do you actively look for compliance problems?*

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 5.1 | We have completed a formal HIPAA risk assessment in the past 12 months | ✓ |
| 5.2 | We maintain audit logs that track access to PHI | ✓ |
| 5.3 | We can demonstrate our audit logging capability on demand | ✓ |
| 5.4 | We review audit logs periodically to detect unauthorized access | |
| 5.5 | We have documented our technical safeguards (encryption, access controls, etc.) | ✓ |

**Conditional — ask only if Q5 includes HITRUST or SOC 2, OR Q6 is enterprise review:**

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 5.C | We conduct periodic security assessments or penetration testing | ✓ |

---

#### Element 6: Enforcement and Discipline
*Do you hold people accountable for compliance violations?*

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 6.1 | We have documented disciplinary procedures for policy violations | |

---

#### Element 7: Response and Prevention
*Can you respond effectively when things go wrong?*

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 7.1 | We have a documented incident response procedure | ✓ |
| 7.2 | We know who to contact and what steps to take if a breach occurs | ✓ |
| 7.3 | We have a breach notification process (internal and external) | ✓ |
| 7.4 | We have tested our incident response procedure through a tabletop exercise or real-world incident response | ✓ |

**Conditional — ask only if running score suggests Active Management (≥70%) based on answers so far:**

| # | Question | Enterprise trigger |
|---|----------|-------------------|
| 7.C | Following any security incident, we documented the event, notified affected parties as required, and updated our procedures as a result | |

---

### Step 2 — Scoring

After all elements are complete:

1. Calculate: `score_pct = (yes_count / applicable_question_count) * 100`

2. Assign tier:
   - 90–100%: **Enterprise-Ready**
   - 70–89%: **Nearly Ready**
   - 48–69%: **Significant Work Needed**
   - 25–47%: **Building Foundation**
   - <25%: **Ground-Up Development**

3. Assign maturity stage:
   - <70%: **Stage 1 — Foundation**
   - 70–89%: **Stage 2 — Active Management**
   - 90–100%: **Stage 3 — Proactive Defense**

4. **Certification override:** If Q9 confirmed SOC 2 Type II or HITRUST CSF,
   minimum stage is Active Management regardless of score. Note this explicitly.

5. **Enterprise blockers:** List every Enterprise trigger question answered No.
   These are reported separately from the overall score.

6. **Extra-protected PHI flag:** If Q8 is Yes, add: "Regulatory scope extends
   beyond standard HIPAA — 42 CFR Part 2 and/or additional state or federal
   obligations apply. Assessment does not cover these additional requirements."

---

### Step 3 — Document Analysis (run if Q7 confirmed documents exist)

For each document type the user has, invoke the appropriate toolkit tool and
capture its full structured output. Run tools in the order below when multiple
document types are present. See the **Invocation Contexts** section at the top
of this skill for the correct syntax in your environment.

#### 3a. Policies / procedures / security manual → HIPAA Gap Analysis

Invoke: **hipaa-gap-analysis** tool

Provide the document text. Capture: control coverage status, evidence
citations, gap severity ratings, confidence scores, remediation recommendations.

After the tool completes, cross-reference its findings against Phase 2 answers:
- Tool found a gap in a control the user said "Yes" to → flag as
  **Unverified / Gap Confirmed**
- Tool found coverage the user said "No" to → flag as
  **Potential Asset — Formalize**

If Q5 includes HITRUST, NIST, ISO 27001, or SOC 2, also invoke:

**framework-mapping** tool

Specify the target framework from Q5. Capture: per-section control mappings,
coverage summary, gaps by control.

#### 3b. Business Associate Agreement → BAA Review

Invoke: **baa-review** tool

Provide the BAA text. Capture: 9-provision assessment, risk level per
provision, gap descriptions, remediation language.

**If Q10 is Yes (subcontractors with PHI access):** After the BAA Review
completes, explicitly flag in your synthesis: "BAA Review covers the direct
vendor relationship. Verify whether subcontractor/offshore BAA chain is also
documented — this was flagged in orientation as an active risk."

Cross-reference: if Phase 2 Element 3, question 3.3 was answered Yes but BAA
Review finds deficiencies → flag as **Deficient — Remediation Required**.

#### 3c. Specific compliance question about a document → Compliance Q&A

Invoke: **compliance-qa** tool

Ask the user what they want to know. Capture: cited answer, confidence score,
gap flags.

#### 3d. Specific control to evaluate → Control Assessment

Invoke: **control-assessment** tool

Ask the user which control ID they want assessed (e.g., AC-2, 164.312(a)(1)).
Capture: evidence extraction, quality rating, severity classification.

#### 3e. State license or business registration

If a state license or business registration document is provided:

1. Extract: issuing state, license type, licensed activity or category,
   issuing regulatory agency
2. Use this to confirm or refine Q12 — the license is a definitive signal
   of which state applies and what the organization's regulated category is
3. If the license reveals a state not mentioned in Q12, or a regulated category
   that changes the applicable law picture, update the state law search:
   - `"[state] [license type] compliance obligations health data privacy 2026"`
   - `"[regulatory agency] data privacy requirements [business description from Q11]"`
4. Note any additional regulatory bodies identified — they may have enforcement
   authority or guidance that supplements federal HIPAA requirements

---

### Step 4 — Synthesis

Combine Phase 2 self-reported answers and Phase 3 document findings (if any)
into a unified posture statement.

Rules:
1. Document findings override self-reported answers when they conflict.
2. Each contradiction generates a labeled finding (Unverified / Gap Confirmed,
   Potential Asset — Formalize, or Deficient — Remediation Required).
3. The overall tier may shift downward if document analysis reveals gaps not
   self-reported.
4. Enterprise blockers confirmed by document analysis are labeled
   **Confirmed Critical**. Blockers from self-report only are labeled
   **Self-Reported Critical — verify**.
5. Risk profile amplifiers (extra-protected PHI, subcontractor access, no
   certifications) are called out in the synthesis preamble.

**Gap prioritization:** Classify each gap on two axes:

| | High Urgency | Low Urgency |
|---|---|---|
| **High Severity** | Priority 1 — act immediately | Priority 2 — plan in 30 days |
| **Low Severity** | Priority 3 — address in 60 days | Priority 4 — backlog |

Severity = regulatory exposure + enterprise deal impact.
Urgency = proximity of a review (Q6) + exploitability of the gap.

---

### Step 5 — Output

Write the posture snapshot to a file: `compliance-posture-YYYY-MM-DD.md`

Use the following structure exactly:

```markdown
---
assessment_date: YYYY-MM-DD
organization_role: [CE/BA/Both]
stage: [company stage from Q3]
primary_customer: [from Q4]
frameworks_in_scope: [from Q5]
extra_protected_phi: [Yes/No/Unsure]
subcontractor_phi_access: [Yes/No/Unsure]
certifications: [from Q9]
states_of_operation: [from Q12]
state_laws_in_scope: [list triggered laws, or "none beyond standard HIPAA"]
score_pct: [X%]
applicable_questions: [N]
yes_count: [N]
maturity_stage: [Stage 1/2/3 label]
tier: [tier label]
documents_analyzed: [list tools that ran]
---

# Compliance Posture Snapshot
**[Organization name if known, or "Your Organization"]**
**Assessment date:** [date]

## Context
[One-paragraph orientation summary from Step 1, including business description
from Q11 and state law flags from Q12]

[If extra-protected PHI: callout block noting additional regulatory scope]
[If subcontractor PHI access: callout block noting subcontractor BAA risk]
[If certifications exist: callout block noting existing certified controls]
[If state law flags active: callout block listing triggered states and laws]

## Maturity Stage
**[Stage label]** — [Score %] ([yes_count]/[applicable_questions])

[2–3 sentence description of what this stage means for this specific organization,
referencing their stage and customer type from orientation]

## Enterprise Blockers
[If none: "No enterprise blockers identified at this time."]
[If any: bulleted list of each blocker with label (Confirmed Critical or
Self-Reported Critical — verify) and a one-line consequence statement]

## Gap Findings by Element

### Element 1: Written Standards and Procedures
| Question | Answer | Status |
|----------|--------|--------|
[row per question with Yes/No and any finding label]

[Repeat for each element]

## Document Analysis Findings
[If Phase 3 did not run: "No documents were provided. Posture is based on
self-reported answers only. Document analysis is recommended to validate
these findings."]

[If Phase 3 ran: one section per tool, summarizing key findings and
contradictions with self-reported answers. Include evidence citations.]

## Synthesis Notes
[List all contradictions flagged during Step 4, labeled by type]

## State Law Considerations
[If Q12 named no states and no state license was provided: "No states of
operation were identified for this assessment. Note: all states have breach
notification laws with timelines that often differ from HIPAA's 60-day window —
verify state-specific requirements for any future incident."]

[If state law flags are active: one subsection per flagged state:

### [State] — [Law(s) identified by web search]
**Why it applies:** [1 sentence connecting the search findings to Q11 business activities]

**Obligations beyond HIPAA:** [from search results]
- [Key obligation 1]
- [Key obligation 2]
- [Key obligation 3 if applicable]

**Awareness gap:** [Likely covered / Uncertain / Not addressed — based on
self-reported answers and document analysis]

**Recommended next step:** State law compliance is beyond the scope of this
automated intake. Recommend consultation with a compliance professional
familiar with [state] obligations before pursuing customers in this jurisdiction.

[Repeat for each flagged state]

**Universal breach notification note:** State notification timelines vary
and are often shorter than HIPAA's 60-day window. Verify applicable
state-specific requirements. The most stringent requirement governs.]

## 30/60/90 Day Roadmap

### 30 Days — Act Immediately (Priority 1 gaps)
[Numbered list of specific actions. Each item: action + element it addresses +
flag if professional support is recommended]

### 60 Days — Plan Now (Priority 2 gaps)
[Same format]

### 90 Days — Schedule (Priority 3 gaps)
[Same format]

### Backlog (Priority 4 gaps)
[Brief list only — not full action items]

## Rote Handoff

[For each gap type found, map to the relevant Rote module and include the
appropriate framing from the handoff table. Only include entries where the
finding actually exists — do not include generic Rote marketing copy.]

[Close with the appropriate CTA based on maturity stage:]
- Foundation: "These findings suggest a structured program buildout is the
  right first step. Book a consultation at dangssolutions.com/book-consultation
  to build a roadmap with a fractional CCO before activating Rote."
- Active Management: "Rote is well-matched to a program at your stage. Join
  the waitlist or explore Professional tier at dangssolutions.com/rote."
- Proactive Defense: "Rote Enterprise is designed for continuous compliance
  at your maturity level. Learn more at dangssolutions.com/rote."

## Tool Chain Log
[List each toolkit tool invoked, the document it analyzed, and its output
status (completed / skipped / error). This section is for auditability.]
| Tool | Document | Status | Key finding count |
|------|----------|--------|------------------|
```

After writing the file, confirm the path to the user and summarize what was
found in 3–4 sentences. Do not repeat the full output — let the file speak.

---

## Notes for Skill Execution

- Do not skip Phase 1 orientation even if the user says "just run the assessment."
  The orientation answers are required for conditional question logic and output framing.

- Do not ask all questions at once. Present them element by element in Step 2.
  This makes the assessment feel like a structured conversation, not a form dump.

- If the user has no documents, Step 3 is skipped entirely. Note this in the
  output and recommend document analysis as a follow-up.

- If a user has certifications (SOC 2 Type II or HITRUST), acknowledge this
  upfront after orientation and explain that the assessment will identify any
  remaining gaps relative to their certification scope.

- The posture snapshot is self-contained. A user should be able to share it
  with a compliance officer, CCO, or Rote account setup without additional context.
