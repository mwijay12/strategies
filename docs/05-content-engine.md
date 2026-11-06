# 05 — Content Engine: kazi 1 → assets 12 (na biashara ndani yake)

Content ni **njia ya kupata clients**, si "kazi ya marketing". Kwa Altraware, kila post inatakiwa ifanye moja ya hivi: (a) ipate namba ya simu, (b) iandae mteja kwa bei, (c) ionyeshe uwezo wa kiufundi.

> Lengo la kihesabu: **post 12 kwa wiki** (2 kwa siku × 6) zinazozalishwa kwa **dakika 40 za binadamu** kwa wiki. Mengine ni workflow.

---

## 1. Mkakati: nguzo 4 (pillars) tu

| Pillar | Nini | Mfano wa Kitz | Inauza nini |
|---|---|---|---|
| **1. Matatizo ya biashara** | Kila post: tatizo la kweli la biashara ya TZ | "Duka lako linapoteza wateja 12 kwa siku kwa sababu hakuna anayejibu WhatsApp baada ya saa 6" | Automation, POS, mifumo |
| **2. Ujenzi (build in public)** | Onyesha code, screens, mistakes | "Tulijaribu kuweka AI kwenye duka la Kariakoo. Ilikataa Kiswahili cha mtaa. Hii ndio tuliyobadilisha." | Trust, uwezo wa tech |
| **3. Elimu ya vitendo** | Mafunzo mafupi yanayotumika | "Jinsi ya kufanya backup ya data ya duka lako kwa M-Pesa statement" | SEO, leads |
| **4. Watu/backstory** | Mwijay, timu, DSM, misheni | "Kwa nini tunaamini software iwe Kiswahili kwanza" | Brand, partnership |

**Kanuni ya lugha:** Kiswahili kwa hisia na mifano; English kwa technical depth na reach ya LinkedIn. Kila piece inazalisha **matoleo mawili**, si tafsiri.

---

## 2. Injini: workflow ya n8n (spec kamili)

```
[Input]  Telegram: sauti / video / maandishi / link
   │
   ├─ kama sauti/video → Whisper (transcribe, Kiswahili) 
   │
   ▼
[1] Clean transcript  (Code: ondoa "eeh", "yaani", rudia; weka alama za mistari)
   ▼
[2] Extract ideas     (AI: hoja 5, hadithi 2, namba 3, mistari 3 inayogusa)
   ▼
[3] Brand voice pass  (AI: system prompt ya Altraware voice + anti-AI-slop rules)
   ▼
[4] Generate 12 assets (AI: structured output / JSON schema — tooles 1 kwa asset)
   ▼
[5] Human review      (Telegram: buttons Approve / Edit / Reject kwa kila asset)
   ▼
[6] Publish/Schedule  (Buffer au Ayrshare au APIs; blog → Next.js repo PR)
   ▼
[7] Log + measure     (Postgres: asset, platform, tarehe, link, engagement ya siku 7)
```

### Assets 12 (output ya workflow moja)

| # | Asset | Platform | Urefu | Kinachohitajika |
|---|---|---|---|---|
| 1 | Blog post (SEO) | Tovuti | 900–1,400 maneno | Title, meta, H2s, schema |
| 2 | LinkedIn post (EN) | LinkedIn | 150–250 maneno | Hook ya mstari 1, mistari mifupi |
| 3 | LinkedIn post (SW) | LinkedIn | 150–250 | Toleo la Kiswahili |
| 4 | X/Twitter thread | X | 6–8 tweets | Namba, mistari fupi |
| 5 | Reels/TikTok script | IG/TikTok | 30–45 sek | Hook 3 sek, beats 5, CTA |
| 6 | IG carousel (slides 6) | Instagram | — | Text kwa kila slide |
| 7 | YouTube/Shorts description | YouTube | — | Title, desc, tags |
| 8 | Newsletter section | Email | 200 maneno | Subject line 3 |
| 9 | WhatsApp broadcast | WhatsApp | <300 chars | Broadcast inavyopaswa kuwa |
| 10 | Telegram channel post | Telegram | — | + picha prompt |
| 11 | Sales DM (kwa prospect) | WhatsApp/LinkedIn | 4 mistari | Personalization slots |
| 12 | FAQ entry (kwa bot) | RAG/bot | — | Swali + jibu |

