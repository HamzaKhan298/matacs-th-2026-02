# AI Prompts

## 1. Extraction Prompt

You are a precise information extraction system for contractor proposals.

Extract the following fields from the proposal text.  
Return ONLY valid JSON. Do not invent any information.  
If a piece of information is missing or unclear, use null.

Required JSON schema:
{
  "company_name": string or null,
  "ontario_licence": {
    "held_by_bidder": boolean,
  },
  "wsib_and_insurance": {
    "wsib_clearance": boolean,
    "liability_5m": boolean,
  },
  "experience": {
    "num_commercial_100kw_plus_last_5_years": number or null,
    "evidence": string or null
  },
  "price_cad": number or null,
  "timeline_weeks": number or null,
  "panel_warranty_years": number or null,
  "workmanship_warranty_years": number or null,
  "team_and_approach": {
    "named_project_lead": boolean,
    "crew_size": number or null,
    "safety_plan": boolean,
    "monitoring_months": number or null,
    "other_notes": string or null
  }
}

Proposal text:
"""
{{PROPOSAL_TEXT}}
"""