---
layout: default
title: Projects
---

# Projects & Case Studies

Welcome! This page collects a mix of infrastructure, security systems, POS/ERP integration, AI/RAG, and observability projects.  
Each entry includes **Context**, **Architecture**, **Prereqs**, **Steps**, **Code/Config**, **Observability**, and **Lessons Learned**.

> Tip: Use the table of contents to jump around. Most sections end with “Next” actions to make the work repeatable and improvable.

---

## Table of Contents

- [VMware Lab on a Budget](#vmware-lab)
- [Windows Server & Active Directory Modernization](#win-ad-modernization)
- [Network Security & Infrastructure Design (Cisco)](#network-cisco)
- [Municipal VMS Modernization (Milestone XProtect)](#milestone)
- [Access Control Upgrade (LenelS2 OnGuard)](#lenel)
- [Access Control Standardization (AMAG Symmetry)](#amag)
- [Campus Video Analytics (Avigilon ACC) — Media HQ (Anonymized)](#avigilon)
- [POS ↔ Odoo Integration (Square & Clover)](#pos-odoo)
- [Self-Hosted AI/RAG Stack](#rag-stack)
- [n8n + Cloudflare Zero Trust](#n8n-cloudflare)
- [Full-Stack Observability](#observability)
- [Cloud: AWS Landing Zone & Migration](#aws-landing-zone)
- [Cloud: Azure Landing Zone & Hybrid AD/Intune](#azure-landing-zone)
- [Nextcloud + Google Drive Sync (On-Prem Files to Cloud)](#nextcloud-drive)
- [Keycloak SSO (Google Workspace, Odoo, n8n, Grafana)](#keycloak-sso)
- [Kitchen & Counter Printing via Star CloudPRNT](#cloudprnt)
- [Disaster Recovery Runbook & Drill](#dr-runbook)
- [Docs & Runbooks with GitHub Pages CI/CD](#docs-cicd)
- [Appendix: Image Placeholders](#appendix-images)
- [KPIs & Metrics Glossary](#kpis)
- [Changelog](#changelog)

---

## VMware Lab on a Budget {#vmware-lab}
![VMware Lab]({{ site.baseurl }}/assets/images/vmware-lab.svg)

**Context.** Build a robust homelab mirroring enterprise clusters: vCenter, ESXi, shared datastore, HA/DRS, backups.  
**Architecture.** Single physical host → Nested ESXi (2–3 nodes) → vCenter → shared storage (NFS/iSCSI). Segmented VLANs: MGMT, vMotion, Storage, App.  
**Prereqs.**
- CPU supporting VT-x/AMD-V and EPT/RVI, 64–128 GB RAM, SSD/NVMe tier
- Managed switch with VLAN trunking; home firewall (pfSense/OPNsense ok)
- ISOs: ESXi, vCenter; datastore target (TrueNAS/NFS/iSCSI)

**Steps.**
1. **Enable VHV** on bare-metal; create nested ESXi VMs (4 vCPU, 16–32 GB RAM, thin disks).
2. **Install ESXi** on each VM; configure vmk0 management; set NTP/DNS.
3. **Deploy vCenter**; add hosts; create **Cluster**; enable **HA/DRS**.
4. **Shared Storage**: present NFS/iSCSI from NAS; mount datastore; test vMotion/storage vMotion.
5. **Networking**: vSwitch0 for MGMT; vSwitch1 for vMotion; vSwitch2 for Storage (MTU 9000 if supported).
6. **Templates**: build golden Windows/Linux templates; cloud-init for Linux; SysPrep for Windows.
7. **Backups**: Veeam Community; weekly full + daily incremental; application-aware (SQL, AD).
8. **Docs**: keep runbooks for vCenter restore, host replacement, and vMotion DR drills.

**Code/Config (snippets).**
```bash
# ESXi: set MTU on a storage vSwitch
esxcli network vswitch standard mtu set -m 9000 -v vSwitch_Storage

# ESXi: label vmk for vMotion
esxcli network ip interface tag add -i vmk1 -t VMotion
