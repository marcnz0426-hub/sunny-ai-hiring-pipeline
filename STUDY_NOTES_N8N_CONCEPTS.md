# 📚 n8n Core Concepts & Study Notes

> **Author:** Marc Neil Zapanta | **Role Prep:** AI Operator — Wunder Management  
> **Platform:** n8n Cloud | **Domain Analogy:** Customer Support Operations & Enterprise Automation  

---

## 🧭 1. Edit Fields (Set) Node

* **What it is:** The data sanitization and variable normalization engine in n8n.
* **Make.com Equivalent:** The *Set Variable* / *Multiple Variables* module.
* **CX / Helpdesk Analogy:**  
  > *When a customer submits an intake ticket, they might type their phone number in weird formats, enter erratic casing, or leave optional fields blank. The Edit Fields node acts as your front-desk receptionist who formats everything neatly into standardized boxes before handing the ticket off to downstream specialists.*
* **Why it matters in AI Pipelines:**  
  LLMs and downstream APIs are vulnerable to dirty data. If a webhook sends hundreds of useless HTTP headers and tracking cookies, feeding all of that into an LLM wastes tokens and increases latency. The Edit Fields node filters out the noise (when "Include Other Input Fields" is turned OFF) so only pure, typed candidate variables reach the AI evaluator.

---

## 🧭 2. HTTP Request Node

* **What it is:** The universal integration bridge in n8n used to call external APIs, send HTTP payloads, and receive JSON responses.
* **Make.com Equivalent:** The *HTTP (Make a request)* module.
* **CX / Helpdesk Analogy:**  
  > *This is like packaging up a candidate's file and sending it to your senior operations lead (Jev) via inter-office dispatch, with strict grading instructions and a scorecard to fill out.*
* **Production Hardening in n8n:**  
  Unlike basic webhooks, external AI APIs (like OpenRouter) can experience rate limits (HTTP 429) or transient provider outages. In n8n, you harden the HTTP Request node by enabling "Retry on Fail" with exponential backoff under Node Settings.

---

## 🧭 3. HTTP Methods (CRUD Operations)

* **POST:** *"Create / Submit New"* — Packages up new data (payload body) and creates a record. Used for application intake forms and sending chat completion requests to OpenRouter.
* **GET:** *"Look Up / Read Only"* — Retrieves data without altering server state. Used for reading rows from Google Sheets or checking applicant status.
* **PUT:** *"Replace Entire Record"* — Overwrites an existing profile completely.
* **PATCH:** *"Partial Update"* — Modifies only specific fields (e.g., updating just `status: "PASS"`) without touching anything else.
* **DELETE:** *"Remove / Erase"* — Permanently removes records.
* **HEAD:** *"Check Header Only"* — Checks if a URL or resource is alive without downloading content.

---

## 🧭 3. Data Structure: Make vs n8n

* **Make.com:** Wraps every transaction in circular bundle dots (`Bundle 1: { ... }`).
* **n8n:** Uses native JavaScript arrays of JSON items (`[{ json: { full_name: "...", email: "..." } }]`).  
  When referencing fields in n8n expressions, you use: `{{ $json.field_name }}`.

---

## 🧭 4. "Pinning Data" in n8n (Never Lose Test Data on Reload)

* **What it is:** The pushpin icon (📌) on any node output in n8n that locks that specific test payload into the workflow's persistent storage.
* **Make.com Equivalent:** Make keeps previous run history in "Incomplete Executions" or scenario logs, but n8n lets you freeze test data directly on the canvas.
* **Why it matters:**  
  When testing complex workflows with multiple nodes, you don't want to keep submitting fresh test webhooks every time you reload or tweak a downstream node. Pinning freezes a realistic candidate payload so you can build, test, and refresh freely.
* **How to use:** After running a node successfully, hover over its output and click the **Pushpin (Pin Data)** icon. The data will turn purple/pinned.

---

## 🧭 5. Rate Limits (HTTP 429) & "Retry on Fail"

* **What HTTP 429 means:** *"Too Many Requests"* — The external API's security guard telling your automation to slow down because shared capacity or rate limits are reached.
* **The Production Fix in n8n:**  
  Open the HTTP Request node &rarr; click the **Settings** tab &rarr; toggle ON **Retry on Fail** &rarr; set **Max Tries: 3** and **Wait Between Tries: 2000ms**. This implements automatic backoff retry.

---

## 🧭 6. The n8n Code Node (JavaScript Parser & Schema Gate)

