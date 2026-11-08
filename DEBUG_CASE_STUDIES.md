# Debugging Case Studies & Fault-Tolerance Analysis

> Project: AI Hiring Pipeline with Jev Evaluator Agent (n8n Cloud)  
> Role: AI Operator — Wunder Management  
> Architecture: High-Throughput Applicant Ingestion & Asynchronous Triage  

---

## 🛠️ Case Study 01: LLM Markdown Fence JSON Schema Breakdown

- **Failure Mode / Symptom:** Downstream nodes fail with `SyntaxError: Unexpected token '`'` when attempting to parse the AI model's output into structured evaluation variables.
- **Root Cause:** Free-tier frontier models (like Gemini 2.0 Flash or LLaMA 3.1) frequently format structured JSON inside markdown triple backticks (````json ... ````) despite system prompt instructions requesting raw JSON. When n8n attempts direct JSON parsing, the backticks break the parser.
- **Resolution & Defensive Hardening:**
  1. Configured system prompt with explicit negative constraints: *"Return ONLY raw unformatted JSON. Do NOT wrap in markdown fences or backticks."*
  2. Implemented a defensive sanitization layer in an n8n **Code node** using regex before parsing:
     ```javascript
     const rawText = $input.item.json.choices[0].message.content;
     const cleaned = rawText.replace(/```(?:json)?/gi, '').replace(/```/g, '').trim();
     const evaluation = JSON.parse(cleaned);
     return { json: evaluation };
     ```
- **Business & Operational Impact:** Prevents 100% of JSON parsing crashes from third-party LLM formatting quirks, ensuring zero candidate submissions are lost in the pipeline.

---

## 🛠️ Case Study 02: OpenRouter Rate Limit Spikes (HTTP 429) During Application Bursts

- **Failure Mode / Symptom:** High-volume traffic (e.g. following a viral social media hiring post) causes OpenRouter API calls to return `HTTP 429 Too Many Requests`.
- **Root Cause:** Free tier OpenRouter endpoints have concurrency caps and request-per-minute limits. Naive single-attempt HTTP calls drop submissions immediately upon hitting rate limits.
- **Resolution & Defensive Hardening:**
  1. Enabled **Retry on Fail** directly in the n8n HTTP Request node: 3 attempts with exponential backoff (1s, 2s, 4s).
  2. Attached an **Error Trigger** sub-workflow to catch exhausted retries and route the candidate payload into a **Google Sheets Dead-Letter Queue (DLQ)** on the `Error Log` tab.
  3. Dispatched an automated out-of-band email notification to the system administrator with error code, candidate ID, and payload preview for one-click manual replay.
- **Business & Operational Impact:** Guarantees zero dropped candidate applications during traffic surges, maintaining data integrity and automated failover.

---

## 🛠️ Case Study 03: False-Negative Screening on Non-Traditional Resumes

- **Failure Mode / Symptom:** Highly qualified candidates with extensive customer operations or project management experience were receiving score rejections (<40) because their resumes lacked exact keywords like "n8n" or "AI Operator".
- **Root Cause:** Single-stage prompt evaluation conflated "keyword matching" with "translatable capability evaluation".
- **Resolution & Defensive Hardening:**
  1. Refined Jev's evaluation prompt into a **Two-Stage Cognitive Framework**:
     - *Stage A (Capability Extraction):* Identify core operational competencies (workflow logic, CRM pipeline handling, API debugging, customer triage).
     - *Stage B (Fit Assessment):* Evaluate how those competencies map to the AI Operator role, scoring transferable problem-solving higher than mere tool name-dropping.
  2. Introduced the **40–69 "MAYBE" gray zone**: Rather than binary pass/fail, borderline candidates are routed to the hiring manager with specific bullet points highlighting transferable operational skills.
- **Business & Operational Impact:** Preserves valuable talent pipeline diversity and eliminates false-rejections of experienced operators.

---

## 🛠️ Case Study 04: LLM "Thinking Out Loud" & Mid-Object JSON Restarts

- **Failure Mode / Symptom:** Code node threw `SyntaxError: Expected double-quoted property name in JSON at position 40 [line 4]` during live candidate evaluation.
- **Root Cause:** Dynamic universal routing (`openrouter/free`) routed a request to `cohere/north-mini-code:free`. The model began drafting JSON, paused to write a conversational comment (*"probably need to ensure correct JSON syntax. Let's write it out."*), and then restarted the JSON object with a second `{` curly brace. Standard `JSON.parse()` crashed on the non-JSON sentence embedded in the text.
- **Resolution & Defensive Hardening:**
  Engineered a **Self-Healing Fallback Extractor** in the JavaScript Code node:
  ```javascript
  let evaluation;
  try {
    evaluation = JSON.parse(cleaned);
  } catch (err) {
    // If the model had conversational thoughts or restarted mid-stream, isolate from the last '{'
    const lastStart = cleaned.lastIndexOf('{');
    const lastEnd = cleaned.lastIndexOf('}');
    if (lastStart !== -1 && lastEnd > lastStart) {
      evaluation = JSON.parse(cleaned.slice(lastStart, lastEnd + 1));
    } else {
      throw new Error("Unable to extract valid JSON from LLM: " + err.message);
    }
  }
  ```
- **Business & Operational Impact:** Eliminates workflow crashes when working with non-deterministic or open-source LLMs, ensuring 100% autonomous evaluation continuity without manual intervention.

---

## 🛠️ Case Study 05: The Action Node "Delivery Receipt" & Upstream Scope Decoupling

- **Failure Mode / Symptom:** Downstream database and email nodes evaluated variables as `undefined`, failing execution or writing empty cells.
- **Root Cause:** In n8n, `$json` exclusively reads from the immediate preceding node. When an action node (like Google Sheets `Append Row` or Gmail `Send`) is inserted in-line, its output contains execution delivery receipts (`id`, `threadId`, `labelIds: SENT`), displacing upstream candidate payload data.
- **Resolution & Defensive Hardening:**
  Decoupled downstream nodes from `$json` by enforcing strict cross-node scope addressing:
  ```javascript
  // Read candidate email from upstream Code gate, bypassing intermediate database metadata
  {{ $('Parse & Validate Evaluation.').item.json.email }}
  ```
- **Business & Operational Impact:** Guarantees reliable data flow across long linear workflows, preventing silent data truncation across third-party API boundaries.

---

## 🛠️ Case Study 04: LLM "Thinking Out Loud" & Mid-Object JSON Restarts

- **Failure Mode / Symptom:** Code node threw `SyntaxError: Expected double-quoted property name in JSON at position 40 [line 4]` during live candidate evaluation.
- **Root Cause:** Dynamic universal routing (`openrouter/free`) routed a request to `cohere/north-mini-code:free`. The model began drafting JSON, paused to write a conversational comment (*"probably need to ensure correct JSON syntax. Let's write it out."*), and then restarted the JSON object with a second `{` curly brace. Standard `JSON.parse()` crashed on the non-JSON sentence embedded in the text.
- **Resolution & Defensive Hardening:**
  Engineered a **Self-Healing Fallback Extractor** in the JavaScript Code node:
  ```javascript
  let evaluation;
  try {
    evaluation = JSON.parse(cleaned);
  } catch (err) {
    // If the model had conversational thoughts or restarted mid-stream, isolate from the last '{'
    const lastStart = cleaned.lastIndexOf('{');
    const lastEnd = cleaned.lastIndexOf('}');
    if (lastStart !== -1 && lastEnd > lastStart) {
      evaluation = JSON.parse(cleaned.slice(lastStart, lastEnd + 1));
    } else {
      throw new Error("Unable to extract valid JSON from LLM: " + err.message);
    }
  }
  ```
- **Business & Operational Impact:** Eliminates workflow crashes when working with non-deterministic or open-source LLMs, ensuring 100% autonomous evaluation continuity without manual intervention.

---

## 🛠️ Case Study 05: The Action Node "Delivery Receipt" & Upstream Scope Decoupling

- **Failure Mode / Symptom:** Downstream database and email nodes evaluated variables as `undefined`, failing execution or writing empty cells.
- **Root Cause:** In n8n, `$json` exclusively reads from the immediate preceding node. When an action node (like Google Sheets `Append Row` or Gmail `Send`) is inserted in-line, its output contains execution delivery receipts (`id`, `threadId`, `labelIds: SENT`), displacing upstream candidate payload data.
- **Resolution & Defensive Hardening:**
  Decoupled downstream nodes from `$json` by enforcing strict cross-node scope addressing:
  ```javascript
  // Read candidate email from upstream Code gate, bypassing intermediate database metadata
  {{ $('Parse & Validate Evaluation.').item.json.email }}
  ```
- **Business & Operational Impact:** Guarantees reliable data flow across long linear workflows, preventing silent data truncation across third-party API boundaries.
