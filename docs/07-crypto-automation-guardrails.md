# 07 — Crypto + Automation: njia salama ya kujifunza (na pesa iko wapi kweli)

> **Tukiseme kwa uwazi kwanza:** bot haiundi "edge". Edge inatoka kwa data, nidhamu, na gharama ndogo. Watu wanaouza "VIP signals" kwa Telegram wanauza *matumaini*, na wengi wao ni scams au wanaingia kwenye matatizo ya kisheria. Pesa ya uhakika kwako iko kwenye **kuuza automation kwa biashara halisi**; crypto ni uwanja wa kujifunza + content niche, si mpango wa kujilipa mshahara.

---

## 1. Hali halisi ya sheria Tanzania (soma kabla ya kujenga chochote)

| Kipengele | Hali (kama ilivyoripotiwa 2026) |
|---|---|
| **Msimamo wa BoT** | Public Notice ya Novemba 2019: virtual currency si legal tender; biashara/kumtangaza/kuitumia kunakataliwa; hatari za money laundering na ulaghai zimetajwa |
| **Kodi** | Finance Act 2024: **3% withholding tax** kwenye uhamisho wa digital assets (kutoka 1 Julai 2024); TRA inatambua VASPs kwa madhumuni ya kodi |
| **Mahakama** | Desemba 2024, *Yellow Card Tanzania Ltd v. Nyamwero*: Mahakama Kuu ilikataa hoja kuwa mkataba wa crypto ni batili; ilisema kwa kuwa shughuli hizi zinatozwa kodi, haziwezi kuitwa haramu. Hukumu ililipa USD 1.193M |
| **Leseni** | **Hakuna** mfumo wa VASP licensing Tanzania. Hakuna leseni ya kupata, na hakuna register ya makampuni yaliyoidhinishwa |
| **PDPA 2022** | Inahusu data za watu — inatumika pia kwenye crypto apps unazojenga |

**Maana yake kivitendo:**
- Personal trading: si legal tender, lakini mahakama imeona mikataba ni enforceable; wewe unafanya kwa hatari yako, na unalipa kodi.
- **Usijenge biashara inayochukua fedha za watu wengine** (custody, pooled trading, "tunawekeza kwa niaba yako", "faida ya 20% kwa mwezi") bila leseni na bila lawyer. Hii ni njia ya moja kwa moja kwenye mashtaka na kufungwa.
- Kuuza **elimu na tooling** (bila kutoa ushauri wa uwekezaji, bila kuahidi faida, bila kushika fedha) ni tofauti sana — lakini bado hakikisha na wakili wako, na useme wazi "hii ni elimu, si ushauri wa kifedha".
- Weka kumbukumbu za kila transaction kwa TRA (statement, tarehe, kiasi, counterparty). 3% WHT inahitaji records safi.

---

## 2. Kile automation inaweza kufanya kwa kweli (na kile haiwezi)

| ✅ Inaweza | ❌ Haiwezi |
|---|---|
| Kukusanya bei, volume, funding rates, news kwa dakika 2 | Kutabiri bei |
| Kukutumia alert ukiwa busy (na kuzuia kuangalia chati kila dakika) | Kukupa "signal" yenye faida ya uhakika |
| Kuweka journal ya kila trade na kuchambua makosa yako | Kufuta hasara |
| Ku-run backtest ya mkakati kwa miaka 3 | Kuhakikisha mkakati utafanya kazi future |
| Ku-monitor wallet yako (read-only) na ku-alert movement | Kukulinda dhidi ya scam links |
| Kuandika maudhui ya elimu (content engine) | Kukupa leseni ya ushauri wa kifedha |

**Kanuni ya binafsi (andika, isome kila mwezi):**
1. Hakuna fedha za kukopesha, za kodi, au za biashara.
2. Kikomo: 2% ya portfolio kwa trade moja; hakuna leverage hadi uwe na miezi 6 ya journal.
3. Bot ya kwanza ni **read-only**. Bot ya pili ni **paper trading**. Bot ya tatu (ukifika) ina kill switch.
4. API keys: **no withdrawal permission**, IP allowlist, sub-account tofauti, rotate kila miezi 3.
5. Seed phrase haiko kwenye simu, kamwe. Hardware wallet.
6. Ukishindwa kuelezewa kwa mistari 3 kwa nini unaingia trade, usiingie.

---

## 3. Workflows 6 za kujenga (salama, zinatumika kwenye n8n yako ya 4GB)

Zote ni 🟢 nyepesi (public APIs, schedule, no websockets za muda mrefu).

### C1 · Morning Market Brief (dakika 2 kusoma)
```
Schedule 07:00 EAT
→ HTTP: CoinGecko /simple/price (BTC, ETH, + watchlist)
→ HTTP: Binance klines (24h change, volume)
→ HTTP: news (CryptoPanic / RSS)
→ AI: summary ya mistari 8 kwa Kiswahili + "kitu 1 cha kuangalia leo"
→ Telegram → wewe
```
*Gharama: ~$0. Kanuni: habari, si ushauri. Weka disclaimer kwenye kila message.*

### C2 · Price & Volatility Alerts (zenye vikomo)
```
Schedule 5 min → HTTP prices → Code (check: % change > 5%, au level ulioweka)
→ dedupe (Postgres: usitume alert ile ile mara 20)
→ IF triggered → Telegram + log
```
*Code node ni muhimu: bila dedupe, bot inakuchoka na unaiua.*

### C3 · Portfolio Tracker (read-only)
```
Schedule 21:00 → HTTP: exchange API (READ-ONLY key) balances
→ prices → Code: value, P&L, allocation
→ Postgres (daily snapshot) → Google Sheet (chart) + Telegram
```
*Hii inakupa kitu ambacho watu wengi hawana: historia ya portfolio yako.*

