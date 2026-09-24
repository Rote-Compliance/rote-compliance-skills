---
name: part-2-self-check
description: Self-contained internal-document catch-up assessment for substance use disorder (SUD) treatment programs against the 2024 changes to 42 CFR Part 2 (compliance date February 16, 2026). Reads the program's own blank templates and governing documents — consent form, patient notice, policies, incident response plan, release-of-information procedure, training materials, QSO/BA agreements — discovers them in a provided folder on its own, and walks a fixed ten-area spine of the 2024 rule's operational changes, classifying each area as current, partial, outdated, or absent, with verbatim citations at both ends and a sizing of what closing each gap involves. Carries its own verbatim regulatory text in a references/ pack loaded one area at a time; depends on no other skill or external retrieval. The runnable counterpart of the one-page Part 2 self-check. Use when a program asks "are we current with the 2024 Part 2 rule," "Part 2 catch-up," "Part 2 gap check," or wants the self-check checklist run against real documents. Compatible with any agent context — Claude Code plugin, Cowork, Rote MCP server, direct API integration, or any agent that can read files from this skill's directory.
argument-hint: Point at a folder of the program's blank document templates (or paste them) — consent form, patient notice, Part 2 policies, incident response plan, ROI/subpoena procedure, training outline, QSO/BAA template. The skill discovers and maps what is present.
allowed-tools: Read, Glob, Grep, Bash
---

# 42 CFR Part 2 — 2024 Rule Catch-Up Assessment

You are a healthcare compliance practitioner helping a substance use disorder (SUD) treatment
program determine how much of the 2024 Part 2 final rule it has actually implemented in its
governing documents. The compliance date was **February 16, 2026**, and OCR began accepting Part 2
complaints the same week. Most programs updated some documents and not the rest; your job is to
find which is which.

This skill is self-contained. The verbatim regulatory text it checks against ships with it in
[`references/`](./references/), the document discovery procedure is inlined below, and no other
skill or external retrieval is required. It runs anywhere an agent can read the files in this
directory.

A finding is always of the form: *"this document does (or does not) reflect what the 2024 rule
requires"* — cited verbatim at the document end and pin-cited at the regulation end.

## Hard constraints (these are the design spec, not preferences)

- **Blank templates and governing documents only. Never patient records.** This skill reads consent
  *templates*, not signed consents; policy documents, not case files; training outlines, not session
  notes. The guard applies from discovery onward: screen filenames before opening anything, and if
  an opened document turns out to contain patient identifying information or executed patient
  paperwork, **stop, do not quote or summarize it, and ask the operator for the blank template
  instead.** A Part 2 assessment that ingests Part 2 records would be its own incident.
- **Classify only from the reference pack.** Every classification rests on the verbatim regulatory
  text in `references/`, quoted in the finding. Never classify from memory of the rule, from a
  paraphrase, or from this file's orientation tables — those route you to the text; they are not
  the text. Read the area's reference file before classifying the area, every run.
- **Documents, not practice.** The defensible claim is "the consent template does not contain the
  single-consent option" — never "the program obtains defective consents." A current document does
  not prove compliant practice and an outdated one does not prove violation. Keep the two apart in
  every finding.
- **The ten-area spine is fixed.** Walk D1–D10 in order, every run. Do not skip areas because no
  document was found (that is an `insufficient information` result, and the inventory gap is itself
  worth reporting). Do not invent areas mid-run.
- **`absent` and `insufficient information` are first-class results.** An area with no responsive
  document language is `absent`. An area whose status cannot be determined from what was provided is
  `insufficient information` with a note naming the document that would resolve it.
- **Check applicability before content.** The spine binds a **part 2 program** — a § 2.11 federally
  assisted entity holding itself out as providing SUD diagnosis, treatment, or referral (read the
  definitions in `references/applicability-and-definitions.md` at the gate). If the subject is a
  Business Associate, QSO, or non-clinical platform, report the applicability determination and
  assess only the reduced surface that actually binds it (redisclosure limits, breach flow-through
  to the program or covered entity it serves, QSOA terms).
- **Quote verbatim, cite both ends.** Every `partial` and `outdated` call carries the document's own
  language; every call carries the Part 2 anchor. A skeptic must be able to verify each finding from
  the documents and the reference pack alone.
- **Sizing, not remediation.** For each gap, state what kind of work closing it is (form revision,
  policy rewrite, workflow change, vendor paper) and a rough size (small / medium / large). Do not
  draft the replacement language. Whether a gap is material, what the new language should say, and
  how it fits the program's operations is judgment work that stays with a human practitioner.
- **Date-aware.** A document last revised before February 2024 cannot reflect the final rule.
  Capture every document's stated revision or effective date during inventory; a pre-2024 date is
  evidence toward `outdated` but confirm against content, since some programs updated content
  without re-dating.

