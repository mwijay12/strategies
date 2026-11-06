# Altraware × n8n × AI Agents — Playbook (Dar es Salaam)

Mkusanyiko wa mikakati, workflows na templates za kugeuza n8n (kwenye AWS 4GB, `mwijay.duckdns.org`) kuwa **chanzo cha mapato ya kurudia** kwa Altraware Technology — si mradi wa majaribio.

📅 Imeandaliwa: Novemba 2026 · Mmiliki: Mwijay · Mkoa: Dar es Salaam, Tanzania

---

## Soma kwa mpangilio huu

| # | Doc | Kinachojibu |
|---|---|---|
| 01 | [Mkakati wa Pesa](01-mkakati-wa-pesa.md) | Pesa inatoka wapi, bei, mapato 4 ya kampuni, fomula ya kuhesabu bei |
| 02 | [Catalog ya Workflows](02-workflows-catalog.md) | Workflows 13 za kuuza, kwa kipaumbele, node kwa node, bei |
| 03 | [AWS 4GB Architecture](03-aws-4gb-architecture.md) | docker-compose, memory tuning, backup, security, scaling |
| 04 | [AI Agents, Hermes & Telegram](04-ai-agents-hermes-telegram.md) | Agents 4 za kuuza, Hermes, memory, guardrails, gharama halisi |
| 05 | [Content Engine](05-content-engine.md) | Kazi 1 → assets 12, prompt blocks, mzunguko wa wiki, content kama bidhaa |
| 06 | [Kupata Clients](06-client-acquisition.md) | ICP wa TZ, offer ladder, scripts, objections, compliance (BRELA/TRA/PDPA) |
| 07 | [Crypto + Automation](07-crypto-automation-guardrails.md) | Sheria za TZ, workflows 6 salama, njia ya kujifunza siku 90, red flags |
| 08 | [Roadmap ya Siku 90](08-roadmap-90-days.md) | Wiki kwa wiki, KPIs, hatari 5, uamuzi wa siku 90 |
| 09 | [Templates & Scripts](09-templates-scripts.md) | Teardown, proposal, mkataba, ujumbe wa WhatsApp, ripoti ya mteja |
| 10 | [Sources](10-sources.md) | Viungo vya uthibitisho na kusoma zaidi |

**Workflows za n8n (JSON):** [`workflows/`](../workflows/) — `alt-01-lead-intake.json` ndio ya kuanza nayo (import → credentials → test kwa curl).

---

## Muhtasari wa dakika 3 (kama huna muda)

**Tatizo:** n8n iko tayari, lakini haina *offer*, *bei*, wala *deal flow*.

**Jibu la doc hizi:**
1. **Uza matokeo, si tech.** "Lead inagusiwa ndani ya dakika 15" inauzwa TZS 900k–1.5M; "n8n workflow" hauzwi.
2. **Build + retainer, kila mara.** Build TZS 800k–3.5M, retainer TZS 250k–700k/mo. Retainers ndio uhuru.
3. **Anza na workflows 2:** Lead Intake (W1) + Daily Owner Report (W3). Zinauma, ni rahisi kuonyesha, zinaongezwa kwenye kila mfumo mliyojenga.
4. **4GB inatosha** kwa wateja 5–8 ukitumia Postgres + pruning + `NODE_OPTIONS` + filesystem binary mode. Usiweke local LLM hapo.
5. **Telegram ni office yako:** `/leads`, `/report`, `/content`, `/status` — na ndio demo hai unayoonyesha kwa mteja.
6. **Wateja wa kwanza 3 wanatoka kwa watu waliokwisha kuona kazi yako** (clients wa Faida POS, CareTrack, shule), si kwa cold DM.
7. **Crypto:** jifunze siku 90, jenga monitoring/journal bots tu — usiingie kwenye "signals" wala kushika fedha za watu (BoT 2019 + hakuna VASP license).

---

## Kazi 7 za wiki hii (tiki ukiisha)

- [ ] Pima AWS (`free -h`, `docker stats`, OOM logs) → weka env vars za memory + pruning (doc 03)
- [ ] Error Workflow → Telegram yako
- [ ] Backup + restore test moja
- [ ] Telegram bot: `/status` na `/leads`
- [ ] Import `alt-01-lead-intake.json`, test kwa `curl`
- [ ] Orodhesha watu 40 waliokwisha kuona kazi yako
- [ ] Tuma teardown kwa 10 (template iko doc 09)

---

## Kanuni 5 zisizobadilika

1. Hakuna build bila retainer.
2. Hakuna kazi bila deposit (50%).
3. Hakuna ujumbe wa mteja unaotoka bila approval — miezi 3 ya kwanza.
4. Hakuna mteja anayepewa login ya n8n (license + support load).
5. Kila kitu kilichojengwa kwa mteja kina handover pack (code, manual, video).

---

*Docs hizi ni rasimu za kazi — si ushauri wa kisheria, kodi, wala uwekezaji. Kwa mikataba na masuala ya kodi/PDPA/crypto, pitisha kwa wakili na mhasibu wa Tanzania.*
