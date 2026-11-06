# 03 — AWS 4GB: Architecture ya n8n isiyoanguka

Lengo: n8n yako ya `https://mwijay.duckdns.org` iendeshe **workflows 20+, wateja 5–8, executions 30,000–80,000/mo** kwenye **4GB RAM** bila OOM (Out Of Memory) kila wiki.

> Kumbuka: mimi (agent) sikuweza kufikia host yako kutoka sandbox (SSL blocked). Hivyo hatua ya kwanza ni **kuipima mwenyewe** — amri ziko chini.

---

## 1. Ukweli wa RAM: 4GB imegawanywa vipi

| Component | RAM ya kawaida | Notes |
|---|---|---|
| OS + Docker daemon | 500–700 MB | Ubuntu 24.04 minimal |
| n8n main (Node.js) | 1,200–1,800 MB | 40–50 MB kwa execution inayoendelea |
| PostgreSQL 16 | 700–1,000 MB | tuned (tazama §5) |
| Caddy (TLS proxy) | 40–60 MB | badala ya Nginx + Certbot (rahisi) |
| Headroom / cache | 500–800 MB | **usiipe mtu** — hapa ndipo OOM inatokea |

**Ukomo wa concurrent executions:** 3–4 za kawaida, **2** kama zina AI/file processing. Zaidi ya hapo Node.js inaanza kufa.

Sheria: `EXECUTIONS_MODE=regular` (bila Redis) hadi ufikie **~50,000 executions/mo au CPU >80% kwa dakika 30 kila siku**. Queue mode kwenye 4GB inaongeza ~1GB baseline — haina maana bado.

---

## 2. Pima kwanza (nakili hizi kwenye server)

```bash
# 1. Resources
free -h; nproc; df -h /

# 2. n8n version + health
docker ps --format '{{.Names}}\t{{.Image}}\t{{.Status}}'
curl -s localhost:5678/healthz; echo

# 3. RAM kwa container
docker stats --no-stream

# 4. OOM kills zilizotokea
sudo dmesg | grep -i -E 'oom|killed process' | tail -20

# 5. Database size (kama >1.5GB, pruning haifanyi kazi)
docker exec -it <postgres-container> psql -U n8n -d n8n -c \
  "select pg_size_pretty(pg_database_size('n8n'));"

# 6. Executions zinazokaa
docker exec -it <postgres-container> psql -U n8n -d n8n -c \
  "select count(*) from execution_entity;"
```

Ukiona `execution_entity` > 150,000 au DB > 2GB → tatizo ni pruning, si RAM.

---

## 3. docker-compose.yml (production, 4GB, Postgres + Caddy)

Weka kwenye `/opt/n8n/docker-compose.yml`. Badilisha `CHANGE_ME` na funga faili la `.env` (`chmod 600`).

```yaml
name: n8n

services:
  postgres:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      POSTGRES_USER: n8n
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: n8n
      # Tuning kwa 4GB — tazama §5
      POSTGRES_INITDB_ARGS: "--data-checksums"
    command: >
      postgres -c shared_buffers=320MB -c effective_cache_size=768MB
               -c work_mem=8MB -c maintenance_work_mem=64MB
               -c max_connections=40 -c wal_compression=on
               -c checkpoint_completion_target=0.9 -c random_page_cost=1.1
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U n8n -d n8n"]
      interval: 30s
      timeout: 5s
      retries: 5
    deploy:
      resources:
        limits:
          memory: 1200M

  n8n:
    image: docker.n8n.io/n8nio/n8n:latest   # PIN version! tazama §7
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy
    environment:
      # --- DB ---
      DB_TYPE: postgresdb
      DB_POSTGRESDB_HOST: postgres
      DB_POSTGRESDB_DATABASE: n8n
      DB_POSTGRESDB_USER: n8n
      DB_POSTGRESDB_PASSWORD: ${POSTGRES_PASSWORD}
      # --- Core ---
      N8N_HOST: mwijay.duckdns.org
      N8N_PROTOCOL: https
      N8N_PORT: 5678
      WEBHOOK_URL: https://mwijay.duckdns.org/
      N8N_EDITOR_BASE_URL: https://mwijay.duckdns.org/
      N8N_PROXY_HOPS: "1"
      GENERIC_TIMEZONE: Africa/Dar_es_Salaam
      TZ: Africa/Dar_es_Salaam
      N8N_ENCRYPTION_KEY: ${N8N_ENCRYPTION_KEY}   # BACKUP! ukiiipoteza, credentials zote zinafungwa
      N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS: "true"
      # --- Memory (muhimu kwa 4GB) ---
      NODE_OPTIONS: "--max-old-space-size=2048"
      N8N_DEFAULT_BINARY_DATA_MODE: filesystem
      N8N_PAYLOAD_SIZE_MAX: "16"
      N8N_RUNNERS_ENABLED: "false"        # inaokoa ~150–300MB; tradeoff: Code node haiko isolated
      N8N_CONCURRENCY_PRODUCTION_LIMIT: "4"
      # --- Pruning (bila hii DB inakua milele) ---
      EXECUTIONS_DATA_PRUNE: "true"
      EXECUTIONS_DATA_MAX_AGE: "168"          # siku 7
      EXECUTIONS_DATA_PRUNE_MAX_COUNT: "20000"
      EXECUTIONS_DATA_SAVE_ON_SUCCESS: all    # badilisha "none" kwa workflows za volume kubwa
      EXECUTIONS_DATA_SAVE_ON_ERROR: all
      EXECUTIONS_TIMEOUT: "300"               # sekunde 5 — linda dhidi ya infinite loops
      EXECUTIONS_TIMEOUT_MAX: "900"
      # --- Security / noise ---
      N8N_BLOCK_ENV_ACCESS_IN_NODE: "true"
      N8N_DIAGNOSTICS_ENABLED: "false"
      N8N_VERSION_NOTIFICATIONS_ENABLED: "false"
      N8N_LOG_LEVEL: info
      N8N_LOG_OUTPUT: console
      N8N_SECURE_COOKIE: "true"
    ports:
      - "127.0.0.1:5678:5678"   # hakuna mtu anafika moja kwa moja; Caddy inapitisha
    volumes:
      - n8ndata:/home/node/.n8n
      - ./files:/files         # binary data (filesystem mode)
    healthcheck:
      test: ["CMD-SHELL", "wget -qO- http://127.0.0.1:5678/healthz || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 60s
    deploy:
      resources:
        limits:
          memory: 2400M

  caddy:
    image: caddy:2-alpine
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddydata:/data
    deploy:
      resources:
        limits:
          memory: 128M

volumes:
  pgdata:
  n8ndata:
  caddydata:
```

