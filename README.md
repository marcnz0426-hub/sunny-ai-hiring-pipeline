# AI Hiring Pipeline with Jev Evaluator Agent

> Built by Marc Neil Zapanta | November 2026 | n8n Cloud & OpenRouter  
> Portfolio Project #3 | Built for Wunder Management — AI Operator Role  
> **Status:** Architecture Designed & Hardened | Sprint Build in Progress

---

## 🎯 Problem Statement

Recruitment funnels for fast-scaling organizations like Wunder Management face a critical operational bottleneck: reviewing hundreds of incoming creator/operator applications manually takes hours per candidate, while off-the-shelf automated ATS screeners rely on naive keyword matching. This leads to high false rejection rates for non-traditional candidates (such as career pivoters with deep frontline CX experience) and lets unqualified applicants slip through to human interviewers.

Furthermore, traditional intake forms ask generic static questions that fail to test whether an applicant actually possesses the technical depth they claim on their resume.

## 💡 The Solution

An autonomous two-stage hiring intelligence engine built in **n8n** powered by **OpenRouter (Gemini 2.0 Flash / LLaMA 3.1)**:
1. **Intake & Multi-Dimensional Scoring:** Ingests live candidate submissions via webhooks and tasks an AI evaluator agent (**"Jev"**) with evaluating applicants across 5 granular dimensions (`experience`, `portfolio_quality`, `cover_letter_depth`, `availability_fit`, and `location_match`).
2. **Dynamic Personalized Qualifying:** Instead of static forms, Jev analyzes the candidate's specific background and automatically drafts 3–5 tailored technical qualifying questions targeting specific claims or gaps in their application.
3. **Automated Stakeholder Dispatch:** Asynchronous branching routes passing candidates to a secondary evaluation webhook, generates an executive candidate dossier for the hiring manager, and prepares interview briefs with suggested probe areas for the technical interviewer.
4. **Resilient Production Hardening:** Implements 3x exponential backoff retries, JSON Schema repair loops, a Google Sheets Dead-Letter Queue (DLQ), and out-of-band email alerts on failure.

---

## 🛠️ Tools & Technologies Used

- **Primary Orchestrator:** n8n Cloud
- **AI Evaluator Engine:** OpenRouter API (`google/gemini-2.0-flash-exp:free` / `meta-llama/llama-3.1-8b-instruct:free`)
- **Evaluator Agent:** "Jev" (Structured JSON Evaluator Persona)
- **Data Persistence & Audit:** Google Sheets API (Applications, Qualifying Responses, Error Log DLQ)
- **Communications:** Gmail OAuth2 (Applicant, Hiring Manager, Interviewer)
- **Data Protocols:** Webhooks (REST POST), JSON Schema v7 validation

---

## 📋 Architectural Workflow & Core Pillars

### Stage 1: Application Intake & AI Screening Pipeline
```
[Webhook Intake] ➡️ [Sanitize & Validate] ➡️ [Jev Evaluator Agent] ➡️ [3-Tier Score Router]
                                                                        ├── ≥70 PASS  ➡️ [Generate 5 Custom Questions] ➡️ [Email Applicant + Sheets Log]
                                                                        ├── 40-69 MID ➡️ [Human Review Flag] ➡️ [Email Manager + Sheets Log]
                                                                        └── <40 FAIL  ➡️ [Polite Rejection] ➡️ [Email Applicant + Sheets Log]
```

### Stage 2: Dynamic Qualifying & Stakeholder Coordination
```
[Webhook Response] ➡️ [Match Intake Context] ➡️ [Jev Stage 2 Evaluator] ➡️ [Advancement Router]
                                                                            ├── ≥70 ADVANCE ➡️ [Advancement Email to Applicant]
                                                                            │                ➡️ [Executive Brief to Hiring Manager]
                                                                            │                ➡️ [Interview Prep Brief to Interviewer]
                                                                            └── <70 HOLD    ➡️ [Manual Hold Report to Manager]
```

---

## 📸 Architectural Blueprints & System Design Gallery

