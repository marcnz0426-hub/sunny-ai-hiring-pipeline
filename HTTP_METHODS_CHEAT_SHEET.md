# 🌐 HTTP Methods & API Operations: Plain-English Reference Guide

> **Author:** Marc Neil Zapanta | **Project:** AI Hiring Intake & Screening  
> **Domain Analogy:** Customer Support Operations & CRM Data Management  

---

## 🧭 The Core Concept: Verbs of the Web

In web APIs, **HTTP Methods** (also called "verbs") tell the receiving system or server *what action* you want to take with customer/candidate data—just like working with support tickets or CRM records.

---

## 📋 Comprehensive Method Comparison Matrix

| Method | Plain English Action | CX & Helpdesk Analogy | Carries Data (Payload Body)? | Real-World Automation Use Case |
|---|---|---|:---:|---|
| **POST** | **Create / Submit New** | A customer fills out a contact form or submits a brand new ticket. It packages up their info and creates a new record on the server. | ✅ **Yes** (JSON body) | Candidate submits application form; Webhook receives intake payload. |
| **GET** | **Look Up / Read Only** | Looking up a customer's order history in Zendesk/Shopify without changing anything. | ❌ **No** (Query params in URL only) | Fetching existing candidate notes from Google Sheets or an ATS. |
| **PUT** | **Replace Entire Record** | Completely replacing an existing customer profile from scratch with a whole new file. If fields are omitted, they get cleared out! | ✅ **Yes** | Overwriting an entire applicant profile record in a database. |
| **PATCH** | **Partial Update** | Updating *only* the customer's phone number or changing ticket status from "Open" to "Pending" without touching any other fields. | ✅ **Yes** | Updating just the `screening_status: "PASSED"` field on an existing applicant. |
| **DELETE** | **Remove / Erase** | Permanently purging a duplicate spam ticket or fulfilling a GDPR data deletion request. | ❌ Rarely | Removing test candidate records or purging inactive profiles. |
| **HEAD** | **Check Header Only** | Asking "Is this customer active?" or "Does this file exist?" without downloading the entire profile or file contents. | ❌ **No** | Pre-flight check to verify if a candidate's resume URL is alive before sending it to an AI model. |

---

## 💡 Quick Rules of Thumb for Builders

1. **Submitting a Form?** &rarr; Always **POST**.
2. **Reading or Fetching Data?** &rarr; Always **GET**.
3. **Updating a Specific Field?** &rarr; Prefer **PATCH** over **PUT** (to avoid accidentally wiping unmentioned fields).
4. **Is it Idempotent?**
   * Calling **GET**, **PUT**, or **DELETE** 5 times in a row leaves the system in the exact same state.
   * Calling **POST** 5 times in a row will create 5 duplicate tickets! (That's why deduplication filters are crucial for webhooks).
