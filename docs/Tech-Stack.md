# DukaanAssist — Tech Stack (MVP)

**Version:** 1.4  
**ICP:** **Travel agencies** (Phase 1).  
**Constraints:** Text-only Phase 1, **minimal cost**, **fast to ship**, **official WhatsApp** (Cloud API).  
**Assumption:** Each agency uses a **WhatsApp Business phone number** connected to the platform (not a personal-only consumer line).

---

## 1. Summary table

| Layer | Choice | Rationale |
|-------|--------|-----------|
| Runtime / API | **Python 3.11+** + **FastAPI** | Async-friendly webhooks, quick iteration, good LLM ecosystem. |
| Database | **PostgreSQL** | Single DB for config, messages, leads; familiar ops; optional `pgvector` later. |
| LLM | **One primary API** (e.g. OpenAI-compatible or direct) | Use **JSON / structured output** for intent + `needs_human`; cheapest model for classification if split. |
| Customer messaging | **Meta Cloud API** (direct) + **adapter boundary** | Phase 1 = WhatsApp only; orchestrator and DB stay **channel-agnostic** so **Telegram**, **SMS**, or other text channels can add adapters later without rewriting the LLM core. |
| Hosting | **Single small VPS** (e.g. 1 vCPU, 1–2 GB RAM) or smallest always-on PaaS | Matches “one VM” mental model; scale after validation. |
| Admin / onboarding | **Streamlit** or **single-page HTML + FastAPI templates** | Fastest for FAQ edit + business profile; no heavy frontend early. |
| Reverse proxy / TLS | **Caddy** or **nginx** + Let’s Encrypt | Free TLS for webhook URL. |
| Process manager | **systemd** or **Docker Compose** (single host) | Simple restart policies. |

**Alternative you already considered:** Twilio for WhatsApp — **faster** first webhook sometimes, **higher** per-message cost. For **least cost** at volume, prefer **Meta Cloud API** once onboarding time is acceptable.

---

## 2. WhatsApp integration

- **Onboarding:** Each **travel agency** supplies a **WhatsApp Business** number (or migrates an existing line) and completes **WABA** + **Cloud API** setup.
- **Product:** WhatsApp Business Platform — **Cloud API**.
- **Webhook:** HTTPS `POST` from Meta; verify token + app secret signature.
- **Outbound:** HTTPS calls to Graph API `v21.0` (or current) `/PHONE_NUMBER_ID/messages`.
- **Templates:** Required for **marketing** and some **utility** outside 24h window; design 2–3 owner-alert templates early.
- **Dev:** Meta test numbers / sandbox flows before production number migration.

**Isolation:** Inbound webhooks and outbound sends for WhatsApp live in a **thin adapter**; the app consumes **normalized** events (tenant, `channel`, thread/sender ids, text) and replies through an **outbound router**. Do not embed Graph API payload shapes in orchestrator or LLM code. Future channels (e.g. **Telegram Bot API**, **SMS** via Twilio or similar) are additional adapters + config keys per tenant. See `Architecture.md` §3.

---

## 3. LLM usage pattern (cost-aware)

1. **Single call** with structured fields: `reply_text`, `detected_language`, `intent`, `confidence`, `needs_human`, optional `lead` object — **or**
2. **Tiny classifier** (cheap model) + **main call** only for reply generation.

**Rules in prompt:** “Answer only from FAQ and profile; if missing, set `needs_human` true and reply with safe acknowledgment.”

**Languages:** Hindi, Hinglish, English — specify in system prompt; mirror user’s script and mix level.

---

## 4. Infrastructure (minimal cost)

| Component | MVP approach |
|-----------|----------------|
| App + worker | Same machine as API process or second process on same VM. |
| DB | Postgres on same VM (acceptable for first pilots with backups) **or** managed Postgres free/low tier (e.g. small Neon/Supabase/RDS) if you want separation. |
| Backups | Automated `pg_dump` daily to object storage or local rotate. |
| Monitoring | Uptime ping + structured logs; error alerts via email/Telegram webhook (optional). |
| Secrets | `.env` on server or host env vars; never commit. |

**When to upgrade:** Sustained traffic, strict SLA, or team growth → managed DB, separate worker queue (Redis + RQ/Celery), horizontal API replicas.

---

## 5. Repositories / layout (no code here — planning only)

- One repo: `api` (FastAPI), `admin` (Streamlit optional), `migrations` (Alembic or SQL files), `deploy` (systemd unit or compose).

---

## 6. Third-party cost categories (no amounts)

Interns and contributors should **not** assume budget numbers from this repo. Leadership tracks actual spend separately.

- **Meta (WhatsApp):** Conversation-based fees — see current Meta documentation for the agency’s country.  
- **LLM provider:** Token-based usage.  
- **Infrastructure:** Hosting, domain, TLS — depends on chosen provider.

Use environment-specific config and secrets; never commit API keys or commercial terms.

---

## 7. Compliance checklist (minimal path)

1. Meta **Business Portfolio** + **WhatsApp Business Account**.
2. **Display name** approval, **phone** verification.
3. **Privacy policy** URL (can be simple one-pager before wide launch).
4. **Data:** Document what you store (messages, leads); retention policy.

---

## 8. Explicitly not in Phase 1

- Kubernetes, multi-region, custom model training.
- Voice stack (STT/TTS, telephony).
- Real-time inventory / payment gateways.

---

## 9. Related documents

- `Architecture.md` — flows and components.
- `Executive-Project-Plan.md` — roadmap and product context.  
- `Pricing.md` — where commercial pricing is documented (**outside sensitive details in this repo**).
- `Project Plan.md` — week-by-week execution.
