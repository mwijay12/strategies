# Workflows za n8n (JSON za kuingiza moja kwa moja)

## Jinsi ya ku-import

1. Fungua n8n yako → **Workflows → Create Workflow → ⋯ (juu kulia) → Import from File**.
2. Chagua faili (mf. `alt-01-lead-intake.json`).
3. Fungua nodes 1 kwa 1, weka **credentials** zako:
   - `Telegram Alert` → Telegram Bot credential (token kutoka BotFather).
   - `AI Qualify` → inatumia `$env.OPENROUTER_API_KEY` (weka kwenye `docker-compose.yml` → `environment`) **au** badilisha node iwe `OpenAI` chat model + credential.
4. Weka env vars mbili kwenye container: `OPENROUTER_API_KEY`, `ALERT_CHAT_ID` (soma chat_id yako kwenye Telegram kwa `@userinfobot`).
5. **Test** kwa `curl`:

```bash
curl -X POST https://mwijay.duckdns.org/webhook/lead-intake \
  -H 'Content-Type: application/json' \
  -d '{"name":"Neema M.","phone":"0712345678","need":"Nahitaji mfumo wa duka, ripoti za mauzo kila siku","budget":1200000,"source":"website"}'
```

6. Ukiona alert kwenye Telegram — workflow iko tayari kuuzwa. Badilisha prompt kwa sauti ya mteja na ongeza hatua ya ku-save kwenye Postgres/Sheet.

## Kanuni za faili hizi

- Jina: `alt-<namba>-<jina>.json` — `alt` = Altraware library, si ya mteja.
- Workflows za mteja: `client-<jina>/<workflow>.json` (usizichanganye na library).
- Kila mabadiliko → commit. JSON ya n8n ni code.
- Kabla ya ku-import kwenye production, fungua node za `Code` na soma — usiamini template yoyote 100%.

## Faili zilizopo

| Faili | Kinachofanya | RAM | Inahitaji |
|---|---|---|---|
| `alt-01-lead-intake.json` | Webhook → hakiki simu → AI qualify (HOT/WARM/COLD) → Telegram alert na buttons | 🟡 | OpenRouter/OpenAI key, Telegram bot |