Namba 11 na 12 ni muhimu zaidi kwa **pesa** — zinauunganisha content na mauzo moja kwa moja.

---

## 3. Prompt blocks (nakili hizi)

### 3.1 Brand voice (weka kwenye Set node au system prompt)

```
Wewe ni mwandishi wa Altraware Technology (Dar es Salaam).
Sauti: mtu mzima mwenye uzoefu, mkalimu lakini mkweli. Hana kelele za marketing.
Lugha: Kiswahili cha kawaida cha biashara (si cha vitabu, si cha mtaa kupita kiasi).
Kanuni:
- Sentensi fupi. Mistari 1–2 kwa paragraph.
- Namba halisi badala ya "nyingi" na "kubwa".
- Mfano mmoja wa Tanzania kwa kila hoja (Kariakoo, M-Pesa, boda, shule, kanisa, soko).
- Hakuna maneno haya: "revolutionize", "game-changer", "unlock", "seamless", "leverage", "in today's fast-paced world", "delve".
- Hakuna emoji zaidi ya 2 kwa post. Hakuna hashtag zaidi ya 3.
- Usidai matokeo tusiyoyafanya. Ukitoa namba, iwe ya client halisi au iwe wazi kuwa ni mfano.
- Mwisho: swali moja au CTA moja. Si zote mbili.
```

### 3.2 Structuring (JSON schema kwa asset 12)

```json
{
  "pillar": "1|2|3|4",
  "hook": "sentensi moja inayosimamisha scroll",
  "core_insight": "hoja kuu kwa sentensi 2",
  "proof": "namba au mfano halisi",
  "assets": {
    "blog":   {"title":"","meta":"","h2":["","",""],"body_md":""},
    "linkedin_en": {"text":""},
    "linkedin_sw": {"text":""},
    "x_thread": {"tweets":[""]},
    "reel_script": {"hook":"","beats":[""],"cta":""},
    "carousel": {"slides":[""]},
    "newsletter": {"subject_options":[""],"body":""},
    "whatsapp_broadcast": {"text":""},
    "telegram_post": {"text":""},
    "sales_dm": {"text":""},
    "faq": {"question":"","answer":""}
  }
}
```

### 3.3 Anti-hallucination guard
```
Ukikosa data ya kutosha kwa sehemu yoyote, andika "NEEDS_INPUT: <nini kinahitajika>".
Usibuni tarehe, bei, majina ya wateja, au asilimia. 
```

---

## 4. Mzunguko wa kila wiki (rhythm)

| Siku | Kazi | Muda |
|---|---|---|
| Jumatatu | Rekodi video/voice moja (dakika 8–12) kuhusu jambo moja | 25 min |
| Jumatatu | Run workflow → pata assets 12 → approve kwenye Telegram | 15 min |
| Jumanne–Alhamisi | Post 2/siku (auto-scheduled) | 0 min |
| Jumatano | Tuma teardown 1 kwa prospects 10 (kutoka doc 09) | 20 min |
| Ijumaa | Blog inapanda + newsletter | 10 min |
| Ijumaa | Angalia metrics: leads, replies, saves | 10 min |
| **Jumla** | | **~1h 20m/wiki** |

**Kanuni:** rekodi **moja** kwa wiki. Kila kitu kingine ni matokeo yake. Ukianza kuandika kila post mwenyewe, utaacha baada ya wiki 3.

---

## 5. Content plan ya siku 90 (mada 12 zinazouza)