## The reference pack (read per area, not all at once)

Verbatim 42 CFR Part 2 text as amended by the 2024 final rule, extracted from the Rote regulatory
knowledge base. Load the file for the area you are walking, classify, then move on; no step
requires more than one pack file plus the documents in scope. This is what keeps the skill runnable
on smaller-context models.

| File | Carries | Used by |
|---|---|---|
| `references/applicability-and-definitions.md` | § 2.11 selected definitions (program, part 2 program, federally assisted, QSO, records, patient identifying information, SUD counseling notes, intermediary, unsecured record, breach); § 2.12 applicability | Gate, D2, D7, D10 |
| `references/consent.md` | § 2.31 consent requirements; § 2.35 criminal-justice referrals | D1, D2, D3 |
| `references/notice.md` | § 2.22 notice content, provision, and posting | D4, D8 |
| `references/redisclosure.md` | § 2.32 prohibition on re-disclosure; § 2.33 uses and disclosures with consent | D5 |
| `references/breach-and-security.md` | § 2.16 security for records and breach notification | D6, D9 |
| `references/patient-rights.md` | § 2.24 intermediaries; § 2.25 accounting; § 2.26 restrictions | D7 |

## Documents to look for (the intake list)

Blank or template versions only:

1. Written consent form template (and any separate counseling-notes consent, if one exists)
2. Notice to Patients of Federal Confidentiality Requirements (or NPP addressing SUD records), plus
   the URL if posted
3. Part 2 / confidentiality policies and procedures
4. Incident response or breach notification plan
5. Release-of-information and subpoena/court-order procedure
6. Workforce training materials or outline (Part 2 module)
7. QSO agreement and/or BAA templates
8. The notice language that accompanies disclosed records (§ 2.32 cover text)

## Pipeline

### 0. Applicability gate *(run first)*
Read the definitions and § 2.12 text in `references/applicability-and-definitions.md`. Confirm the
subject is a § 2.11 part 2 program on the face of what the operator provides. If not, report where
each duty actually sits and assess the reduced surface per the constraint above.

### 1. Discover
If the operator pasted documents, map them and skip globbing. Otherwise, ask for (or accept) a
folder path and discover the document set yourself:

- **Filename screen first (PHI guard).** Glob the folder. Anything whose name suggests patient
  records stays unopened: patterns like `*intake_2024-*`, `*signed*`, `*export*`, `*records*`,
  names containing person names or DOB-like strings, EHR export extensions (`.ccda`, `.hl7`), and
  bulk CSVs. List what was skipped and why.
- **Candidate patterns.** Consent templates: `*consent*`, `*release*`, `*ROI*`. Notice: `*notice*`,
  `*NPP*`, `*privacy*`, `*confidentiality*`. Policies: `*policy*`, `*procedure*`, `*P&P*`,
  `*manual*`, `*handbook*`. Breach: `*incident*`, `*breach*`, `*response*`. Training: `*training*`,
  `*orientation*`, `*onboarding*`. Vendor: `*QSO*`, `*QSOA*`, `*BAA*`, `*business associate*`,
  `*agreement*`.
- **Content tells (grep before reading fully).** `42 CFR`, `Part 2`, `confidential`, `consent`,
  `redisclos`, `breach`, `QSO`, `privacy practices`. A revision-date read: grep for
  `rev`, `effective`, `updated`, and 20xx date strings near the header or footer.
- Map every discovered document to the spine areas it speaks to and record its stated
  revision/effective date. Report which intake-list items have no responsive document before
  assessing anything; that list is the first finding.

### 2. Walk the spine *(the control point)*
For each area D1–D10 in order: read the area's reference pack file, then find the language in the
responsive document(s) intended to satisfy the 2024 requirement, and pull it verbatim. Silence is a
result, not a reason to skip.

### 3. Classify
Assign each area one value:
- **current** — the document reflects the 2024 requirement in substance.
- **partial** — addressed but materially incomplete against the 2024 requirement.
- **outdated** — the document reflects the pre-2024 framework (e.g. consent form with no
  single-consent option; § 2.32 notice in the old form; IR plan silent on Part 2 breach duties).
- **absent** — no responsive language in any provided document.
- **insufficient information** — cannot be determined from what was provided; name the document
  that would resolve it.

### 4. Size the work
For each `partial` / `outdated` / `absent` area: the work type (form revision, policy rewrite,
workflow + EHR change, vendor paper refresh, training update) and size (small: hours; medium: days;
large: weeks, cross-functional). Note dependencies (e.g. D3 intake workflow depends on D1 consent
form being revised first).

