# 🎬 Loom Video Script: AI Hiring Pipeline with Jev Evaluator Agent

> **Target Duration:** 2 minutes | **Platform:** n8n Cloud + OpenRouter  
> **Audience:** Lauris Kalnins & Wunder Management Hiring Team  
> **Theme:** "Tools That Run & Save the Team Time"  

---

## ⏱️ Timeline Breakdown

### 0:00 - 0:15 | The Hook (15 seconds)
> *"Hi Lauris and the Wunder team, I'm Marc. To show you how I approach the AI Operator role, I didn't just write a resume — I built a production-grade, two-stage AI hiring pipeline in n8n that automates screening, evaluates technical depth with an AI evaluator agent named Jev, and handles the entire follow-up and interview dispatch flow."*

### 0:15 - 0:45 | The Architecture & Stage 1 Screening (30 seconds)
> *(Show n8n canvas on screen with Workflow 1)*  
> *"Here's Stage 1. When an applicant submits, our webhook catches the payload and runs a 180-day deduplication cooldown check against Google Sheets to eliminate duplicate spam and protect our AI budget. Eligible applicants are passed to Jev via OpenRouter.*  
> *Jev evaluates across five weighted dimensions: portfolio quality, experience, cover letter depth, availability, and location fit. Depending on the score, our 3-tier router either sends a polite rejection, flags a borderline candidate with an immutable composite primary key for hiring manager review, or—for candidates scoring 70 and above—dynamically generates 5 tailored qualifying questions based specifically on the applicant's claimed tools."*

### 0:45 - 1:15 | Stage 2 Asynchronous Decoupling & Multi-Stakeholder Dispatch (30 seconds)
> *(Show Workflow 2 canvas)*  
> *"Rather than blocking a monolithic workflow while waiting days for the applicant to reply, Stage 2 is completely decoupled. When the candidate submits their answers, Webhook 2 activates, verifies our Stage 2 Idempotency Gate to prevent double-submissions, and hydrates their past Stage 1 profile from Google Sheets.*  
> *Jev evaluates their technical depth. If they advance, it simultaneously coordinates the entire 3-way hiring loop: the candidate gets an automated interview booking link, the hiring manager gets an executive summary scorecard, and the technical interviewer receives a pre-briefing sheet with tailored drill-down probe questions."*

### 1:15 - 1:40 | Production Hardening & Error Handling (25 seconds)
> *(Zoom in on Error Trigger and Google Sheets Error Log tab)*  
> *"In production, AI models hallucinate and third-party APIs hit rate limits. That's why I built enterprise-grade defensive reliability: 3x exponential backoff retries on OpenRouter, a self-healing JavaScript parser that strips markdown fences, and a Dead-Letter Queue (DLQ) with an Error Trigger node that quarantines failed runs into Google Sheets and pages me via Gmail with a 1-click execution replay link."*

### 1:40 - 2:00 | The Close & CX Moat (20 seconds)
> *"I built this from 5+ years of customer operations experience where I lived in the middle of companies solving bottlenecks. I'm ready to bring this exact builder mindset to Wunder starting Day 1. Looking forward to walking you through this live in our interview!"*