### 1. End-to-End Hiring Funnel Architecture
![Full Funnel Architecture](screenshots/01-system-architecture-flowchart.png)

### 2. System Design & Architectural Decision Matrix
![Architectural Decision Matrix](screenshots/02-architectural-decision-matrix.png)

### 3. Production Data Schemas & AI Contracts
![Data Schemas Spec](screenshots/03-data-schemas-spec.png)

### 4. Multi-Layer Fault Tolerance & Dead-Letter Queue
![Error Handling Architecture](screenshots/04-error-handling-architecture.png)

### 5. n8n Two-Workflow Execution Blueprint
![n8n Workflow Structure](screenshots/05-n8n-workflow-structure.png)

### 6. Jev AI Evaluator — Stage 1 System Prompt & Payload Spec
![Jev Stage 1 Evaluation Prompt Payload](screenshots/06-jev-stage1-evaluation-prompt-payload.png)

### 7. Code Node: Defensive JSON Parsing & Contact Dossier Merger
![Code Node JSON Parser & Merger](screenshots/07-code-node-json-parser-and-merger.png)

### 8. Live Candidate Qualifying Email & Gmail Execution Verification
![Live Candidate Qualifying Email](screenshots/08-live-candidate-email-mobile.jpg)
![n8n Gmail Node Execution](screenshots/09-n8n-gmail-node-execution.jpg)

### 9. Google Sheets Audit Database Logging & Verified Execution
![n8n Google Sheets Node Execution](screenshots/10-n8n-google-sheets-node-execution.jpg)
![Live Google Sheets Database](screenshots/11-live-google-sheets-applications-db.png)

