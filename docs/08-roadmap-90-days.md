# 08 — Roadmap ya Siku 90 (mpango wa utekelezaji)

Lengo moja: **ifikapo siku 90, Altraware iwe na MRR ≥ TZS 1,000,000 na builds 3 zilizolipwa**, ikitumia infra moja (4GB) na mtu mmoja (wewe) + msaidizi wa part-time.

---

## Awamu 0 · Siku 1–7: Msingi (usiyahamisha)

| # | Kazi | Kipimo cha kufaulu |
|---|---|---|
| 1 | Pima AWS (doc 03 §2), weka env vars za memory + pruning | RAM < 80%, DB < 500MB |
| 2 | Error Workflow → Telegram yako | Unapata alert ukitengeneza kosa kwa makusudi |
| 3 | Backup + restore test moja | Fail la dump limefunguka kwenye DB ya test |
| 4 | UptimeRobot → `/healthz` | Email/SMS inakufikia |
| 5 | Bot ya Telegram: `/status`, `/leads` | Unapata jibu ndani ya sekunde 3 |
| 6 | Pin version ya n8n + git repo ya workflows | `git log` ina commit 2 |
| 7 | Orodhesha watu 40 (doc 06 §3.1) | Orodha iko kwenye Sheet na ina namba |

**Ushauri:** usijenge workflow ya mteja kabla ya #1–#4. Outage ya demo = deal imekufa.

---

## Awamu 1 · Wiki 2–4: Bidhaa + Deal flow

| Wiki | Kazi kuu | Deliverable |
|---|---|---|
| 2 | Jenga **W1 Lead Intake** + **W3 Daily Report** kwa "duka la kubuni" (data ya mfano) | Demo 2 zinazofanya kazi + video dakika 3 |
| 2 | Ukurasa wa "Automation & AI Agents" kwenye tovuti (bei, demo, CTA) | URL hai |
| 3 | Teardown 10 kwa orodha ya 40 (anza na 10 wa joto) | Mikutano 3–4 |
| 3 | Content engine: rekodi video 1, run workflow, chagua 6 assets | Posts 6 zilizopangwa |
| 4 | Audit 2 za kulipia (150k) | **TZS 300,000** + proposal 2 |
| 4 | Ongeza `docs/` hii kwenye repo + andika case study ya Faida POS | Case study ya ukurasa 2 |

**Kizuizi cha awamu hii:** kuuza kabla ya kuwa na demo. Demo = video + bot hai + case study. Hakuna kingine.

---

## Awamu 2 · Mwezi 2: Delivery + case study

| Wiki | Kazi kuu | Deliverable |
|---|---|---|
| 5 | Pilot 1 (W1 au W3) kwa mteja wa kwanza | Baseline + after numbers |
| 5 | Onboarding checklist, SLA, training manual | Pakage la handover |
| 6 | Build 1 (2M) — workflows 2–3 | Go-live + retainer ya kwanza |
| 6 | Content: posts 12/wiki kwa mzunguko | Leads 2–3 kutoka content |
| 7 | Case study #1 (na namba + testimonial) | PDF + post |
| 7 | Referral ombi kwa kila mteja aliyelipwa | Majina 2–3 mapya |
| 8 | Pilot 2 / build 2 | **TZS 1.5–2.5M** |

---

## Awamu 3 · Mwezi 3: Retainers + bei mpya

| Wiki | Kazi kuu | Deliverable |
|---|---|---|
| 9 | Retainers 3 zinaanza (250k–400k) | MRR ~1M |
| 9 | Panda bei kwa mteja mpya (+20%) | Proposal mpya zina bei mpya |
| 10 | Unganisha content engine kwa 1 mteja (retainer wa 4) | MRR 1.3–1.6M |
| 10 | Andika SOPs: jinsi ya ku-deliver bila wewe | Docs 6–8 kurasa |
| 11 | Tathmini: segment ipi inalipa zaidi? | Uamuzi wa niche ya robo 2 |
| 12 | Msaidizi wa part-time (delivery support) au VA | Kazi 20h/wiki zimehamishwa |

---

## Namba za kila wiki (dashboard yako)

Weka kwenye Sheet moja, update kila Ijumaa:

| Metric | Target wiki 4 | wiki 8 | wiki 12 |
|---|---|---|---|
| Mikutano (discovery) | 4 | 8 | 10 |
| Audit zilizolipwa | 2 | 3 | 2 |
| Pilots/builds zilizolipwa | 0 | 2 | 4 |
| MRR (TZS) | 0 | 350,000 | 1,300,000 |
| Cash collected (cumulative) | 300,000 | 3,500,000 | 7,500,000 |
| Leads kutoka content | 0 | 3 | 8 |
| Referrals | 0 | 1 | 3 |
| Failed executions (production) | < 20 | < 15 | < 10 |
| Uptime | 99% | 99.5% | 99.9% |

---

## Hatari 5 na dawa zake

| Hatari | Dalili | Dawa |
|---|---|---|
| **Delivery inakula mauzo** | Wiki 3 bila teardown | Weka Jumanne 09:00–10:00 kama "sales block" isiyoguswa |
| **Scope creep** | "Kitu kimoja tu cha ziada" kila wiki | Change order ya maandishi; masaa 4/mwezi tu kwenye retainer |
| **OOM outage** | n8n inakufa nyakati za peak | doc 03 §4 swap + concurrency limit 4 + alert |
| **Retainer churn** | Mteja haoni namba | Ripoti ya mwezi na metrics 3 (muda, pesa, makosa) |
| **Kutegemea mteja mmoja** | 60% ya mapato kwa mtu 1 | Kanuni: hakuna mteja > 35% ya mapato ifikapo mwezi 6 |

---

## Vitu 5 vya kufanya kama unaweza kufanya vichache tu

1. **Weka Error Workflow + backup.** (siku 1 — inaokoa reputation)
2. **Jenga W3 (Daily Report) na uifanye demo ya dakika 3.** (siku 2 — inauza yenyewe)
3. **Tuma teardown 10 kwa watu wa joto.** (siku 1 — deal ya kwanza iko hapa)
4. **Rekodi video 1 kwa wiki na iruhusu engine iandike mengine.** (dakika 40 — lead flow)
5. **Kila build lazima iwe na retainer.** (kanuni — MRR ndio uhuru)

---

## Uamuzi wa siku 90 (andika majibu)

- Segment ipi ililipa haraka zaidi? → __________
- Workflow ipi iliuzwa mara nyingi? → __________
- Ni kitu gani kilinigharimu muda mwingi bila malipo? → __________
- Bei inapaswa kupanda kwa asilimia ngapi? → __________
- Nini kitafanywa na msaidizi wa kwanza? → __________
- Je, ninahitaji 8GB? (Dalili: RAM > 85% siku 3, executions > 50k/mo) → __________

Baada ya majibu haya, robo inayofuata inakuwa wazi.
