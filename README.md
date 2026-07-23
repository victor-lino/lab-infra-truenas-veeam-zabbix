# 🏢 Enterprise Infrastructure Laboratory: Storage, Backup, and Observability

This repository documents the implementation of a practical laboratory simulating a high-availability corporate environment. The project covers centralized storage configuration, data protection policies, active monitoring, and infrastructure integration.

## 🛠️ Technologies and Architecture

* **Storage:** TrueNAS Core (ZFS, iSCSI, SMB, Snapshots, Cloud Sync)
* **Backup and Protection:** Veeam Backup & Replication (Agent, Block-level Backup, Backup Copy)
* **Observability:** Zabbix Server (SNMP/Agents) and Grafana
* **Network Infrastructure:** pfSense (Firewall) and Windows Server (Active Directory / File Server)

> **Solution Architecture Diagram:**
> 

---

## 1. ZFS Storage and Protection (TrueNAS Core)

TrueNAS acts as the storage core of the project. ZFS-based pools were provisioned and segmented into organizational **Datasets** (`Storage-Financeiro`, `Storage-TI`), in addition to creating a dedicated **Zvol (Block Storage)** for iSCSI provisioning.

To guarantee native data protection against accidental deletions or ransomware, **Periodic Snapshot** tasks were implemented.

> **Datasets and Zvols Structure:**
> 
> **Snapshot Routine:**
> 

---

## 2. Connectivity and File Sharing

To distribute storage across the network, two protocols were used simultaneously:

* **iSCSI (Block Level):** The TrueNAS Zvol was mapped directly to the Backup Server via the Windows iSCSI Initiator, formatted in NTFS for high-speed writing.
* **SMB/CIFS (File Level):** Datasets were shared across the network so that client machines and servers could map native network drives.

> **Established iSCSI Connection:**
> 
> **Local iSCSI Unit (Disk Management):**
> 
> **Mapped Drives on Windows Client (This PC):**
> 

---

## 3. Backup Management and Retention (Veeam)

The backup strategy was designed to ensure storage efficiency and resiliency. Multiple repositories were configured in **Veeam Backup & Replication**, including high-performance iSCSI storage (Tier 1) and a high-capacity External HDD (Tier 2 / Backup Copy).

During execution, the job scope was optimized (migrating from *Entire Computer* to *Volume-level backup*), ensuring that data fit perfectly within the allocated LUN without exhausting storage capacity.

> **Infrastructure Repositories:**
> 
> **Successfully Processed Backup Job:**
> 

---

## 4. Disaster Recovery and Cloud (Cloud Sync)

Following the 3-2-1 backup rule, an off-site external resiliency layer was configured natively in the storage. Critical data is synchronized daily (PULL/COPY mode) to **Google Drive**, ensuring recovery in the event of a total physical failure of the laboratory.

> **Cloud Synchronization (Google Drive):**
> 

---

## 5. Observability, Monitoring, and Alerts

To ensure continuous service availability, the entire ecosystem is monitored 24/7.
**Zabbix** handles metric collection via SNMP (for TrueNAS and pfSense) and via Zabbix Agent (for Windows and Linux servers).

Triggers were configured to monitor CPU utilization, swap, ICMP packet loss, and disk space. **Grafana** consumes the Zabbix API to deliver real-time visualizations, and automations (Action Rules) dispatch emails to the team in case of incidents.

> **Monitored Hosts Inventory (Zabbix):**
> 
> **Real-Time Dashboards (Grafana):**
> 
> **Email Notification Validation (SMTP):**
> 

---

*Project developed and documented with a focus on real corporate architecture, advanced network/storage troubleshooting, and IT infrastructure best practices.*
