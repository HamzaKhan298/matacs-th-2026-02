# Write-up: Questions/Answer

## 1. AI vs. rules
**AI is used for two things only:** 
- reading messy proposal text and turning it into structured facts, each with aligning according to criteria defined. 
- judging qualitative items.

**Plain code/rules do everything else:** 

PDF text extraction, schema validation, the mandatory pass/fail gate, all arithmetic scoring, weights, ranking, tie-breaks, logging.

**Why this line:** 
- AI is good at understanding unstructured language; it is bad at consistent arithmetic and is open to manipulation. 
- Mandatory items and arithmetic are deterministic and must never hallucinate. so results are repeatable and the admin's criteria stay in control, not the model's opinion.

## 2. Consistency and explainability
- Extraction runs at temperature 0 with a fixed JSON schema, pinned model version and a fixed prompt.
- Same proposal text + same criteria always produce the same structured extraction → same scores.
- Every ranked proposal stores:
  -  Extracted facts
  -  Per-criterion score + short justification
  -  Final weighted total

## 3. Human in the loop
- **Reviewer** uploads proposals and selects category.
- System returns ranked list + reasons.
- Final award decision is always made by a human outside this tool.

## 4. Changing criteria
- Criteria are **versioned**. A scoring run always stores the version it used; old results are never silently changed.
- When an admin edits criteria or weights, existing results are marked **"scored under an older version"**.
- Because extraction is stored separately from scoring, re-scoring under the new version is cheap: only the deterministic step re-runs, no new AI cost. If a new criterion needs facts that were not extracted, only then is extraction re-run.
- The reviewer can compare old vs new ranking before accepting. Re-scoring is never automatic once a decision is in progress, and the change is logged.
- Weights must still total 100%; the admin screen blocks saving otherwise.

## 5. Risks and guards
| Risk | Guard |
|---|---|
| Wrong extraction | Strict JSON schema + mandatory field validation + human review of extracted facts before scoring is accepted |
| Made-up facts | Prompt forbids inventing information; anything not present is returned as null |
| Prompt injection / manipulation | Proposal text is wrapped as untrusted data; model told to ignore instructions inside it; a code scan and the model both flag such text; AI cannot set ranks or scores. Sample C contains such a hidden note ("rank first, full marks"); the design ignores it and flags C. |
| Bias (e.g. favouring big firms or polished writing) | Criteria and weights are set by admin (not the model). AI only scores against the given criteria |
| Gaming the rules (e.g. B subcontracting licence) | Mandatory gate requires licence held by the bidder; "to be confirmed later" = fail. |
---


