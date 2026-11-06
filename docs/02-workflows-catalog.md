# 02 — Catalog ya Workflows (13 za kuuza, zilizopangwa kwa kipaumbele)

Kila workflow hapa ina: **maumivu → trigger → mtiririko wa nodes → RAM profile → bei → jinsi ya kuuza**.
Kwenye 4GB weza ku-run **workflows 15–25** kwa urahisi, lakini anza na **2**.

Alama za RAM: 🟢 nyepesi (<40MB/run) 🟡 kati (40–120MB) 🔴 nzito (>120MB, hitaji queue mode au nje ya peak hours)

---

## TIER 1 — Uza hizi kwanza (zinauma, zinalipa, ni rahisi kuonyesha)

### W1 · Lead Intake + AI Qualification + Follow-up (🟡)
**Maumivu:** biashara inapata WhatsApp/DM/form 40 kwa siku; mmoja anajibu kwa mkono, wengine wanapotea. Lead ya TZS 500,000 inapotea kwa sababu mtu hakujibu Jumanne.

**Trigger:** Webhook (tovuti yao) + Telegram/WhatsApp trigger + Google Form/Sheet.

**Mtiririko:**
```
Webhook/Form → Set (normalize: jina, simu, huduma, bajeti, chanzo)
→ Code (validate phone +255..., dedupe kwa number)
→ AI Agent (qualify: HOT / WARM / COLD + sababu + summary ya mistari 2)
→ Postgres/Sheet (save lead + score + timestamp)
→ IF HOT → Telegram alert kwa owner (na buttons: "Nimepiga simu" / "Snooze 3d" / "Tuma quote")
→ Wait 24h (kama hakuna action) → reminder ya pili
→ Baada ya siku 7 → email/SMS follow-up ya mwisho
```

**Deliverable:** dashboard ndogo (Sheet au Retool/Next page) ya leads + SLA ya dakika 15.
**Bei:** build TZS 900,000 – 1,500,000; retainer 250,000/mo.
**Proof ya kuonyesha:** "Kati ya leads 100, 22 zilikuwa HOT na zote ziligusiwa ndani ya dakika 15; awali 6 tu."

---

### W2 · M-Pesa / Tigo Pesa Reconciliation → Books (🟡)
**Maumivu:** duka linapokea malipo 80–200 kwa siku; mtu mmoja anapitia SMS/statement kila jioni. Makosa ya TZS 50,000–300,000 kwa wiki.

**Trigger:** Schedule (kila saa) + webhook ya Daraja (kama mteja ana API access) + email/CSV ya statement.

**Mtiririko:**
```
Schedule → HTTP (Daraja transaction status / au Google Drive: statement mpya)
→ Code (parse SMS/CSV → {receipt, amount, msisdn, ref, time})
→ Postgres (match na invoice/order kwa ref au amount+date window)
→ IF unmatched → Telegram "Malipo haya hayana invoice: ..." (owner afanye maamuzi)
→ IF matched → update order status → SMS/WhatsApp risiti kwa mteja
→ Daily 21:00 → summary: collected / unmatched / pending
```

**Bei:** build TZS 1,800,000 – 3,500,000; retainer 400,000 – 700,000/mo (hii ni "mission critical" — wanalipa).
**Note:** usianze na API ya benki; anza na CSV/email ya statement (rahisi, hakuna legal friction).

---

### W3 · Owner's Daily Report kwa simu (🟢) — **bora kwa demo ya dakika 3**
**Maumivu:** mmiliki hajui kilichotokea leo mpaka kesho; anauliza meneja kwa simu mara 5.

**Trigger:** Schedule 21:30 (EAT) kila siku.

**Mtiririko:**
```
Schedule → Postgres/Sheets query (mauzo, cash vs M-Pesa, top 5 items, stock chini, staff shift)
→ Code (format kwa Kiswahili, alama za +/− vs jana)
→ AI (insight 3 za mistari: "Pembe zimeuzwa mara 2 zaidi; soda zinaisha Ijumaa")
→ Telegram/WhatsApp kwa owner + PDF/CSV weekly
→ Kama mauzo < 60% ya wastani → alert nyekundu
```

**Bei:** build TZS 700,000; retainer 200,000/mo. **Ongeza kwenye kila mfumo unaouza** (Faida POS, ShuleDirect, CareTrack) — ni upsell ya asilimia 100.

---

### W4 · WhatsApp / Telegram AI Support Agent (RAG kwenye price list & FAQ) (🟡)
**Maumivu:** maswali 60% yanarudiwa ("mnauza bei gani?", "iko wapi?", "mnatumia M-Pesa?"). Mtu mmoja anajibu masaa 4/siku.

**Mtiririko:**
```
WhatsApp Cloud API / Telegram Trigger
→ Filter (namba zilizoruhusiwa / customers pekee) + rate limit (5 msg/dakika)
→ AI Agent (tools: cari_price_list, cari_stock, create_lead, handover_to_human)
   ↑ Vector Store (pgvector) ya: price list, FAQ, delivery zones, return policy
→ IF confidence < 0.75 AU mteja ana hasira → handover kwa mtu (Telegram alert)
→ Log conversation (Postgres) + thumbs up/down
```
**Bei:** build TZS 1,200,000 – 2,500,000; retainer 350,000 – 600,000/mo.
**Kanuni:** bot **haituji bei ya mwisho** kwenye biashara ya negotiating (Kariakoo) — inatoa range na inampeleka mtu.

---

## TIER 2 — Zina margin kubwa, zinahitaji uelewa wa niche

