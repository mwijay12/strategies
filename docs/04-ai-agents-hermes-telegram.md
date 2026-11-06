# 04 — AI Agents, Hermes na Telegram: "mfanyakazi asiye na mshahara"

## 1. Tofautisha vitu vitatu (watu wanachanganya hii sana)

| Kitu | Ni nini | Mfano |
|---|---|---|
| **Workflow** | Mtiririko uliopangwa: A → B → C. Unajua hatua zote. | Lead inaingia → inaandikwa → SMS inatumwa |
| **AI Agent** | LLM inayochagua hatua na tools. Hujui itafanya nini hatua kwa hatua. | "Tafuta lead hii, angalia historia yake, andika follow-up inayofaa, niulize kabla ya kutuma" |
| **Model** | Ubongo (Hermes 4, GPT, Qwen, Claude) | Haifanyi kazi peke yake; inahitaji runtime/tools |

**Kanuni ya ujenzi:** 80% workflow + 20% agent. Workflow kwa sehemu zinazojirudia (parse, save, send). Agent kwa sehemu inayohitaji ** hukumu** (kuandika, kuainisha, kuamua).

---

## 2. AI Agent node kwenye n8n (jinsi inavyofanya kazi kweli)

```
Trigger (Telegram / Webhook / Schedule)
      │
      ▼
┌─────────────────── AI Agent (Tools Agent) ───────────────────┐
│  System prompt  ← wewe unaandika (ndio bidhaa yako)          │
│  Model          ← OpenAI / Gemini / Hermes via OpenRouter     │
│  Memory         ← Postgres Chat Memory (window 10)            │
│  Tools:                                                        │
│    • Postgres / Google Sheets   (data ya biashara)            │
│    • HTTP Request               (API yoyote: M-Pesa, stock)   │
│    • Vector Store (pgvector)    (PDF, price list, manuals)    │
│    • Calculator / Code          (hesabu, format)              │
│    • Sub-workflow               (kazi maalum: "create_lead")  │
│    • HTTP → Telegram            (kutuma majibu)               │
└───────────────────────────────────────────────────────────────┘
      │
      ▼
Output → validation → approval (kama ni kwa mteja) → send
```

**Muhimu:** Agent haina "akili ya biashara" yako. Ina **tools + prompt + data**. Ndio maana mtu asiyeelewa biashara hawezi ku-copy workflow yako.

---

## 3. Hermes: maelezo mawili, maamuzi mawili

Watu wanaposema "Hermes" kwenye AI agents, wanamaanisha moja ya hivi:

| Hermes | Ni nini | Inafaa wapi |
|---|---|---|
| **Hermes 4 / 4.3 (Nous Research)** | Family ya open-weight models (14B, 36B, 70B, 405B). Zimefundishwa kwa `<tool_call>` tokens halisi + JSON schema mode — nzuri sana kwa agents | Kwenye GPU yako au API (Nous Portal, OpenRouter) |
| **Hermes Agent** (`NousResearch/hermes-agent`) | Runtime ya Python (MIT) yenye terminal, memory ya kudumu, tools 40+, na **gateways za Telegram/Discord/Slack/WhatsApp/Signal** | Kwenye laptop yako au VPS ya 8GB+, si kwenye n8n ya 4GB |

### Uamuzi kwa mazingira yako (4GB AWS)

| Nia | Chaguo sahihi | Kwa nini |
|---|---|---|
| Agent kwa wateja, ndani ya n8n | **Hermes 4 kupitia API** (OpenAI-compatible node) | Hakuna RAM ya modeli; unalipa kwa token |
| Kujaribu Hermes offline/private | **Laptop yako** + Ollama, modeli 8B–14B Q4 | 4GB VPS haiwezi; 14B Q4 ≈ 9GB |
| Agent ya "kila kitu" kwenye simu yako (Telegram) | **Hermes Agent** kwenye laptop au $20 VPS | Ina gateway + memory tayari; usiunde upya |
| Production ya mteja | n8n + agent node + API model | Inaonekana, inarekodiwa, ina-error handling |