### 10. End-to-End Workflow 1 Canvas & Sequential Architecture Hardening
![Workflow 1 Canvas Sequential Architecture](screenshots/12-workflow-1-canvas-sequential-architecture.jpg)
![Applications DB Retry on Fail Settings](screenshots/13-applications-db-retry-settings.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 10. End-to-End Workflow 1 Canvas & Sequential Architecture Hardening
![Workflow 1 Canvas Sequential Architecture](screenshots/12-workflow-1-canvas-sequential-architecture.jpg)
![Applications DB Retry on Fail Settings](screenshots/13-applications-db-retry-settings.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 9. Google Sheets Audit Database Logging & Verified Execution
![n8n Google Sheets Node Execution](screenshots/10-n8n-google-sheets-node-execution.jpg)
![Live Google Sheets Database](screenshots/11-live-google-sheets-applications-db.png)

### 10. End-to-End Workflow 1 Canvas & Sequential Architecture Hardening
![Workflow 1 Canvas Sequential Architecture](screenshots/12-workflow-1-canvas-sequential-architecture.jpg)
![Applications DB Retry on Fail Settings](screenshots/13-applications-db-retry-settings.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 10. End-to-End Workflow 1 Canvas & Sequential Architecture Hardening
![Workflow 1 Canvas Sequential Architecture](screenshots/12-workflow-1-canvas-sequential-architecture.jpg)
![Applications DB Retry on Fail Settings](screenshots/13-applications-db-retry-settings.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 11. Live Borderline Review (MAYBE 40-69) Internal Email & Sheets Audit Row
![Live Hiring Manager Flag Email](screenshots/14-live-hiring-manager-flag-email.png)
![Live Google Sheets Row 3 Borderline Candidate](screenshots/15-live-google-sheets-row-3-borderline.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 12. Enterprise Composite Database Link & Live Borderline Audit
![Live Hiring Manager Composite Link Email](screenshots/16-live-hiring-manager-composite-link-email.png)
![Live Google Sheets Borderline Row](screenshots/17-live-google-sheets-borderline-row.png)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 13. Live Candidate Rejection Email & 3-Tier Database Master Ledger
![Live Rejection Email](screenshots/18-live-rejection-email.png)
![Live Google Sheets 3 Candidate Archetypes](screenshots/19-live-google-sheets-3-candidate-archetypes.jpg)

### 14. Live Technical Qualifying Questions Email & PASS Execution Verification
![Live Qualifying Questions Email](screenshots/20-live-qualifying-questions-email-delivered.jpg)
![n8n PASS Branch Execution](screenshots/21-n8n-pass-branch-node-execution.jpg)

### 15. Dead-Letter Queue (DLQ) & Live Out-of-Band Incident Alerting
![Workflow 1 Canvas with DLQ Branch](screenshots/22_workflow1_dlq_canvas_overview.jpg)
![DLQ Log Error to Sheet Execution](screenshots/28_dlq_log_error_to_sheet_executed.png)
![Live Google Sheets Error Log Quarantined Row](screenshots/30_google_sheets_error_log_verified.png)
![Live Admin Critical Incident Email Delivered](screenshots/31_dlq_critical_incident_email_verified.png)

### 16. Workflow 2: Stage 2 Technical Qualifying Evaluator & State Hydration
![Lookup Stage 1 Profile Real-Time Hydration](screenshots/34_workflow2_lookup_stage1_profile_executed.jpg)
![OpenRouter Jev Stage 2 Technical Assessment Success](screenshots/38_workflow2_openrouter_stage2_evaluation_success.jpg)
![Defensive Code Parser Scorecard Output](screenshots/39_workflow2_code_parser_stage2_evaluation_verified.jpg)
![Live Google Sheets Qualifying Responses Master Ledger](screenshots/51_workflow2_google_sheets_row4_advance_verified.png)

### 17. Stage 2 Idempotency Gate (Duplicate Interception & $0 Waste Policy)
![Stage 2 Idempotency Gate Canvas Architecture](screenshots/59_workflow2_idempotency_gate_canvas_overview.png)
![Idempotency If Node True Branch Execution](screenshots/66_workflow2_already_submitted_if_node_true_branch.jpg)
![Live Candidate Duplicate Submission Notice Delivered](screenshots/64_workflow2_live_duplicate_notice_email_delivered.png)

### 18. Multi-Stakeholder Stage 2 Notification Suite (ADVANCE & HOLD Verified)
![Live HOLD Hiring Manager Human Review Alert with Row #2 Reference](screenshots/45_workflow2_hold_hiring_manager_email_delivered.jpg)
![Live Candidate Final Technical Interview Invitation](screenshots/56_workflow2_live_candidate_interview_invite_email.png)
![Live Hiring Manager ADVANCE Cleared Executive Brief](screenshots/57_workflow2_live_hiring_manager_advance_brief_email.png)
![Live Technical Interviewer Tailored Pre-Briefing Guide](screenshots/58_workflow2_live_interviewer_prebrief_guide_email.jpg)

---

## 🛡️ Defensive Engineering & Reliability

1. **3x Exponential Backoff Retry:** Protects against OpenRouter rate limits (HTTP 429) and upstream model cold starts.
2. **Schema Auto-Repair Loop:** If an LLM response fails strict JSON parsing, an auto-healing prompt is triggered to parse the raw text into valid JSON before failing.
3. **Dead-Letter Queue (DLQ):** Unprocessable bundles are quarantined in a dedicated Google Sheets `Error Log` tab with payload context, preventing silent drops.
4. **Out-of-Band Incident Alerting:** Alerts system administrators via an independent email route when an exception exhausts its retry budget.

---

## 🎤 Interview-Ready Talking Points (Why This Wins)

- **Bridging CX Ops to AI Systems:** *"In my 5+ years handling frontline operations and high-volume ticketing, I saw firsthand where rigid rule-based systems break and alienate users. I designed Jev not to replace human discretion, but to handle the 80% repetitive cognitive screening while surfacing gray-zone candidates directly to human managers."*
- **Two-Stage Decoupling:** *"Rather than creating a monolithic workflow that blocks on candidate replies, I architected a decoupled two-stage webhook pipeline in n8n. Stage 1 executes in seconds; Stage 2 activates asynchronously when the candidate submits their qualifying answers."*
- **Model Agnostic & Cost-Effective:** *"By integrating OpenRouter via standard HTTP nodes in n8n rather than proprietary platform nodes, the entire system can hot-swap between free and paid frontier models with zero workflow rebuilds."*
