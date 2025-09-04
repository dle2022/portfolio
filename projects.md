---
layout: default
title: Projects
---

# Projects & Case Studies

Each entry includes **context**, **architecture**, **steps**, **code/commands**, **observability**, and **lessons learned**.

## VMware Lab on a Budget {#vmware-lab}
![VMware Lab](/assets/images/vmware-lab.svg)

**Context.** Build a robust homelab to mirror enterprise clusters (vCenter, ESXi, vSAN, HA/DRS).  
**Architecture.** Nested ESXi on a single host; vCenter VM; shared datastore; VLANs.  
**Steps.**
1. Enable VHV; prepare boot ISO; deploy ESXi VMs.
2. Install vCenter; add ESXi; create clusters; enable HA/DRS.
3. Set up shared storage; snapshots; backups.
**Observability.** Telegraf→Prometheus→Grafana dashboards.  
**Lessons.** Hardware limits; licensing; snapshot hygiene.

---

## POS ↔ Odoo Integration {#pos-odoo}
![POS Integration]({{ site.baseurl }}/assets/images/pos-odoo.svg)

**Context.** Sync orders/payments, reflect `modifiers` and `variations` on receipts.  
**Architecture.** PHP/Laravel admin ↔ Square/Clover APIs ↔ Odoo.  
**Steps.**
1. Map products/modifier groups; sync cron/webhooks.
2. Implement `getModifierIdByLabel()`; auto‑create missing modifiers.
3. Ensure receipts show add‑ons + variations.
**Observability.** Odoo logs + POS dashboard events.  
**Lessons.** Data model mapping and idempotency are key.

---

[Download my resume]({{ site.baseurl }}/assets/Duong-Le-Resume.pdf)
---
**Lessons.** Cardinality/label discipline; dashboards per audience.
## Self‑Hosted AI/RAG Stack {#rag-stack}
![RAG Stack](/assets/images/rag-stack.svg)

**Context.** Ingest docs/emails; answer with retrieval; log Q&A.  
**Architecture.** Docker Compose: Ollama, Qdrant, n8n, Postgres, Open WebUI.  
**Steps.**
1. Ingest `.pdf/.docx/.txt`; embed to Qdrant.
2. n8n flows trigger summarization/classification and Odoo CRM creation.
3. `/reindex` endpoint refreshes vectors.
**Observability.** Qdrant stats + n8n logs.  
**Lessons.** Prompt discipline; metadata; retry/backoff.

---

## n8n + Cloudflare Zero Trust {#n8n-cloudflare}
**Context.** Protect webhooks and long jobs.  
**Architecture.** Cloudflare Tunnel → n8n with queue mode.  
**Steps.**
1. Set cache rules to bypass API/webhooks.
2. Enable WebSockets; test with curl.
3. Use signed webhook secrets.
**Lessons.** Edge cache and security level matter.

---

## Full‑stack Observability {#observability}
**Context.** Unify metrics across ESXi, NAS, firewall, Wi‑Fi.  
**Architecture.** Telegraf → Prometheus → Grafana.  
**Steps.**
1. Telegraf vsphere input; Prometheus scrape; Grafana dashboards.
2. Alerting for CPU/RAM/disk; synthetic pings.
**Lessons.** Cardinality/label discipline; dashboards per audience.
