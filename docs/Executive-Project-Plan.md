# DukaanAssist — Executive Project Plan

**Version:** 1.4  
**Audience:** Team, stakeholders, pilot partners  
**ICP (Phase 1):** **Travel agencies** only — WhatsApp Business, text-only.  
**Assumption:** Each pilot uses a **WhatsApp Business phone number** as its public customer contact.

Commercial pricing and confidential business terms are **not** in this file; see `Pricing.md` (outline only).

---

## 1. Vision (one line)

Help **travel agencies** never miss a customer on **WhatsApp** by answering in **Hindi, Hinglish, or English** with a **human tone**, capturing trip leads, and **looping the owner in** when needed — without sounding like a generic bot.

---

## 2. Target customers (Phase 1)

**Who:** Small and mid-sized **travel agencies** (often tier-2 / regional markets) that already advertise “WhatsApp us” for packages, dates, visas, and quotes.

**Typical customer queries:**

- Destinations, itineraries, group vs individual trips  
- Dates, seasonality, availability (as stated in static FAQ only)  
- Inclusions/exclusions, rough price bands (from FAQ only)  
- Documents (visa checklist style — factual from agency-supplied content only)  
- “Talk to someone” / custom quotes → escalation

**Why WhatsApp:** High-intent, conversational threads; same channel agencies already use; code-mixed language is common.

---

## 3. Phase 1 scope (locked)

| In scope | Out of scope (later) |
|----------|----------------------|
| Text on **WhatsApp** only | Voice / phone bot |
| **Static FAQ** + agency profile as truth | Live GDS / dynamic inventory |
| Same-language replies: **Hindi, Hinglish, English** | Full analytics dashboards |
| Lead capture + **escalation** to owner | In-chat payments |
| **Daily / periodic summary** for owner | Heavy fine-tuning / custom ML |

**Tone requirement:** Answers read like a **helpful agency staff member**, not a template robot (prompt design + short paragraphs + locale-appropriate phrasing).

**Engineering (not a Phase 1 product commitment):** The backend treats **WhatsApp as one messaging adapter**. The conversation core (FAQ, LLM, leads, escalation) is **channel-agnostic** so **Telegram**, **SMS**, or other text ingress can be added later without a rewrite. Phase 1 still **ships WhatsApp only** for customers. See `Architecture.md` §3.

---

## 4. Owner workflow (product intent)

Customers message the agency’s **WhatsApp Business number**. The product should:

1. **Auto-respond** using agency-configured FAQs and rules.  
2. **Notify the owner** when human judgment is needed (complex itinerary, low confidence, explicit “talk to owner”, high-value lead).  
3. **Takeover path:** Owner **continues as the business** when available (see `Architecture.md`: **web operator inbox** + Cloud API is the reliable MVP path).  
4. **Fallback:** If the owner does not respond within a defined window, the **bot continues** with safe replies (acknowledge, capture trip intent and contact, promise callback) — **no facts beyond FAQ**.

Success metric for pilots: fewer ignored threads and **measurable qualified leads** with owner satisfaction.

---

## 5. Practicality (given current AI)

| Area | Assessment |
|------|------------|
| FAQ + short replies in Hindi / Hinglish / English | **Strong fit** with clear system prompts and strict “only use provided facts.” |
| Lead extraction (name, intent, phone, rough trip idea) | **Strong fit** with structured output / simple schemas. |
| Accurate prices, visa rules, availability | **Requires** static FAQ + **escalation** when unsure — never invent policy or pricing. |
| Human-like tone | **Achievable** with travel-specific prompt snippets and owner-approved examples. |
| Scale / cost | Controllable for MVP with **one VM**, **single DB**, **rate limits**, and sensible model choice per task. |

**Risk:** Wrong or outdated trip/visa/price information **damages trust**. Mitigation: confidence thresholds, sensitive topics → default to owner.

---

## 6. Compliance posture (minimal, affordable)

- **WhatsApp Business Platform (Cloud API)** for production messaging; follow Meta’s business and phone-number onboarding.  
- **Privacy:** Store only what you need; define retention for chats and leads; align with agency privacy commitments as you scale.

Details: `Tech-Stack.md`, `Architecture.md`.

---

## 7. Roadmap (executive phases)

| Phase | Duration (indicative) | Goal |
|-------|------------------------|------|
| **0 — Discovery** | 1–2 weeks | Design partners; **travel-agency** FAQ templates; success metrics. |
| **1 — MVP** | 2–3 weeks | Webhook → LLM → DB → reply; leads; owner notifications; daily summary; minimal admin. |
| **2 — Pilot harden** | 4–6 weeks | Reliability, retries, language quality, billing hooks (if productized), abuse handling. |
| **3 — Scale features** | As needed | Richer FAQ import (e.g. packages CSV), simple fields for dates/pax, staff seats; **optional second customer channel** (e.g. Telegram or SMS) on the same orchestrator when ICP demand appears. |
| **4 — Voice (optional)** | After text PMF | Narrow callback flows — only if WhatsApp text proves value. |

---

## 8. Success metrics (Phase 1)

- **Activation:** Agency completes onboarding (FAQ + profile).  
- **Engagement:** Share of inbound threads that get a **same-day** automated reply.  
- **Business:** Leads per week; escalation rate in a healthy band (handoff works; bot still adds value).  
- **Quality:** Factual spot checks; “sounded human?” sampling.

---

## 9. Dependencies / decisions

- **WhatsApp:** **Business number** per agency, linked to **Cloud API** / WABA (Phase 1 customer channel).  
- **Messaging architecture:** **Adapter + normalized events** so additional channels are incremental work, not a fork (`Architecture.md` §3).  
- **Owner takeover:** **Web operator inbox** + escalation notifications; refine after pilots.  
- **Source of truth:** Static FAQ and policies **supplied and maintained by the agency** (or onboarding assist).

---

## 10. Document map

| Document | Purpose |
|----------|---------|
| `Project Plan.md` | Week-by-week engineering sketch |
| `Executive-Project-Plan.md` (this file) | Strategy, ICP, phases, practicality |
| `Architecture.md` | System design, flows, components |
| `Tech-Stack.md` | Stack choices for MVP |
| `Pricing.md` | Where commercial pricing lives (**no sensitive numbers in repo**) |