* **What it is:** A lightweight JavaScript engine inside n8n used to transform, clean, and validate data between nodes.
* **Make.com Equivalent:** In Make, you used the *Parse JSON* module. In n8n, we use the **Code node** (JavaScript).
* **CX / Helpdesk Analogy:**  
  > *This is your data auditor. It unpacks the raw evaluation text, checks that it's valid JSON, strips out any accidental markdown formatting, and merges the candidate's original email address and name so downstream email nodes know who to send to.*
* **Why it's superior in n8n:**  
  Make requires a separate data structure schema definition. In n8n, a simple 5-line JavaScript snippet can:
  1. Strip markdown fences (` ```json `) with regex so LLM formatting quirks never crash the parser (Case Study 01).
  2. Parse the string into clickable JSON objects using `JSON.parse()`.
  3. Merge previous node data (like candidate's email and name from `$('Edit Fields')`) with the new AI scores so all downstream nodes have everything in one place.
* **Syntax pattern:**
  ```javascript
  const rawText = $input.item.json.choices[0].message.content;
  const cleaned = rawText.replace(/```(?:json)?/gi, '').replace(/```/g, '').trim();
  const evaluation = JSON.parse(cleaned);
  const candidate = $('Edit Fields').first().json;
  return { json: { ...candidate, ...evaluation } };
  ```

---

## 🧭 7. The Switch Node (Queue Dispatcher & Routing Engine)

* **What it is:** The conditional branching node in n8n that routes data down different paths based on rules you define.
* **Make.com Equivalent:** The *Router* module with conditional filters on each route.
* **CX / Helpdesk Analogy:**  
  > *This is your queue dispatcher. High-priority VIP tickets go directly to the fast-track resolution queue; borderline edge-cases go to the Team Lead review queue; and out-of-scope requests receive an automated graceful reply.*
* **Fallback Output ("Extra Output" vs "None"):**  
  * If set to *None (default)*: Any item that doesn't match Rule 1 or Rule 2 is silently dropped. In customer ops or recruiting, silent drops are catastrophic (a candidate gets ghosted).
  * If set to ***Extra Output***: n8n creates a dedicated third branch. Any candidate who scored `< 40` (neither PASS nor MAYBE) exits here, allowing you to connect an automated polite rejection email and audit log.

---

## 🧭 8. Canvas Navigation: Output Dot Dragging (Multi-Branch Routing)

* **What it is:** The visual mechanic in n8n used to connect distinct downstream nodes to specific output branches of a Switch, If, or Filter node.
* **Make.com Equivalent:** In Make, you add routes to a central circular Router dot, and then click the wrench icon to set filters.
* **How it works in n8n:**  
  Every rule created in a Switch node generates a distinct numbered connection dot on the right edge of the node. To wire up a specific branch (e.g. `PASS >= 70`), simply hover directly over the top output dot, click and drag the connection line across the canvas, and release to attach your action node (e.g., Gmail).

---

## 🧭 9. Google Sheets Node: Resource vs Operation Architecture

* **What it is:** The database persistence layer in n8n for tabular records.
* **Make.com Equivalent:** The *Google Sheets (Add a Row)* module.
* **CX / Helpdesk Analogy:**  
  > *This is your master candidate roster or CRM log. Every interaction, score, and decision is recorded chronologically so hiring leads and team auditors can review pipeline velocity at a glance.*
* **Understanding n8n's Resource Hierarchy:**  
  * `Resource: Document` &rarr; Used to create or delete an *entire spreadsheet workbook file*.
  * `Resource: Sheet Within Document` &rarr; Used to interact with *rows and tabs inside an existing spreadsheet*.
  * To append records, set **Resource: `Sheet Within Document`** &rarr; **Operation: `Append Row`**.

---

## 🧭 10. Data Scope in n8n: The "Delivery Receipt" Trap (`$json` vs `$('Node').item.json`)

* **The Problem:** When a node sits immediately after an action node (like Gmail, Slack, or Twilio), `{{ $json.field_name }}` returns `undefined`.
* **The Root Cause (The Delivery Receipt Trap):**  
  *CX Analogy:* When a courier (Gmail) delivers a package, they hand you a delivery receipt (`{ id: "1a1aad...", threadId: "...", labelIds: ["SENT"] }`). The courier's receipt doesn't include the candidate's full resume—it only contains shipping metadata.
* **The Two Production Fixes:**
  1. **Cross-Node Syntax:** Instead of `$json`, reference the source node directly:  
     `{{ $('Node Name').item.json.applicant_name }}`  
     *(Important: The string inside `$('...')` must match the exact name of the node on the canvas, including any punctuation like trailing periods).*
  2. **Canvas Parallel Branching:** Connect Google Sheets directly to the data-bearing node (`Parse & Validate Evaluation.` or `Switch`) so it receives the candidate dossier directly without passing through the Gmail receipt node.

---

## 🧭 11. CX Communication Architecture: Score Decoupling

* **The Rule:** Decouple internal algorithmic scores from external candidate communications.
* **Why it matters:**  
  *Internal:* Hiring managers and interviewers need granular numbers (`overall_score: 72.5`, `portfolio_quality: 7/10`) to prioritize review queues and benchmark candidate cohorts.  
  *Candidate-facing:* Telling an applicant their numerical score (e.g., "You scored 72.5") adds friction, triggers unnecessary debate over point rubrics, and feels dehumanizing. Good CX and ethical AI systems convey the positive outcome ("Your portfolio scored strongly in our screening") while keeping numeric calculations in internal dossiers.

---

## 🧭 10. OpenRouter Universal Fallback Routing (`openrouter/free` & `models` array)

* **The Problem:** Free-tier AI endpoints (like individual Gemma or LLaMA models) can experience sudden traffic spikes, maintenance, or deprecation. Hardcoding a single model string (`"google/gemini-2.0-flash-exp:free"`) makes your pipeline brittle.
* **The Universal Solution:**
  1. **Option A (Universal Dynamic Router):** Set `"model": "openrouter/free"`. OpenRouter automatically evaluates available free providers in real-time and routes your request to whichever free model is currently active, fast, and healthy.
  2. **Option B (Ordered Fallback Array):** Use `"models": ["model-a:free", "model-b:free"]`. OpenRouter attempts model A first; if it returns 429 or 5xx, it falls back to model B with zero downtime.
* **Strategic Portfolio Takeaway:** This demonstrates senior architectural thinking: decoupling your pipeline from any single brittle model endpoint and building resilient, provider-agnostic AI pipelines.

---

## 🧭 12. Deduplication Engine & The 6-Month Cooldown Window

* **The Problem:** Applicants submit duplicate entries—either by accident, by spamming forms, or re-applying immediately after a rejection. This bloats databases, wastes AI token costs, and confuses hiring managers.
* **CX / Support Ops Analogy:**
  > *In Zendesk or Freshdesk, if a customer sends 5 tickets in 10 minutes about the same issue, the system merges them into a single parent ticket. You don't have 5 different agents responding simultaneously.*
* **The Production n8n Architecture:**
  1. Place a **Google Sheets (Search Rows)** or **DB Lookup** node immediately after the Webhook.
  2. Search by `applicant_email`.
  3. If a match exists, calculate date difference: `(current_date - previous_evaluated_at) / (1000 * 60 * 60 * 24)`.
  4. If `< 180 days` (6 months), bypass the AI node entirely and route to a **Cooldown Notice** email:
     *"Thank you for your interest! Our policy allows candidates to re-apply after a 6-month cooldown. Your last application was received on [Date]."*
* **Why this is a Senior Architectural Move:**
  - **Zero Wasted AI Tokens:** The expensive LLM API call is never executed for ineligible duplicate applications.
  - **Database Integrity:** No duplicate rows are created in the active review ledger.

---

## 🧭 13. ATS Integration: n8n as "Intelligent Middleware"

* **The Question in Interviews:** *"What ATS system does your evaluator connect to, and how does it integrate?"*
* **The Production Answer:**
  > *"In an enterprise talent stack, n8n functions as the **Intelligent Cognitive Middleware Layer** between the candidate intake surface (Typeform, Tally, Careers Portal) and the Core ATS (such as Ashby, Greenhouse, Lever, or Workable)."*
* **Why not use the native ATS screening?**
  Legacy ATS platforms rely on dumb regex keyword filters (e.g. searching for "SQL" or "Python"). They reject qualified candidates who phrase their experience differently and pass unqualified candidates who keyword-stuff.
* **The Architecture:**
  1. Candidate submits on Careers Portal / Webhook.
  2. n8n runs Stage 1 Jev cognitive screening and generates candidate-tailored questions.
  3. n8n calls the ATS REST API (e.g., `POST https://api.ashbyhq.com/candidate.create` or `POST https://harvest.greenhouse.io/v1/candidates`) via an HTTP Request node.
  4. The candidate profile, resume, Stage 1 score, and custom questions are injected directly into the company's official ATS pipeline with zero human data entry.

---

## 🧭 14. Data Hygiene & Input Formatting (The "mArc   NeIl" Problem)

* **The Problem:** Applicants enter unstructured or chaotic text into form inputs (e.g., `marc neil`, `mArc   NeIl`, `+63 912-345 6789`).
* **Where Formatting Lives in n8n:**
  Unlike Make (which has built-in text functions like `capitalize()`, `trim()`), n8n gives you full JavaScript string manipulation inside either the **Edit Fields (Set)** node or the **Code** node.
* **The 1-Line JavaScript Transformation for Clean Title Casing:**
  ```javascript
  // 1. Trim outer spaces, collapse multi-spaces to single space
  // 2. Lowercase all characters
  // 3. Capitalize first letter of every word
  const cleanName = rawName.trim().replace(/\s+/g, ' ').toLowerCase().replace(/\b\w/g, c => c.toUpperCase());
  // "  mArc   NeIl  " ---> "Marc Neil"
  ```
* **Why it matters:**
  Clean data hygiene prevents embarrassing candidate-facing emails (e.g. *"Dear mArc   NeIl,"*) and ensures deduplication lookups match accurately regardless of casing.

---

## 🧭 15. Primary Key Architecture: Why Email & Candidate ID Trump Row Numbers

* **The Operational Flaw with "Row Numbers" in Pipelines:**
  In beginner builds, people try to reference spreadsheet coordinates like "Row #3". But in real business operations, spreadsheets get sorted chronologically, by score, or filtered by hiring managers. The moment a sheet is sorted, "Row #3" suddenly points to an entirely different person!
* **The Enterprise Solution (Natural Primary Keys):**
  Human names are not unique (multiple applicants share identical names). The universal unique identifier is the candidate's **verified email address** or a composite tag: `Candidate Name (email)`.
* **CX / Stakeholder Benefit:**
  Displaying `View Damian Wayne (damianwayne09288@gmail.com) in Sunny Database ↗` gives the hiring manager a direct, zero-ambiguity link that never breaks regardless of how the database is sorted or indexed.

---

## 🧭 16. Dead-Letter Queues (DLQ) & Out-of-Band Incident Alerting

* **What it is:** A specialized fault-tolerance architecture where failed executions are quarantined into a dedicated storage location (`Error Log` tab) and on-call engineers are paged via an independent communication channel.
* **CX / Helpdesk Analogy:**
  > *If a customer ticket gets dropped due to a server crash, you don't just lose the customer. The system automatically routes the broken ticket into an **"Unassigned Critical Escalation Queue"** and pages the on-call systems administrator with the exact ticket ID.*
* **Why it matters in Production:**
  In naive automation, when a third-party API (OpenRouter, Google Sheets) fails, the execution silently dies and the applicant is ghosted forever. An **Error Trigger** node catches unhandled exceptions, records the exact execution URL and error stack in a Dead-Letter Queue, and fires an out-of-band email alert so the team can replay the run with 1 click once the service recovers.

---

## 🎯 17. Mock Execution URLs vs. Real Production Execution IDs (Why Test Links 404)

* **The Observation:**
  When clicking the incident URL from the DLQ alert email generated during editor testing (`https://marcneil.app.n8n.cloud/execution/workflow/1/231`), n8n throws a `404 Error: Oops, couldn't find that`.
* **The Root Cause:**
  When you click **"Fetch Test Event"** on an `Error Trigger` node in the n8n canvas editor, n8n injects **synthetic placeholder mock data**:
  - `workflow.id: 1` ("Example Workflow")
  - `execution.id: 231`
  Because execution `#231` never actually executed on the instance database, the URL has no underlying record to load.
* **CX / Operations Analogy:**
  > *"It's like printing a sample shipping label with the tracking number 123456789. The label generator and barcode printer work perfectly, but if you look up 123456789 on FedEx.com, it will say 'Tracking Number Not Found' because no real package was shipped yet."*
* **Production vs. Test Behavior:**
  In a **live production failure**:
  1. The workflow throws an unhandled exception during a live webhook execution.
  2. n8n assigns a persistent, database-backed execution ID (e.g. `execution/7842`).
  3. The link in the admin alert navigates directly to that saved execution record, highlighting the exact node that failed in red and displaying the input/output state at the time of failure for 1-click retry.

---

## 📦 18. Portability & Self-Hosting: Cloud vs. Local n8n Migration

* **Workflow JSON Standardization:**
  The JSON export schema in n8n is universal and version-compatible across all hosting environments (n8n Cloud, Docker, npm, Kubernetes, or self-hosted VPS). A workflow exported from n8n Cloud can be imported into a local n8n instance in seconds via **"Import from File"** or simple canvas clipboard paste (`Ctrl + V`).
* **The "Zero-Secret" Security Boundary:**
  For security reasons, n8n workflow blueprints never export sensitive tokens, OAuth credentials, or private API keys. The JSON contains node structure, business logic, JavaScript transformations, and variable expressions, but leaves credential references as abstract IDs. Upon importing into a local instance, the operator simply re-authorizes credentials in the node drawers.
* **Self-Hosting (Free Forever):**
  Running n8n locally via Docker (`docker run -p 5678:5678 docker.n8n.io/n8nio/n8n`) or npm (`npx n8n`) provides unlimited workflows, unlimited executions, and zero subscription costs. For webhooks, tunneling tools like `n8n start --tunnel` or Cloudflare Tunnels provide public HTTPS URLs.
* **Portfolio Value:**
  Storing clean, sanitized `.json` blueprints in public GitHub repositories allows prospective employers or hiring managers to clone and spin up the exact candidate pipeline on their own infrastructure in under two minutes.

---

## 🌉 19. Asynchronous Webhook Decoupling & State Hydration

* **The Problem with Monolithic Pipelines:**
  A beginner instinct is to put an entire multi-day hiring process into a single workflow with a "Wait" node. But waiting days or weeks for an applicant to write technical answers keeps executions lingering in server memory, invites timeout failures on server restarts, and makes observability impossible.
* **The Asynchronous Decoupling Architecture:**
  1. **Workflow 1 (Intake & Screening):** Executes in < 5 seconds, logs the candidate, sends the custom questions email with a portal submission link, and gracefully closes.
  2. **The Portal / Form:** When the applicant finishes their answers, the portal fires an independent `HTTP POST` request across the internet to Workflow 2's webhook listener (`POST /qualifying-response`).
  3. **State Hydration (The Bridge):** Because Workflow 2 only receives the new answers and the candidate's email, it uses the candidate's email (the universal primary key) to query Google Sheets and "hydrate" the original Stage 1 context (initial score, resume, and portfolio link).
* **CX / Ticketing Analogy:**
  > *It's identical to Zendesk ticket handling: An automated email asks the customer for screenshots. The automation closes the active webhook immediately. When the customer replies 3 days later, a brand new trigger fires, looks up the existing ticket ID, hydrates the past conversation history, and routes it to tier-2 support.*

---

## 🔐 20. Secret Hygiene: Hardcoded Headers vs. Credential Vaults (The Enterprise Shift)

* **The Sprint Pattern vs. Enterprise Security:**
  - **In Fast Prototyping:** Manually typing `Authorization: Bearer sk-or-v1-...` into an HTTP Request node drawer allows rapid verification without configuration overhead.
  - **The Risk in Production:** Hardcoded secrets in node parameters get exported into workflow `.json` files. If committed to a public Git repository, API keys are leaked immediately.
* **The Production Solution (Header Auth Credential Vault):**
  Instead of hardcoding headers in the node parameters:
  1. Set **Authentication:** `Generic Credential Type` &rarr; `Header Auth`.
  2. Create a credential in n8n's encrypted vault named `OpenRouter API`.
  3. Enter Name: `Authorization`, Value: `Bearer <token>`.
  4. Now, the HTTP node references the encrypted credential abstractly. When the workflow JSON is downloaded or shared, the API key remains 100% hidden and secure.
* **Environment Variables (`$vars`):**
  Dynamic metadata headers like `HTTP-Referer` and `X-Title` can be abstracted into n8n **Environment Variables** (`$env.PORTFOLIO_URL` or `$vars.APP_TITLE`), allowing seamless staging vs. production deployment switches without altering node code.

---

## 🔁 21. Multi-Stage Pipeline Idempotency: Workflow 1 vs. Workflow 2 Deduplication

* **Workflow 1 Gate (Cooldown Policy):**
  - **Purpose:** Protects public perimeter webhooks from candidate spam, re-application spam, and redundant LLM API costs.
  - **Mechanism:** 180-day date calculation between `$now` and historical submission timestamp.
* **Workflow 2 Gate (Submission Idempotency):**
  - **The Risk:** What happens if an applicant double-clicks the "Submit" button on the portal or submits updated answers 10 minutes later?
  - **Production Behavior Without Idempotency:** The pipeline evaluates the answers twice, logs two duplicate rows to `Qualifying Responses`, and fires duplicate interview invitations to the applicant and hiring manager.
  - **The Enterprise Solution:** A Stage 2 Idempotency Gate checks the `Qualifying Responses` tab first. If `marc.zapanta@example.com` already exists with a status or score, the webhook rejects or ignores the duplicate submission, or updates the existing row in place rather than creating duplicate calendar invites.
