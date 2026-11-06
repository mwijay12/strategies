# 01 — Mkakati wa Pesa: n8n, AI Agents na Altraware

> **Swali la msingi:** kwa nini "lines of code" zinaweza kuzalisha pesa zaidi ya mshahara?
> Jibu: kwa sababu code inayobadilisha *gharama ya kurudia-rudia* inauzwa kwa **mwezi**, si kwa saa.

Hii ni doc ya kwanza kati ya 9. Soma kwa mfuatano: hii inatoa *framework*, docs 02–05 zinatoa *vifaa*, 06–09 zinatoa *utekelezaji*.

---

## 1. Ukweli wa kiuchumi: pesa iko kwenye "recurring pain", si kwenye tech

Mteja hajui n8n ni nini. Hajui AI Agent ni nini. Anajua mambo matatu tu:

1. **Anapoteza pesa** kila siku kwa kazi inayorudiwa (mtu anapiga simu, anaandika kwa mkono, anasahau follow-up).
2. **Anapoteza wateja** kwa sababu hajibu WhatsApp kwa haraka.
3. **Hana uhakika** wa namba zake (mauzo, stock, madeni) mpaka mwisho wa mwezi.

Code inayogusa moja ya matatu hayo inauzwa kwa **TZS 800,000 – 4,000,000** kwa build + **TZS 250,000 – 1,200,000/mo** kwa retainer. Hiyo ni hesabu tofauti kabisa na tovuti ya 350,000.

### Fomula ya bei (tumia hii kila siku)

```
Thamani ya mwaka = (masaa yanayopotea kwa wiki × 4.3 × 12) × gharama ya saa
Bei ya build     = 15% – 30% ya thamani ya mwaka  (ya kwanza)
Retainer / mwezi = 8% – 15% ya bei ya build      (hosting, monitoring, mabadiliko)
```

Mfano halisi (duka la Kariakoo, mSomi wa mauzo anapoteza saa 10/wiki kwenye ripoti na follow-up ya madeni, gharama ya saa TZS 3,500):

- Thamani ya mwaka ≈ 10 × 4.3 × 12 × 3,500 = **TZS 1,806,000**
- Build = 25% ≈ **TZS 450,000 – 900,000** (uza kwa 800,000 ukiwa na dashboards + training)
- Retainer = **TZS 250,000/mo** (hosting, monitoring, ripoti 1 mpya/mwezi)

Weka hesabu hii kwenye *kila* proposal. Mteja asione "gharama ya software", aone "gharama ya kutokuwa na mfumo".

---

## 2. Mapato 4 ya Altraware (usichanganye, zigawanye)

| Line | Kinachouzwa | Bei (TZS) | Mara ngapi | Ugumu | Margin |
|---|---|---|---|---|---|
| **A. Builds** (project) | Tovuti, app, mfumo (mlango wenu wa sasa) | 350k – 4M | Mara moja | Juu | 60–75% |
| **B. Automation builds** | Workflow 1–3 zinazogusa mauzo/hela | 800k – 3.5M | Mara moja | Kati | 80–90% |
| **C. Retainers** (hii ndiyo dhahabu) | Hosting + monitoring + mabadiliko + ripoti | 150k – 1.2M/mo | **Kila mwezi** | Chini | 85–95% |
| **D. Content/Media engine** | Content factory inayoendeshwa na AI kwa brand ya mteja | 400k – 900k/mo | Kila mwezi | Chini | 90% |

**Kanuni:** kila build (A/B) lazima izalishe retainer (C/D). Build bila retainer = kazi ya mkono; retainer bila build = mapato ya uhakika.

> Lengo la Mwaka 1: **retainers ziwe 60%+ ya mapato.** Mteja 10 × TZS 400,000/mo = TZS 4,000,000/mo bila kuuza kitu kipya. Hiyo ndiyo "lines of code that make money".

---

## 3. Positioning: wewe si "msee wa n8n", wewe ni "the operations layer"

Watu wanaouza n8n wanashindana kwa bei. Watu wanaouza **matokeo** hawashindani kwa bei.

**Kifungu kimoja (tumia kwenye bio, LinkedIn, WhatsApp):**

> *Altraware — Dar es Salaam. Tunajenga tovuti, mifumo na automation za AI zinazopunguza kazi ya mkono kwa biashara za Tanzania. Kiswahili kwanza, M-Pesa ndani, offline-first. Tunaanza na audit ya bure ya dakika 20.*

Vitu vitatu vinavyotutofautisha (na ambavyo agency za Ulaya hazina):
1. **Kiswahili-first** + uelewa wa mazungumzo ya biashara ya Kitanzania (si translation ya Google).
2. **M-Pesa / Tigo Pesa / Airtel Money** ndani ya workflow (reconciliation, STK push, SMS fallback).
3. **Offline/2G resilience** — tumeshajenga hii (Faida POS), ni proof halisi.