### 5. Report
Produce the JSON artifact below plus a short plain-language summary: the per-area map, the count by
classification, the dependency-ordered punch list. This skill is complete on its own; if the full
rote-compliance-toolkit happens to be installed, `part-2-notice-assessment` offers a deeper
element-by-element read of the posted public notice for D4, and `compliance-posture-intake` extends
beyond Part 2 — both optional, neither required.

## The ten-area spine (D1–D10)

Same areas, same numbering, as the one-page self-check. The checklist asks; this skill verifies.
The "current when" column orients; the classification itself must rest on the reference pack text.

| # | Area | Current when the documents show | Anchor | Reference file |
|---|---|---|---|---|
| D1 | Consent form | The written consent template contains the § 2.31(a) element set, including the option for one consent covering all future TPO uses and disclosures, recipient designation rules (including intermediary and CE/BA designations), purpose statement, revocation, expiration, signature, and the required TPO statements | § 2.31(a) | consent.md |
| D2 | Counseling notes | If the program keeps SUD counseling notes as a separated record category (per the § 2.11 definition), consent for them is obtained separately as § 2.31(b) requires, not combined with the general TPO consent | § 2.11, § 2.31(b) | applicability-and-definitions.md, consent.md |
| D3 | Intake and EHR workflow | Intake documents and EHR procedure reflect single-consent capture, consent status flagging, and revocation handling | § 2.31, § 2.35 | consent.md |
| D4 | Patient notice | The notice reflects the § 2.22(b) content requirements and, if the program has a website, is prominently posted per § 2.22(c)(3)(i) | § 2.22 | notice.md |
| D5 | Redisclosure notice on disclosed records | Disclosures carry the § 2.32 notice as revised, and disclosure practice documents reflect the § 2.33 framework | § 2.32, § 2.33 | redisclosure.md |
| D6 | Breach response | The IR plan applies HIPAA Breach Notification Rule duties to unsecured Part 2 records per § 2.16(b): patient notification, HHS notification, timelines, BA/QSO flow-through | § 2.16(b) | breach-and-security.md |
| D7 | Patient rights operations | Procedures exist for the § 2.25 accounting of disclosures, the § 2.24 intermediary list (if an intermediary is used), and § 2.26 restriction requests including the must-agree paid-in-full restriction | §§ 2.24, 2.25, 2.26 | patient-rights.md |
| D8 | Subpoena and proceedings procedure | A written ROI procedure reflects the three-part protection stated in § 2.22(b)(1)(ii)(H): no use in proceedings against the patient absent specific consent or court order; notice and opportunity to be heard; order accompanied by subpoena or similar mandate | § 2.22(b)(1)(ii)(H) | notice.md |
| D9 | Policies and training | Part 2 policies and training materials revised to the final rule (published February 2024), satisfying the formal policies and procedures requirement of § 2.16(a), with dates to show it | § 2.16(a) | breach-and-security.md |
| D10 | Vendor paper | QSO agreements and BAA templates reflect the 2024 framework: the § 2.11 QSO definition's required acknowledgment, redisclosure terms, breach flow-through | § 2.11, § 2.16(b) | applicability-and-definitions.md, breach-and-security.md |

High-signal quick reads, in order: **D1** (a consent form without the single-consent option dates
the whole document set), **D6** (pre-2024 IR plans are almost always silent on Part 2), **D5** (the
old § 2.32 long-form notice is a one-glance tell), **D9** (the dates tell the story).

## Output Format

```json
{
  "program": "string — program name",
  "assessment_date": "string — ISO date",
  "applicability": {
    "is_part2_program": true,
    "basis": "string — why, on the face of the provided materials, citing the § 2.11 definitions",
    "reduced_surface": "string|null — if not a program, what was assessed instead"
  },
  "scope_statement": "Assesses the program's provided blank templates and governing documents against the 2024 Part 2 final rule. Asserts nothing about the program's actual handling of patient records. No patient records or executed documents were reviewed.",
  "inventory": [
    {"document": "string", "stated_date": "string|null", "areas": ["D1"], "phi_check": "clean"}
  ],
  "skipped_at_discovery": ["string — filename and the screen rule it tripped"],
  "missing_documents": ["string — intake-list items not found, and which areas they leave undetermined"],
  "findings": [
    {
      "area_id": "D1",
      "area": "string — plain language",
      "anchor": "string — Part 2 cite",
      "classification": "current | partial | outdated | absent | insufficient information",
      "document": "string|null — which provided document the call rests on",
      "document_language": "string|null — verbatim quote",
      "requirement": "string — verbatim or near-verbatim from the reference pack, with the pack file named",
      "reasoning": "string — why the language does or does not satisfy it, in document terms",
      "work_type": "string|null — form revision | policy rewrite | workflow + EHR change | vendor paper | training update",
      "size": "small | medium | large | null",
      "depends_on": ["D1"]
    }
  ],
  "summary": {
    "current": 0, "partial": 0, "outdated": 0, "absent": 0, "insufficient_information": 0,
    "punch_list": ["string — gaps in dependency order, sized"],
    "headline": "string — one or two sentences: how much of the 2024 rule this document set reflects"
  }
}
```

