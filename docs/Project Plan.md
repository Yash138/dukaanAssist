Objective (MVP in 2–3 weeks)

**ICP:** **Travel agencies** only (Phase 1).  
**Assumption:** Each agency uses a **WhatsApp Business phone number** as its customer-facing line; integration targets **WhatsApp Business Platform (Cloud API)** on that number.

Build a WhatsApp AI assistant for **travel agencies** that:

Responds automatically to traveler queries (from static FAQ + profile)
Captures trip leads (intent, contact, rough requirements)
Notifies the agency owner
Provides daily summary

👉 No over-engineering. No voice. No heavy ML.  
👉 Focus = “convert missed WhatsApp chats into qualified trip leads”

MVP Scope (Strictly Controlled)
Must Have (Ship this only)
WhatsApp chatbot (text only)
FAQ + package / trip responses (from configured content only)
Lead capture (name, intent, phone)
Escalation to owner
Daily summary (WhatsApp or email)
Explicitly NOT in MVP
Voice bot
Full inventory sync
Complex dashboards
Payment integrations
Tech Stack (Practical + Fast)
Backend
FastAPI (Python) or Node.js (Express)
You already have Python + infra → go with FastAPI
LLM Layer
OpenAI / cheaper alt (Groq, Together, etc.)
Use simple prompt + retrieval, not fine-tuning
Messaging
WhatsApp Business API via:
Twilio (fastest) OR
Meta Cloud API (cheaper but more setup)
Storage
Postgres (you already use it)
Tables:
business_config
conversations
leads
Hosting
Single VM (you already operate infra)
No Kubernetes, no scaling yet
High-Level Architecture
Customer → WhatsApp → Webhook → Backend
                                ↓
                          LLM + Prompt
                                ↓
                    Response + Intent Detection
                                ↓
        Save → DB (lead / chat / escalation)
                                ↓
            Reply → WhatsApp API → Customer
WEEK-BY-WEEK EXECUTION PLAN
WEEK 1 — Core Chat System (Foundation)
Goal

End of week → chatbot can reply to WhatsApp messages using business context.

Tasks
1. WhatsApp Integration (Day 1–2)
Setup Twilio / Meta API
Configure webhook endpoint
POST /webhook/whatsapp
Log incoming messages
2. Basic Backend Setup (Day 1–2)
FastAPI service
Postgres connection
Basic schema:
business_config (
  id,
  business_name,
  description,
  faq_json,
  language
)

conversations (
  id,
  user_phone,
  message,
  response,
  timestamp
)
3. LLM Response Layer (Day 3–4)

Prompt design (keep simple):

You are an assistant for a travel agency.
Agency details:
{business_description}

FAQs (packages, destinations, policies — only use these facts):
{faq}

Rules:
- Respond in same language as user (Hindi / Hinglish / English)
- Be concise and human; do not invent prices, visa rules, or availability
- If unsure → say you will connect to the agency owner
4. Response Pipeline (Day 4–5)

Flow:

Receive message
Fetch business config
Send to LLM
Return response
Save conversation
Output of Week 1
Fully working chatbot (basic)
Can answer FAQs
Language-aware
WEEK 2 — Lead Capture + Escalation (Make it Useful)
Goal

Convert chatbot → business tool

Tasks
1. Intent Detection (Day 1–2)

Add lightweight classification:

INTENTS = [
  "package_inquiry",
  "destination_info",
  "price_quote",
  "booking_intent",
  "visa_documents",
  "support",
  "unknown"
]

Use LLM or rule-based hybrid.

2. Lead Capture Logic (Day 2–3)

When intent = high value:

Ask:
Name
Requirement

Store:

leads (
  id,
  phone,
  name,
  intent,
  message,
  status
)
3. Escalation System (Day 3–4)

Trigger escalation when:

User asks complex question
LLM confidence low
Booking / quote intent detected

Action:

Send WhatsApp message to owner:
New Lead:
Name: X
Query: Y
Phone: Z
4. Smart Responses (Day 4–5)

Enhance replies:

If package / destination asked:
Suggest 2–3 options from FAQ only
If not in FAQ:
Offer callback / owner handoff
Output of Week 2
Leads getting captured
Owner receiving actionable info
Real business value starts
WEEK 3 — Retention Layer (Make it Sellable)
Goal

Make owner feel:
👉 “I need this product”

Tasks
1. Daily Summary सिस्टम (Day 1–2)

Send WhatsApp summary:

Today's Report:
- Total messages: 42
- Leads generated: 8
- Missed queries: 3
2. Missed Query Detection (Day 2–3)

If LLM fallback triggered:

Log as missed opportunity
3. Simple Admin Panel (Day 3–4)

Minimal UI (even basic):

Upload FAQ
Update business info

👉 Can be:

Streamlit app OR
Simple HTML form
4. Onboarding Flow (Day 4–5)

Critical for scale:

Ask agency owner:

Agency name, focus regions
Top packages / destinations
Typical price bands (for FAQ only — owner responsibility)
Contact / hours

Auto-generate:

FAQ draft
Prompt context
Output of Week 3
Sellable MVP
Owner sees daily value
Ready for pilot users
Final Deliverable (After 3 Weeks)
What You Will Have

✅ WhatsApp AI chatbot
✅ Lead capture system
✅ Escalation alerts
✅ Daily analytics
✅ Basic onboarding