1. "Kwa nini duka lako linapoteza wateja 12 kwa siku (na jinsi ya kuipima)"
2. "Tulijaribu AI kwenye WhatsApp ya duka la Kariakoo — ilishindwa hivi, tukarekebisha hivi"
3. "M-Pesa statement yako inaweza kukuambia stock gani inaisha — hapa ni jinsi"
4. "Faida POS: kile tunachojifunza kutoka maduka 20 ya DSM"
5. "Bei ya tovuti Tanzania: 350k, 900k, 3M — tofauti ni nini kweli"
6. "Offline-first: kwa nini mfumo wako unatakiwa kufanya kazi bila internet"
7. "Ripoti ya kila siku kwa simu ya mmiliki: dakika 30 za setup"
8. "AI Agent vs chatbot: tofauti ambayo inabadilisha gharama"
9. "Shule: jinsi ya kupunguza simu 40 za wazazi kwa siku"
10. "Tender monitoring: jinsi kampuni ya ujenzi ilipata tender 3 kwa mwezi"
11. "Data ya mteja (PDPA 2022): mambo 5 ya lazima kwa biashara ndogo"
12. "Kwa nini tunajenga kwa Kiswahili kwanza (na kwa nini hiyo ni faida ya biashara)"

Kila moja ina **lead magnet** rahisi: checklist PDF, template ya Sheet, au demo ya bot.

---

## 6. Kuchapisha (bila kupoteza siku kwenye API)

| Njia | Faida | Hasara | Ushauri |
|---|---|---|---|
| **Buffer / Metricool** | Platforms 8+, scheduling, analytics | $6–20/mo | **Anza hapa** — API moja tu ya kuunganisha |
| **Ayrshare** | API moja kwa social zote | $20+/mo | Nzuri kwa multi-client |
| **LinkedIn API moja kwa moja** | Bure | Scope approval inachukua wiki; personal profile posting ni restricted | Usianze hapa |
| **Meta Graph (IG/FB)** | Bure | Setup ya app review | Mwezi wa 2+ |
| **Manual copy kutoka Telegram** | 0 gharama, 0 kuvunjika | Dakika 2 kwa post | Sahihi kwa mwezi wa kwanza |

**Ushauri wa kweli:** mwezi 1 — workflow inatengeneza, wewe unacopy-paste kutoka Telegram. Mwezi 2 — Buffer. Mwezi 3 — APIs moja kwa moja kwa clients wanaolipa.

---

## 7. Content kama bidhaa (hii ndio pesa ya kweli)

Wateja wanaotaka content engine (na hawajui n8n):

| Mteja | Kile wanachokosa | Offer yako | Bei |
|---|---|---|---|
| Hoteli / lodge (Zanzibar, DSM) | Posts za kila siku, bei za rooms, reviews | "Content + review manager: posts 12/wiki + majibu ya reviews" | TZS 600k–900k/mo |
| Tour operator | Itinerary content, SEO, inquiries | "Inquiry → itinerary PDF → follow-up" | 700k–1.2M/mo |
| Shule / chuo | Taarifa kwa wazazi, matukio, admissions | "Parent comms engine + admissions follow-up" | 400k–700k/mo |
| NGOs | Donor reports, social proof | "Impact story engine + report drafts" | 500k–900k/mo |
| Restaurant / salon | Menu posts, offers, bookings | "Offers + booking bot" | 350k–600k/mo |

**Pricing logic:** unauza **output** (posts 48/mo, reports 4/mo), si "workflow". Retainer ina jumuisha: workflow, hosting, approvals 2/mwikifanya na wewe, ripoti ya monthly.

**Jinsi ya kuianzisha:** chukua mteja mmoja wa Faida POS/CareTrack aliye na social accounts zilizokufa → toa **wiki 2 bure** → onyesha analytics → uza retainer. Case study moja inatosha kuuza tano.

---

## 8. Kupima (vitu 5 tu, kila Ijumaa)

1. **Reach** → watu wapya wangapi waliona (per platform).
2. **Saves/Shares** → hii ni dalili ya ubora, si likes.
3. **Profile visits → DM/WhatsApp** → hii ndio lead.
4. **Leads** (kutoka workflow W1) → ngapi, ngapi HOT.
5. **Content → deal** → deal ngapi zilitokea kwenye post (weka `?src=li-post-12` kwenye links).

Kama baada ya wiki 6 hakuna lead yoyote: tatizo si content, ni **offer** au **niche**. Rudi doc 06.