**Caddyfile:**

```
mwijay.duckdns.org {
  encode zstd gzip
  reverse_proxy n8n:5678
  header {
    Strict-Transport-Security "max-age=31536000; includeSubDomains"
    X-Content-Type-Options nosniff
    X-Frame-Options SAMEORIGIN
    Referrer-Policy strict-origin-when-cross-origin
  }
}
```

**Kwa nini Caddy:** HTTPS ya automatic (Let's Encrypt), config mistari 5, memory ndogo. DuckDNS inahitaji port 80 wazi kwa HTTP-01 challenge.

---

## 4. Swap + kernel (hii inaokoa OOM mara nyingi)

```bash
# 2GB swap (badala ya kufa, n8n itachelewa kidogo)
sudo fallocate -l 2G /swapfile && sudo chmod 600 /swapfile
sudo mkswap /swapfile && sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
# Tumia swap tu ukiwa desperate
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf

# Docker log rotation (logs zinaweza kula disk kimya kimya)
sudo tee /etc/docker/daemon.json >/dev/null <<'EOF'
{ "log-driver": "json-file", "log-opts": { "max-size": "10m", "max-file": "3" } }
EOF
sudo systemctl restart docker

# UFW
sudo ufw allow 22/tcp; sudo ufw allow 80,443/tcp; sudo ufw --force enable
# Zuia SSH kwa password; tumia keys au AWS SSM Session Manager tu
```

---

## 5. Postgres tuning (kwa 4GB, shared box)

| Param | Value | Kwa nini |
|---|---|---|
| `shared_buffers` | 320MB | 25% ya RAM inayotolewa kwa DB |
| `effective_cache_size` | 768MB | msaidie planner |
| `work_mem` | 8MB | concurrency 4 × 8MB ni salama |
| `maintenance_work_mem` | 64MB | vacuum/index |
| `max_connections` | 40 | n8n inatumia ~10; baki ni reserve |
| `random_page_cost` | 1.1 | kwa SSD (gp3/EBS) |

Ongeza index (inafanya execution list kuwa haraka sana):

```sql
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_execution_workflow_started
  ON execution_entity ("workflowId", "startedAt" DESC);
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_execution_status
  ON execution_entity (status);
```

---

## 6. Backup + monitoring (usikae bila hivi hata siku moja)

**Backup ya kila siku (script + cron 02:30 EAT):**

```bash
#!/usr/bin/env bash
set -euo pipefail
TS=$(date +%F-%H%M)
DEST=/opt/backups
mkdir -p "$DEST"
# 1. Database
docker exec n8n-postgres-1 pg_dump -Fc -U n8n n8n > "$DEST/n8n-$TS.dump"
# 2. n8n data dir (encryption key, settings)
docker run --rm -v n8n_n8ndata:/data -v "$DEST":/backup alpine \
  tar czf "/backup/n8ndata-$TS.tar.gz" -C /data .
# 3. Upload S3 (bucket yako; tumia IAM role ya EC2, usiweke keys kwenye script)
aws s3 cp "$DEST/n8n-$TS.dump"     s3://YOUR-BUCKET/n8n/db/     --storage-class STANDARD_IA
aws s3 cp "$DEST/n8ndata-$TS.tar.gz" s3://YOUR-BUCKET/n8n/data/ --storage-class STANDARD_IA
# 4. Safisha za wiki 2 zilizopita
find "$DEST" -type f -mtime +14 -delete
```

**Test ya restore kila mwezi 1** (bila hii backup ni hadithi):
```bash
docker exec -i n8n-postgres-1 pg_restore -U n8n -d n8n_test --clean --if-exists < n8n-XXXX.dump
```

**Monitoring (bila gharama):**
- UptimeRobot au BetterStack → ping `https://mwijay.duckdns.org/healthz` kila dakika 5 → SMS/Telegram.
- Cron ya kila saa: `df -h / | awk 'NR==2 && $5+0>85 {print "DISK " $5}'` → Telegram.
- **Error Workflow** (n8n Settings → Error Workflow) → Telegram: jina la workflow, node, error, execution URL. Hii ni muhimu kuliko kila kitu kingine kwenye ukurasa huu.
- Kila mteja: workflow moja ya "heartbeat" (Schedule kila siku 08:00 → HTTP ping yako) — ikikosa, unajua.

---

## 7. Version pinning & upgrades (usianze "latest")

- Badilisha `n8nio/n8n:latest` → version mahususi (mf. `:1.9x.x`). `latest` ikibadilika usiku mmoja, workflows zinaweza kuvunjika asubuhi.
- Kabla ya upgrade: `docker compose exec n8n n8n export:workflow --all --output=/files/export-$(date +%F).json` + backup ya DB.
- Upgrade kwa mwezi mmoja nyuma ya release, si siku ya release.
- Andika `CHANGELOG-altraware.md` kwenye repo: tarehe, version, kilichobadilika.

---

## 8. Security checklist (mteja wa kwanza atauliza)

- [ ] HTTPS pekee; HTTP inaelekezwa kwa HTTPS (Caddy inafanya).
- [ ] Port 5678 haiko wazi kwa internet (`127.0.0.1` bind).
- [ ] MFA/2FA kwenye akaunti ya n8n owner (n8n ina MFA kwenye settings) + password manager.
- [ ] Hakuna mteja ana login ya n8n (license + usalama) — wanapata dashboard/Sheets/Telegram pekee.
- [ ] Credentials kwa mteja tofauti; majina: `CLIENT_JINA - WhatsApp`.
- [ ] `N8N_BLOCK_ENV_ACCESS_IN_NODE=true` (Code node isisome secrets).
- [ ] API keys za LLM zina **spending limit** kwa provider (OpenRouter: limit $20/mo; OpenAI: hard limit).
- [ ] Data ya mteja: hakuna kutuma PII kwa model bila sababu; mchakato wa kufuta data baada ya mkataba.
- [ ] Mkataba wa data (DPA) + PDPA 2022 mention; weka kwenye `09-templates-scripts.md`.
- [ ] Logs za error hazina namba za simu/bei za mteja (mask kwenye Code node).

---

## 9. Wakati wa kupanda (scaling triggers — usikisie)

| Dalili | Hatua | Gharama/mo (takriban) |
|---|---|---|
| RAM > 85% kwa siku 3 mfululizo | Panda `t4g.medium` → `t4g.large` (8GB) | $12–15 → $24–30 |
| DB > 2GB / queries polepole | Hamisha Postgres kwa RDS `db.t4g.micro` | +$15–20 |
| CPU > 80% kwa dakika 30/siku | Queue mode: main + worker 1 + Redis | +$12–18 |
| Wateja 8+ / executions 100k/mo | Node 2: worker pekee, filesystem → S3 binary | +$20–30 |
| Unahitaji local LLM | **Usifanye kwenye node hii.** Tumia API, au GPU node tofauti (spot) | $0.3–1.5/hr |

**Kanuni ya dhahabu:** ramani ya ukuaji ni **RAM → DB → workers → GPU**, kwa mpangilio huo. Kuruka hatua = outage.

---

## 10. Hatua ya kwanza leo (dakika 30)

1. Run amri za §2, andika matokeo kwenye `docs/notes/aws-baseline.md`.
2. Ongeza `NODE_OPTIONS`, `N8N_DEFAULT_BINARY_DATA_MODE`, pruning env vars — restart.
3. Weka Error Workflow → Telegram yako.
4. Weka backup script + cron; fanya restore test moja.
5. Weka UptimeRobot kwa `/healthz`.
6. Pin version ya n8n.

Baada ya haya, unaweza kuahidi SLA ya "uptime 99%" kwa mteja bila kusema uongo.