**Kwa nini usiweke local LLM kwenye 4GB:** modeli 8B Q4 inahitaji ~5.2GB RAM. Ukiiweka, n8n yako inakufa kila siku. Ukihitaji privacy ya data, panda 16GB + GPU node tofauti — uza hiyo kama "on-premise AI" kwa TZS 3M+.

Modeli za API zinazolingana kwa bei (tumia kwenye `HTTP Request` au model node):

| Model | Matumizi | Gharama (takriban) |
|---|---|---|
| Hermes 4 70B (via OpenRouter) | Agents, tool calling | $0.4–1.2 / 1M tokens |
| Gemini 2.5 Flash | RAG, extraction, content (rahisi) | $0.1–0.4 / 1M |
| GPT-4.1-mini / 5-mini | General | $0.4–1.6 / 1M |
| DeepSeek / Qwen | Kazi za lugha nyingi, bei ndogo | $0.1–0.5 / 1M |
| Whisper / ASR ndogo | Transcription | ~$0.006/dakika |

Kwa workflow ya kawaida (lead qualification), gharama kwa mteja ni **TZS 15–150 kwa mwezi**. Hii ndio margin ya 90%: unauza TZS 400,000/mo, unatumia TZS 5,000.

---

## 4. Telegram: remote control ya biashara yako (na ya mteja)

Telegram ni bora kuliko WhatsApp kwa **internal ops** (bure, bot API rahisi, buttons, files, groups). WhatsApp itumike kwa **wateja wa mwisho** (wako huko).

### Setup ya dakika 10
1. Telegram → `@BotFather` → `/newbot` → pata `BOT_TOKEN`.
2. n8n → Credentials → Telegram → weka token.
3. n8n → workflow mpya → **Telegram Trigger** (updates: message, callback_query).
4. Chuja kwa `chat_id` yako (usiruhusu mtu yeyote).
5. Jibu kwa **Telegram → Send Message** + Inline Keyboard.

### Bot yako ya "Altraware Ops" (commands za kuanza nazo)

| Command | Kinachotokea |
|---|---|
| `/leads` | Leads 10 za mwisho + score; buttons: `Piga simu` / `Snooze` / `Quote` |
| `/report` | Daily report ya mteja (mauzo, unmatched payments, stock) |
| `/content <wazo>` | Inazalisha posts 5 → buttons `Approve` / `Edit` / `Reject` → inapublish |
| `/quote <mteja> <huduma>` | Inachukua template → inajaza → PDF → inakurudishia |
| `/status` | Uptime ya workflows, errors 24h, executions leo |
| `/price <bidhaa>` | Inatafuta kwenye price list (RAG) na kujibu |
| `/agent <swali>` | Agent yenye tools (DB, web, calculator) |

**Callback query button pattern** (muhimu kwa human-in-the-loop):
```
Send Message (inline keyboard: [{text:"Approve", callback_data:"ok:123"},{text:"Reject",callback_data:"no:123"}])
→ Telegram Trigger (callback_query) → Switch kwa callback_data → approve: publish; reject: log + hall
```

### Usalama wa bot
- Allowlist ya `chat_id` (hata moja ya kigeni = leak ya data ya mteja).
- Usiweke token kwenye prompt au Code node — tumia n8n credential.
- Rate limit: `Wait` au counter kwenye Postgres (mtu asitumie bot kupiga API mara 1,000).
- Log kila command + matokeo (nani, lini, nini).

---

## 5. Agents 4 unazojenga kwa Altraware (na kuuza)

### A5.1 · Sales/Outreach Agent (🟡)
**Input:** jina la kampuni + website + niche.
**Tools:** HTTP (fetch site), web search, Postgres (CRM), calculator.
**Output:** teardown ya ukurasa 1 (matatizo 3 + automation 2 + bei + hoja ya muda) + ujumbe wa WhatsApp wa mistari 4.
**Uza kwa:** wewe mwenyewe (kupata clients) → baadaye kama "BD Agent" kwa agency nyingine ($300–800/mo).

### A5.2 · Support/Order Agent (RAG) (🟡)
**Data:** price list, FAQ, delivery zones, return policy, stock (live).
**Guardrails:** haitoi discount, haitoi bei ya mwisho kwenye negotiation, inahamisha kwa mtu kwa hasira/legal/complaint.
**Uza kwa:** TZS 1.2M–2.5M build + 350k–600k/mo.