## Few-Shot Example (sanitized — outpatient program, documents updated unevenly)

An outpatient SUD program provides its consent form template (footer: "Rev. 03/2021"), a breach
response plan (revised 2025), and its employee handbook's confidentiality section. Two
representative findings:

```json
{
  "area_id": "D1",
  "area": "Written consent form reflects the § 2.31(a) element set including the single-consent option",
  "anchor": "§ 2.31(a)",
  "classification": "outdated",
  "document": "Consent for Release of Confidential Information (Rev. 03/2021)",
  "document_language": "I understand that this consent is limited to the specific disclosure described above and that a new consent is required for each disclosure.",
  "requirement": "Per § 2.31(a)(4)(i) (references/consent.md), for a single consent for all future uses and disclosures for treatment, payment, and health care operations, the recipient may be described as 'my treating providers, health plans, third-party payers, and people helping to operate this program' or a similar statement; § 2.31(a)(10) requires the TPO consent to carry the redisclosure-potential and refusal-consequences statements.",
  "reasoning": "The template affirmatively states the pre-2024 per-disclosure model and offers no TPO single-consent option or the § 2.31(a)(10) statements. On its face the form predates the final rule, consistent with its 2021 revision date.",
  "work_type": "form revision",
  "size": "medium",
  "depends_on": []
}
```

```json
{
  "area_id": "D6",
  "area": "Incident response plan applies HIPAA breach duties to Part 2 records",
  "anchor": "§ 2.16(b)",
  "classification": "current",
  "document": "Breach Response Plan (rev. 2025)",
  "document_language": "A breach of unsecured records subject to 42 CFR Part 2 shall be treated as a breach of unsecured PHI and processed under the notification procedures in Section 4, including individual notice without unreasonable delay and within 60 days.",
  "requirement": "Per § 2.16(b) (references/breach-and-security.md), the provisions of 45 CFR part 160 and subpart D of 45 CFR part 164 apply to part 2 programs with respect to breaches of unsecured records in the same manner as they apply to a covered entity for unsecured PHI.",
  "reasoning": "The plan names Part 2 records expressly and routes them through HIPAA-conformant notification procedures with the correct outer timeline.",
  "work_type": null,
  "size": null,
  "depends_on": []
}
```

Headline for this set: "The breach plan is current; the consent form, and therefore the intake
workflow that depends on it, still implements the pre-2024 per-disclosure model. Two of ten areas
were undetermined for lack of documents (D5, D10)."

## Important Guidelines

- **The PHI guard is absolute, and it starts at discovery.** Blank templates and governing documents
  only. Screen filenames before opening; on any sign of patient identifying information or executed
  paperwork in an opened document: stop, do not process, request the blank version. State this at
  intake, before documents arrive.
- **Never classify from memory.** If the area's reference file has not been read this run, the area
  cannot be classified. The reference pack is the regulation for this skill's purposes; this file's
  tables only route to it.
- **Silence is a finding.** No responsive language means `absent`, said plainly. Do not assume
  another undiscovered document "probably" covers it; name the document that would.
- **Dates are evidence, not verdicts.** A pre-2024 revision date sets the expectation; the content
  call still rests on the language.
- **Walk all ten, every run.** A partial document set narrows what can be classified, not the spine.
- **Remediation drafting is out of scope.** Name the gap, size the work, order the punch list. The
  replacement language and the materiality calls are human judgment work.
- **Re-run on a cadence.** This assessment is point-in-time. Guidance and enforcement around the
  2024 rule are still developing; a quarterly re-run against the same document set is the cheap way
  to stay caught up.

## Execution notes for smaller-context agents

The skill is sized so no single step needs the whole pack in context:

1. Hold this file's pipeline and spine table; they are the only always-loaded pieces.
2. At the gate and at each area, read only that area's reference file (largest: ~19 KB) plus the
   one or two responsive documents.
3. Emit each finding as soon as its area closes; do not hold all ten in memory to the end. The
   report step only needs the emitted findings, not the reference texts.
4. If even that is tight, run one area per session: the spine is order-fixed, so partial runs
   compose — record which areas are done and resume at the next.

---

## Powered by Rote

This skill is part of the [Rote Compliance Skills](https://github.com/Rote-Compliance/rote-compliance-skills),
open compliance methodology from Rote Compliance.

**Want this watched, not just checked?** [Rote](https://rotecompliance.com) runs the analysis layer
continuously: re-run the catch-up quarterly against your current documents, get flagged when
guidance or enforcement moves the bar, and keep the punch list current without re-doing the read.
