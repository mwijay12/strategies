# 10 — Sources & Viungo (kwa uthibitisho na kusoma zaidi)

Kumbuka: viungo na takwimu zinabadilika. Hakiki kila kitu kabla ya kukiweka kwenye proposal ya mteja.

---

## A. n8n: uwezo, RAM na self-hosting

| Mada | Chanzo |
|---|---|
| Sizing: 2GB msingi, 4GB kwa AI agents, 8GB+ kwa queue mode | https://sophrai.com/how-much-ram-does-n8n-need/ |
| Memory tuning: `NODE_OPTIONS`, filesystem binary mode, pruning | https://gb.hostadvice.com/blog/web-hosting/vps/optimizing-node-js-memory-usage-for-n8n/ |
| Queue mode + production checklist | https://n8nlab.io/blog/self-hosted-n8n-best-practices-setup-checklist |
| Docker Compose ya kisasa (Traefik/Dokploy), version pinning | https://www.bitdoze.com/n8n-self-host-workflow-automation/ |
| Makosa ya kawaida ya VPS (OOM, timeouts, Redis) | https://www.youstable.com/blog/common-mistakes-when-hosting-n8n-on-a-vps/ |
| RAG kwenye VPS ndogo (pgvector vs Qdrant) | https://jservo.com/jservo-ai-blog/n8n-rag-self-hosted-agent-guide/ |
| Docs rasmi za n8n | https://docs.n8n.io/hosting/ |

## B. License ya n8n (soma kabla ya kuuza)

| Mada | Chanzo |
|---|---|
| Sustainable Use License — unaweza ku-build kwa clients, huwezi kuwapa editor | https://docs.n8n.io/sustainable-use-license/ |
| Muhtasari wa wakili/agency: "outputs, not access" | https://accuweb.cloud/blog/can-an-agency-host-n8n-for-clients |
| Ufafanuzi wa mipaka (white-label, fork, hosted access) | https://www.ssdnodes.com/learn/n8n-sustainable-use-license-explained |
| Maswali ya leseni moja kwa moja | license@n8n.io |

## C. AI Agents & Hermes

| Mada | Chanzo |
|---|---|
| Hermes 4 family (14B/36B/70B/405B, `<tool_call>`, JSON schema) | https://vantaige.io/blog/nous-hermes-4-self-hosted-setup-vs-closed-agents-2026 |
| Hermes Agent runtime (MIT, 40+ tools, Telegram gateway) | https://github.com/NousResearch/hermes-agent |
| Kuunganisha Hermes + Ollama (na mipaka yake) | https://bestllmfor.com/guide/hermes-agent-ollama-guide/ |
| Modeli za tool-calling kwa GPU ndogo | https://localaimaster.com/blog/hermes-agent-ollama |

## D. Templates & repos za workflows

| Repo | Stars (Nov 2026) | Matumizi |
|---|---|---|
| https://github.com/n8n-io/n8n | 209k | Core |
| https://github.com/Zie619/n8n-workflows | 57k | Library ya JSON (5,000+) |
| https://github.com/enescingoz/awesome-n8n-templates | 26k | 280+ templates kwa category |
| https://github.com/czlonkowski/n8n-mcp | 23k | Ku-build workflows kwa prompt (Claude/Cursor) |
| https://github.com/n8n-io/self-hosted-ai-starter-kit | 15k | Local AI stack (kwa laptop) |
| https://github.com/wassupjay/n8n-free-templates | 6.2k | 200+ templates + vector DB |
| https://github.com/e2b-dev/awesome-ai-agents | 30k | Orodha ya agent frameworks |
| https://n8n.io/workflows/ | — | Templates rasmi (na specs nzuri za kusoma) |

## E. Bei na mfumo wa agency

| Mada | Chanzo |
|---|---|
| Pricing models ($500–$20k), retainers, productized | https://taskip.net/ai-automation-agency-pricing/ |
| Hatua ya siku 90 kupata mteja wa kwanza | https://www.husnainbukhari.com/blog/how-to-start-an-ai-automation-agency |
| Scripts za outreach + pilot pricing | https://money-lab.app/blog/how-to-get-first-ai-automation-client-2026 |
| Niche + productized service (AAPS) | https://pyrsonalize.com/blog/starting-an-ai-automation-agency-in-2026-guide/ |
| Namba halisi za build fee na retainer | https://abhyashsuchi.in/how-to-start-ai-automation-agency-2026/ |

## F. Soko la Tanzania

| Mada | Chanzo |
|---|---|
| WhatsApp automation TZ (M-Pesa flows, tourism) | https://answerforme.io/en/blog/whatsapp-business-automation-tanzania-2026-guide |
| AI automation services TZ 2026 (use cases za SME) | https://kepha14.dev/blog/ai-automation-services-in-tanzania-2026 |
| Digital solutions TZ 2026 | https://sigosoft.com/blog/digital-solutions-in-tanzania-2026/ |
| Regional SME automation (KE/UG/TZ) | https://neuroptikai.com/blog/ai-automation-for-small-business-cutting-costs-across-kenya-uganda-tanzania |

## G. Crypto: sheria ya Tanzania

| Mada | Chanzo |
|---|---|
| Muhtasari wa hali (BoT 2019, Finance Act 2024, hakuna VASP law) | https://defi-intel.com/jurisdictions/tz/ |
| Uchambuzi wa kesi ya Yellow Card (Mahakama Kuu, Des 2024) | https://www.dentons.com/en/insights/articles/2025/march/17/the-evolving-regulatory-landscape-of-cryptocurrencies-in-tanzania-implications-on-legal-contracts |
| Muhtasari wa kesi + kodi ya 3% WHT | https://www.afriwise.com/blog/tanzania-courts-decision-on-virtual-assets |

## H. Content & distribution

| Mada | Chanzo |
|---|---|
| Content repurposing kwa n8n (YouTube → LinkedIn/X/blog) | https://n8n.io/workflows/12264-repurpose-youtube-videos-into-blogs-and-social-posts-with-gpt-4o-wordpress-linkedin-x-and-instagram/ |
| Mfumo wa social pipeline (Telegram approval loop) | https://growwstacks.com/blog/automate-social-media-content-n8n-youtube |
| Workflows 10 za biashara 2026 | https://digitalgyan.org/top-10-n8n-workflows-to-automate-your-business/ |

---

## Kanuni ya kutumia sources

1. Kila namba unayoweka kwenye proposal ya mteja, iwe na chanzo au iwe ni **yako** (baseline ya mteja).
2. Usinakili takwimu za blog ya Marekani kama "soko la Tanzania" — tumia tu kwa *mbinu*, si kwa *ukweli wa soko*.
3. Tarehe ya makala ni muhimu: pricing na modeli za 2024 zinaweza kuwa za kizamani 2026.
