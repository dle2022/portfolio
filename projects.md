---
layout: default
title: Projects
---

# Projects & Case Studies

Each entry includes **context**, **architecture**, **steps**, **commands/config**, **observability**, and **lessons learned**.

---

## VMware Lab on a Budget {#vmware-lab}
![VMware Lab]({{ site.baseurl }}/assets/images/vmware-lab.svg)

**Context.** Build a robust homelab that mirrors enterprise clusters: vCenter, ESXi, vSAN-style shared storage, HA/DRS.  
**Architecture.** Nested ESXi on a single physical host; vCenter VM; shared datastore (iSCSI/NFS); VLANs & trunking; pfSense/OpnSense edge.

**Steps.**
1. **Enable VHV** (Virtualized Hardware) on the bare-metal hypervisor; create 2–3 nested ESXi VMs (16–32GB RAM each).
2. **Deploy vCenter**; add ESXi; create a **Cluster**; enable **HA/DRS**, anti-affinity for infra VMs.
3. **Shared storage:** expose an NFS/iSCSI target; create a datastore; place infra VMs and test vMotion.
4. **Backups:** Veeam Community + application-consistent snapshots; weekly full + daily incremental.
5. **Networking:** create Mgmt/Storage/vMotion/App VLANs; trunk to a lab switch; set jumbo frames on storage path.

**Commands/Config (snippets).**
- ESXi shell: `esxcli network vswitch standard mtu set -m 9000 -v vSwitch1`  
- vSphere CLI label host/vmk: `esxcli network ip interface set -i vmk1 -M 9000`

**Observability.** Telegraf → Prometheus → Grafana: host CPU/RAM/disk, VM latency, datastore IOPS, packet drops.  
**Lessons.** Nested labs surface real-world constraints (NUMA, storage latency). Keep snapshots short-lived; document vMotion/DR drills.

---

## POS ↔ Odoo Integration (Square & Clover) {#pos-odoo}
![POS Integration]({{ site.baseurl }}/assets/images/pos-odoo.svg)

**Context.** Sync items/orders/payments and ensure **modifiers** and **variations** appear correctly on POS receipts and in Odoo.  
**Architecture.** PHP/Laravel admin ↔ Square/Clover APIs ↔ Odoo; webhooks + scheduled sync; MySQL/PG for mapping.

**Steps.**
1. **Catalog mapping:** products ↔ variations; **modifier groups** ↔ add-ons; SKU & external IDs normalized.
2. Implement `getModifierIdByLabel()`; **auto-create** missing modifiers; persist IDs to avoid duplicates.
3. **Orders:** normalize line-items; attach modifiers; compute taxes/discounts; idempotent upserts into Odoo.
4. **Receipts:** confirm POS dashboards render both **add-ons** and **variations** as intended.

**Commands/Config.**
- Laravel scheduler: `* * * * * php /var/www/artisan schedule:run`  
- Webhook signatures: HMAC (Square) + app secrets (Clover); reject mismatches.

**Observability.** Odoo logs, POS dashboards, and a small **reconciliation table** (missing IDs, price deltas).  
**Lessons.** Treat catalog as a graph; strict **idempotency keys** for webhooks; backfill jobs for retro data.

---

## Self-Hosted AI/RAG Stack {#rag-stack}
![RAG Stack]({{ site.baseurl }}/assets/images/rag-stack.svg)

**Context.** Ingest docs/emails and answer with retrieval; store Q&A for learning.  
**Architecture.** Docker Compose: **Ollama**, **Qdrant**, **n8n**, **Postgres**, **Open WebUI**; simple FastAPI for `/reindex`.

**Steps.**
1. Ingest `.pdf/.docx/.txt`; chunk + embed → **Qdrant**.
2. n8n flows trigger **summarization/classification**; optionally create Odoo CRM opportunities.
3. `/reindex` endpoint kicks an ingest worker to refresh vectors on demand.

**Commands/Config (compose excerpt).**
```yaml
services:
  qdrant:
    image: qdrant/qdrant:latest
    volumes: [ "./qdrant:/qdrant/storage" ]
  ollama:
    image: ollama/ollama:latest
    volumes: [ "ollama:/root/.ollama" ]
  n8n:
    image: n8nio/n8n:latest
    environment:
      - N8N_SECURE_COOKIE=true
      - N8N_ENCRYPTION_KEY=${N8N_KEY}