---

## 4. Njia tano za kupata pesa haraka (bila kuajiri)

1. **Operations Audit** (paid, TZS 150,000, inakatwa kwenye build) — siku 2 za kazi, inauza build yenyewe.
2. **Pilot** ya workflow moja kwa TZS 600,000 – 1,200,000 (siku 5–7) — inatoa case study + testimonial.
3. **Automation retainers** zilizounganishwa na tovuti/mifumo mliyojenga (upsell kwa clients waliopo — **anza hapa**, ni rahisi kuliko mteja mpya).
4. **Content-engine-as-a-service** kwa hoteli/tour operators/schools (Zanzibar + DSM) — 400k–900k/mo, delivery ni workflow mmoja.
5. **Tender & compliance monitoring agent** kwa makampuni ya ujenzi/supply — wanapoteza tenders kwa sababu hawafuatilii PPRA kwa wakati. Hii inauzwa kwa 15k–35k USD/mwaka kwa kampuni kubwa.

---

## 5. Sheria za maamuzi (zifiche kichwani)

- **Ukubwa wa RAM ndio ukomo wa biashara, si mawazo.** Kwenye 4GB unaweza kuendesha ~4–6 concurrent workflows. Uza **quality**, si volume, hadi uhamie 8GB.
- **Local LLM kwenye 4GB = kosa.** Modeli ya 8B inahitaji 5–6GB peke yake. Tumia APIs (OpenRouter/Groq/Gemini) — gharama kwa workflow ni TZS 20–200 kwa mteja kwa mwezi.
- **License ya n8n:** unaruhusiwa ku-build na kuendesha workflows za wateja kwenye instance yako **kama wateja hawapati editor**. Kumpa mteja login ya n8n yako = inahitaji Enterprise license. Soma `03` na `06` kabla ya kuuza.
- **Usiuze "bot" — uza "follow-up inayotokea ndani ya dakika 2, kila siku, bila mtu kukumbuka".**
- **Kila kitu kinachoenda kwa mteja kinapita Telegram yako kwa approval** (human-in-the-loop) hadi uwe na miezi 3 ya data safi.
- **PDPA 2022 (Tanzania):** ukishika data za watu (namba, majina, madeni, health), unahitaji makubaliano ya data, storage salama, na uwezekano wa kusajiliwa kama data collector/processor. Weka kwenye contract, ongea na lawyer mara moja.

---

## 6. Nini cha kufanya wiki hii (7 hatua)

1. Soma `03-aws-4gb-architecture.md` na tumia env vars zake — instance yako isianguke katikati ya demo.
2. Chagua **workflows 2 tu** kutoka `02` (pendekezo: **W1 Lead Intake** + **W4 Daily Owner Report**).
3. Jenga demo kwa data ya kubuni (duka la kubuni, "Mama Neema") — video ya dakika 3 kwenye simu.
4. Andika offer moja kwa sentensi moja + bei. Kisha useme "ndiyo" kwa kila mtu anayeuliza.
5. Orodhesha **watu 40** waliokwisha kuona kazi yako (clients wa Faida POS, CareTrack, shule, church app, marafiki wa tech). Anza hapo, si kwa cold DM.
6. Tuma teardown 1 kwa watu 10 (template iko `09`).
7. Weka Telegram bot yako ifanye kazi: `/leads`, `/report`, `/content` — itakuwa demo yako hai mbele ya mteja.

---

## 7. Viungo vinavyosaidia (repos halisi, zilizothibitishwa)

| Repo | Stars | Cha kuchukua |
|---|---|---|
| `n8n-io/n8n` | 209k | Core; AI nodes ziko free edition |
| `Zie619/n8n-workflows` | 57k | Library kubwa ya JSON za ku-import (5,000+) |
| `enescingoz/awesome-n8n-templates` | 26k | Templates 280+ zilizoainishwa kwa category |
| `czlonkowski/n8n-mcp` | 23k | Ku-connect Claude/Cursor na n8n yako — ku-build workflows kwa prompt |
| `n8n-io/self-hosted-ai-starter-kit` | 15k | Docker stack ya local AI (tumia kwenye laptop, si kwenye 4GB) |
| `wassupjay/n8n-free-templates` | 6.2k | 200+ templates + vector DB/AI |
| `NousResearch/hermes-agent` | — | Agent runtime ya Hermes (Telegram gateway, 40+ tools) |
| `e2b-dev/awesome-ai-agents` | 30k | Orodha ya frameworks za agents |

> Angalia stars/date kabla ya kutegemea repo — nyingi zina templates za zamani ambazo hazitafanya kazi na n8n 2.x. Import, fungua, hakiki nodes, kisha tumia.
