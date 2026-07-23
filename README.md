# 🏢 Corporate Infrastructure Laboratory: Storage, Backup, and Observability

![Project Cover](imagens/01_Capa_Projeto_Infra.png)

This repository documents the implementation of a practical lab simulating a high-availability corporate environment. The project covers centralized storage configuration, data protection policies, active monitoring, and infrastructure integration.

## 🛠️ Technologies and Architecture

*   **Storage:** TrueNAS Core (ZFS, iSCSI, SMB, Snapshots, Cloud Sync)
*   **Backup and Protection:** Veeam Backup & Replication (Agent, Block-level Backup, Backup Copy)
*   **Observability:** Zabbix Server (SNMP/Agents) and Grafana
*   **Network Infrastructure:** pfSense (Firewall) and Windows Server (Active Directory / File Server)

> **Solution Architecture Diagram:**
> ![Architecture and Topology](imagens/02_Arquitetura_Topologia.jpg)

---

## 1. ZFS Storage and Protection (TrueNAS Core)

TrueNAS acts as the storage core of the project. ZFS-based pools were provisioned and segmented into organizational **Datasets** (`Storage-Financeiro`, `Storage-TI`), along with the creation of a dedicated **Zvol (Block Storage)** for iSCSI provisioning.

To ensure native data protection against accidental deletion or ransomware, **Periodic Snapshot** tasks were implemented.

> **Datasets and Zvols Structure:**
> ![Datasets on TrueNAS](imagens/03_TrueNAS_Datasets_Zvol.png)
> 
> **Automated Snapshot Routine:**
> ![Automated Snapshots](imagens/09_TrueNAS_Snapshots_ZFS.png)

---

## 2. Connectivity and File Sharing

To distribute storage across the network, two protocols were used simultaneously:
*   **iSCSI (Block Level):** The TrueNAS Zvol was mapped directly to the Backup server via the Windows iSCSI Initiator, formatted in NTFS for high-speed writing.
*   **SMB/CIFS (File Level):** The Datasets were shared over the network so that client machines and servers could map native network drives.

> **Established iSCSI Connection:**
> ![iSCSI Status](imagens/04_Windows_iSCSI_Connect.png)
> 
> **Local iSCSI Drive (Disk Management):**
> ![Local iSCSI Drive](imagens/05_Windows_Unidade_iSCSI_Local.png)
> 
> **Mapped Drives on Windows Client (This PC):**
> ![Windows Drives](imagens/06_Windows_Unidades_SMB_Rede.jpg)

---

## 3. Backup Management and Retention (Veeam)

The backup strategy was designed to ensure storage efficiency and resilience. Multiple repositories were configured in **Veeam Backup & Replication**, including high-performance iSCSI storage (Tier 1) and a large-capacity External HDD (Tier 2 / Backup Copy).

During execution, the job scope was optimized (migrating from *Entire Computer* to *Volume-level backup*), ensuring the data fit perfectly within the allocated LUN without exhausting the Storage capacity.

> **Infrastructure Repositories:**
> ![Veeam Repositories](imagens/07_Veeam_Repositorios_Tiering.png)
> 
> **Successfully Processed Backup Job:**
> ![Veeam Job](imagens/08_Veeam_Job_Backup_Sucesso.jpg)

---

## 4. Disaster Recovery and Cloud (Cloud Sync)

Following the 3-2-1 backup rule, an external resilience layer (*off-site*) was natively configured on the storage. Critical data is synchronized daily (PULL/COPY mode) to **Google Drive**, ensuring recovery in case of a total physical failure of the lab.

> **Cloud Synchronization (Google Drive):**
> ![Cloud Sync](imagens/10_CloudSync_GoogleDrive.png)

---

## 5. Observability, Monitoring, and Alerts

To ensure continuous service availability, the entire ecosystem is monitored 24/7.
**Zabbix** collects metrics via SNMP (for TrueNAS and pfSense) and via Zabbix Agent (for Windows and Linux servers). 

Triggers were configured to monitor CPU usage, swap, ICMP packet loss, and disk space. **Grafana** consumes the Zabbix API to deliver real-time visualizations, and automations (Action Rules) trigger emails to the team in case of incidents.

> **Monitored Hosts Inventory (Zabbix):**
> ![Zabbix Hosts](imagens/11_Zabbix_Inventario_Hosts.png)
> 
> **Real-Time Dashboards (Grafana):**
> ![Grafana Dashboard](imagens/12_Grafana_Dashboard_NOC.png)
> 
> **Email Notification Validation (SMTP):**
> ![Email Alert](imagens/13_Zabbix_Alerta_Email_SMTP.png)

---
*Project developed and documented with a focus on real corporate architecture, advanced network/storage troubleshooting, and IT infrastructure best practices.*