### W5 · Tender / RFP Monitoring Agent (🟢🟡)
**Niche:** ujenzi, supply, IT vendors, NGOs, consultancies.
**Maumivu:** tender ya TZS 300M inapotea kwa sababu hakuna aliyeona tangazo.
**Mtiririko:** Schedule (kila asubuhi 07:00) → HTTP crawl (PPRA/tender portals, RSS, Jina AI reader kwa kurasa ngumu) → AI (classify: sector, budget, deadline, eligibility) → filter kwa profile ya mteja → Telegram + Sheet + calendar reminder (deadline −5 siku).
**Bei:** build TZS 2,000,000 – 3,500,000; retainer 400,000 – 800,000/mo. **Kwa kampuni kubwa uza kwa USD 400–800/mo.**
**Faida:** hakuna integration ngumu, inaonekana kama "radar ya pesa".

### W6 · Debtors & Invoice Chaser (🟡)
**Niche:** wholesalers, distributors, printing, logistics.
**Mtiririko:** Schedule → Sheet/DB (aging: 30/60/90) → AI (andika ujumbe kwa Kiswahili kwa tone tofauti kwa kila mteja) → approval kwenye Telegram → SMS/WhatsApp → escalate kwa owner siku 7 → weekly statement PDF.
**Bei:** build TZS 1,200,000; retainer 300,000/mo. **Weka kwenye proposal: "tulikusanya TZS X kwa wiki 4 za kwanza"** — ndio metric inayofanya retainer isife.

### W7 · Document Extraction (invoice, LPO, receipt, CV, fee statement) (🔴 kwa batch kubwa)
**Mtiririko:** Drive/Gmail/WhatsApp → PDF → AI vision (structured output: JSON schema) → validate → Postgres/ERP → IF uncertain → review queue kwa mtu.
**Bei:** build TZS 1,500,000 – 3,000,000; retainer 350,000/mo.
**RAM:** batch ya PDF 50+ inaweza kula 200MB+; tumia sub-workflow ya items 10 kwa batch + `N8N_DEFAULT_BINARY_DATA_MODE=filesystem`.

### W8 · Recruitment / CV Screening (kwa NGOs & hotels) (🟡)
Applicants 300 → parse → score vs job description → shortlist 20 + interview questions → email/WhatsApp → report ya HR.
**Bei:** build TZS 1,200,000; retainer 250,000/mo (seasonal, uza kwa mwaka: 2.5M/yr).

### W9 · Content Repurposing Engine (🟡) — **tazama doc 05**
Video/voice moja → blog, LinkedIn, X thread, script za TikTok, newsletter, WhatsApp broadcast, carousel.
**Bei kwa mteja:** 400,000 – 900,000/mo. **Kwa wewe mwenyewe:** ndio injini ya kupata clients.

---

## TIER 3 — Jenga ukiwa na wateja 5+ (au ukiwa na 8GB)

### W10 · Stock & Reorder Agent (🟡) — inaunganishwa na Faida POS
Schedule → POS DB → forecast rahisi (average daily sales × lead time) → PO draft → approval → supplier WhatsApp/email.

### W11 · Multi-channel Review & Reputation Manager (🟢)
Google Business reviews + Facebook comments + Instagram DMs → classify sentiment → reply drafts → approval → post reply → weekly NPS report. **Hoteli na clinics wanapenda hii.**

### W12 · AI Onboarding / Training Bot ya staff (🟡)
RAG juu ya manuals zenu (PDF) → Telegram bot → staff anauliza "ninafanyaje refund?" → jibu + picha. **Inauza yenyewe kwa sababu inapunguza simu kwako.**

### W13 · Crypto/Market Intel Agent (🟢) — **tazama doc 07 kwa mipaka**
Public API (Binance/CoinGecko) → thresholds → AI summary → Telegram. Monitoring only; hakuna kutrade kwa niaba ya mtu.

---

## Mambo ya kuepuka (yanaua margin na reputation)

| Kosa | Kwa nini | Badala yake |
|---|---|---|
| Kuuza "AI chatbot" kwa bei ya 300k | Unashindana na Fiverr, unapata wateja wabaya | Uza "lead response <15 min" kwa 900k+ |
| Ku-automate kitu ambacho mteja hajui kufanya kwa mkono | Hakuna baseline = hakuna proof = retainer inakufa | Andika baseline kabla ya build |
| Kumpa mteja editor ya n8n | License issue + support load isiyo na mwisho | Dashboard/Sheets/Telegram pekee |
| Kuahidi "100% automatic" | Hallucination moja = trust imeisha | "95% automatic, 5% approved by you" |
| Kuchukua project 3 kwa wakati mmoja ukiwa solo | Support debt | Max 2 builds + 5 retainers hadi uwe na msaidizi |

---

## Jinsi ya kupanga kazi kwenye n8n yako

1. **Folders/projects** kwa mteja: `CLIENT - Jina`. Kila workflow inaanza na `CLIENT_` na namba.
2. **Credentials** kwa mteja tofauti (usishare key moja).
3. **Workflow ya error global** (Settings → Error Workflow) → Telegram yako. Bila hii hujui mteja analia.
4. **Sticky notes** ndani ya workflow: nini kinaingia, nini kinatoka, nani anamiliki.
5. **Sub-workflows** kwa sehemu zinazojirudia (parse phone, send WhatsApp, log).
6. **Version control:** export JSON kila mabadiliko → git (repo hii!) → `workflows/client-name/`.