### C4 · Trade Journal Analyzer (hii ndio inayobadilisha tabia)
```
Telegram: /trade (au form) → {pair, side, size, entry, exit, sababu, hisia}
→ Postgres
→ Weekly: AI inachambua: "wewe unashinda 68% ukiwa na plan, 21% ukiwa FOMO"
→ Telegram ripoti + PDF
```
*Hii ni workflow yenye thamani kubwa zaidi kuliko "signal bot" yoyote.*

### C5 · On-chain Wallet Watcher (read-only)
```
Schedule 10 min → Etherscan/BscScan API (address yako)
→ IF outflow / new approval / large transfer → Telegram alert
```
*Hii ni security feature. Inaokoa pesa kweli.*

### C6 · Paper Trading Bot (bila pesa halisi)
```
Schedule → strategy rules kwenye Code node → signals zinaandikwa tu
→ Postgres (paper positions) → daily P&L → Telegram
→ Baada ya miezi 3: angalia expectancy, max drawdown, win rate
```
**Kanuni ngumu:** hakuna kuunganisha order execution hadi uwe na **miezi 3 paper + miezi 3 micro-size (pesa ndogo sana)**. Kama mkakati unashindwa kwenye paper, hauwezi kufanikiwa kwa pesa halisi.

---

## 4. Njia ya kujifunza (siku 90, saa 4 kwa wiki)

| Wiki | Mada | Praktika |
|---|---|---|
| 1 | Jinsi blockchain inavyofanya kazi (blocks, keys, gas) | Fungua wallet ya testnet, tumia faucet |
| 2 | Exchange, order book, spot vs futures, funding rate | Fanya trade 5 kwenye testnet |
| 3 | Risk: position sizing, R:R, drawdown, expectancy | Andika risk plan yako (ukurasa 1) |
| 4 | On-chain basics: explorer, wallet labels, approvals | Chunguza address 5 maarufu |
| 5 | Custody & security: hardware wallet, phishing, approvals | Weka hardware wallet, futa approvals za zamani |
| 6 | Technical analysis (support/resistance, volume) — kwa ufupi | Chora levels 20, andika hypothesis |
| 7 | Kodi na records (TRA, 3% WHT) | Fungua sheet ya records; ongea na accountant |
| 8 | Backtesting: data, overfitting, survivorship bias | Backtest mkakati mmoja kwa Python/Sheets |
| 9 | Building C1–C3 kwenye n8n | Live bots zako |
| 10 | C4 journal + review | Trade 10 za paper + journal |
| 11 | Sheria na maadili (BoT, CMSA, ushauri wa kifedha) | Ongea na wakili; andika "what I will not do" |
| 12 | Uamuzi: unaendelea, unaacha, au unabadilisha niche | Andika ukurasa 1 wa maamuzi |

**Rasilimali za bure:** Bitcoin whitepaper, Investopedia (risk), Binance Academy, CoinGecko learn, `ccxt` docs (github), `freqtrade` (backtesting framework, open source), `vectorbt`. Kwa Kiswahili: YouTube channels za Kitz (hakiki kwa makini — wengi wanauza course).

---

## 5. Crypto kama niche ya biashara yako (njia salama zaidi)

Badala ya kuwa trader, kuwa **mtu anayejenga tooling na content**:

| Offer | Mteja | Bei | Hatari |
|---|---|---|---|
| Content engine kwa brand ya elimu ya crypto | Media brand, educator | 500k–900k/mo | Chini (content pekee) |
| Alert/analytics dashboard (read-only) kwa community | Trading communities | 800k–1.5M build + 200–400k/mo | Kati (usianze kutoa "signals") |
| Automation ya finance ops (si crypto) | Biashara za kawaida | 1.5M+ | Chini |
| Course/training ya "AI + automation kwa finance" | Wafanyabiashara | 150k–300k kwa mtu | Chini |

**Mbali na hiyo:** ukiwa na ujuzi wa n8n + AI, kuna wateja wengi wa **fintech, SACCO, microfinance, insurance brokers** Tanzania wana shida halisi za reconciliation na reporting — wanailipa vizuri, na hakuna hatari ya udhibiti.

---

## 6. Red flags (kama mtu anakuuliza hivi, ni scam)

- "Tuma 0.1 BTC, tunakurudishia 0.2"
- "Bot yetu inatoa 3% kwa siku, hakuna risk"
- "Tupe API key yako yenye withdrawal"
- "Njoo Telegram, admin atakupa signal, tunalipa baada ya faida"
- "Tuna leseni ya UK/ Seychelles" (uliza namba, thibitisha kwenye register)
- "Weka pesa, tunakuwekea kwenye account yetu"

**Kama ni mteja anayetaka wewe ujenge kitu kinachogusa fedha za watu wengine:** kataa, au tumia entity/leseni/legal opinion ya wakili. Reputation ndio asset kubwa zaidi ya Altraware.

---

## 7. Kile ninachoshauri kwa miezi 6 ya kwanza

1. **Kipaumbele 1:** automation za biashara (doc 02) — hii ndio inalipa bili.
2. **Kipaumbele 2:** crypto kama **content niche** + kujifunza (saa 4/wiki), si trading.
3. **Kipaumbele 3:** jenga C1, C4, C5 kwa ajili yako mwenyewe — zinaonyesha uwezo na zinakupa data ya kuandika content inayoaminika.
4. Baada ya miezi 6, ukiwa na journal ya trades 200+ na mkakati uliofanya kazi kwenye paper + micro, *ndio* fikiria execution automation kwa pesa ndogo.

> Ujumbe mmoja: **bot nzuri ya biashara inakupa TZS 400,000 kila mwezi. Bot nzuri ya trading inakupa matumaini.** Chagua kwa makini.