### A5.3 · Ops Analyst Agent (🟡)
**Input:** "Kwa nini mauzo ya jana yalipungua?"
**Tools:** Postgres read-only views, calculator.
**Output:** jibu + namba 3 + chart PNG (QuickChart) → Telegram.
**Uza kwa:** mmiliki wa biashara yenye branches 2+. TZS 900k build, 300k/mo.

### A5.4 · Content Agent (🟡) — tazama doc 05
**Input:** video/voice/note moja.
**Output:** assets 12 kwa platforms, kwa Kiswahili na English.

---

## 6. Memory: jinsi ya kuifanya isisahau na isikumbuke kupita kiasi

| Aina | Mahali | Muda |
|---|---|---|
| Short-term (mazungumzo ya sasa) | **Postgres Chat Memory** (window 8–12) | dakika/saa |
| Long-term facts ("mteja anapenda delivery jioni") | Table `customer_memory` + pgvector | miezi |
| Knowledge base (bei, sera, manuals) | pgvector (chunks 400–800 tokens) | hadi kubadilishwa |
| Logs (kila ujumbe) | Postgres `messages` | siku 90 |

**Usiweke** history yote kwenye prompt — inagharimu pesa na kuharibu ubora. Window + summary ndio njia.

---

## 7. Guardrails: mambo 8 ya lazima kabla agent iandike kwa mteja

1. **Approval gate** kwa kila ujumbe unaotoka nje (Telegram buttons) — miezi 3 ya kwanza.
2. **Output validator** (Code node): hakuna namba za simu za uongo, hakuna bei isiyo kwenye price list, urefu wa ujumbe.
3. **Confidence threshold** kwenye RAG (cosine < 0.75 → "nitakuunganisha na mtu").
4. **Escalation rules** kwa maneno: "kulipa", "sheria", "polisi", "malalamiko", "refund" → mtu.
5. **Cost cap:** max tool calls 6 kwa ujumbe; max tokens 2,000; kill switch.
6. **PII masking** kwenye logs (simu: `+2557*****123`).
7. **Golden set:** maswali 30 + majibu sahihi; kila unapobadilisha prompt, run test (workflow ya evaluation) — usibadilishe kwa hisia.
8. **Kill switch:** workflow moja ya "PAUSE ALL" inayozima agents zote (Telegram command `/pause`).

---

## 8. Gharama halisi ya kuendesha agents kwenye 4GB

| Kipengele | Kiasi | Gharama/mo |
|---|---|---|
| AWS t4g.medium (au Lightsail 4GB) | 1 | $12–18 |
| LLM API (wateja 5, msg 2,000/mo) | ~4M tokens | $8–25 |
| Embeddings (RAG, 2,000 chunks) | — | $0.5–2 |
| Telegram / UptimeRobot / Cloudflare | — | $0 |
| S3 backups (5GB) | — | $0.2 |
| **Jumla** | | **$21–45 ≈ TZS 55k–120k/mo** |

Ukiuza retainer moja ya TZS 350,000, infrastructure imelipwa mara 3. **Ndio maana hii biashara inafanya kazi.**

---

## 9. Ramani ya ujenzi (wiki 2)

| Siku | Kazi |
|---|---|
| 1 | Bot ya Ops (Telegram) + `/status` + allowlist |
| 2 | Error workflow → Telegram + `/leads` kutoka Postgres |
| 3–4 | Agent ya Support (RAG kwa price list ya kubuni) + validator + approval buttons |
| 5 | Golden set ya maswali 30 + workflow ya evaluation |
| 6–7 | `/content` (doc 05) + `/report` (W3) |
| 8–10 | Package: video demo 3 dakika, ukurasa wa "AI Agents" kwenye tovuti, bei |
| 11–14 | Onyesha kwa clients 10 waliopo; toa pilot 2 |

> Ukimaliza wiki hizi 2, huna "project ya n8n" — una **bidhaa** yenye demo hai, bei, na proof.
