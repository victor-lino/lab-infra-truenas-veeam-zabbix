# 🏢 Laboratório de Infraestrutura Corporativa: Armazenamento, Backup e Observabilidade

Este repositório documenta a implementação de um laboratório prático simulando um ambiente corporativo de alta disponibilidade. O projeto abrange a configuração centralizada de armazenamento, políticas de proteção de dados, monitoramento ativo e integração de infraestrutura.

## 🛠️ Tecnologias e Arquitetura

*   **Storage:** TrueNAS Core (ZFS, iSCSI, SMB, Snapshots, Cloud Sync)
*   **Backup e Proteção:** Veeam Backup & Replication (Agent, Block-level Backup, Backup Copy)
*   **Observabilidade:** Zabbix Server (SNMP/Agents) e Grafana
*   **Infraestrutura de Rede:** pfSense (Firewall) e Windows Server (Active Directory / File Server)

---

## 1. Armazenamento e Proteção ZFS (TrueNAS Core)

O TrueNAS atua como o núcleo de armazenamento do projeto. Foram provisionados *pools* baseados em ZFS e segmentados em **Datasets** organizacionais (`Storage-Financeiro`, `Storage-TI`), além da criação de um **Zvol (Block Storage)** dedicado para provisionamento iSCSI.

Para garantir a proteção nativa dos dados contra exclusões acidentais ou ransomware, foram implementadas tarefas de **Snapshots Periódicos**.

> **Estrutura de Datasets e Zvols:**
> ![Datasets no TrueNAS](imagens/truenas_core_10.png)
> 
> **Rotina de Snapshots:**
> ![Snapshots Automatizados](imagens/truenas_core_2.png)

---

## 2. Conectividade e Compartilhamento de Arquivos

Para distribuir o armazenamento pela rede, dois protocolos foram utilizados simultaneamente:
*   **iSCSI (Block Level):** O Zvol do TrueNAS foi mapeado diretamente para o servidor de Backup via Iniciador iSCSI no Windows, formatado em NTFS para gravação em alta velocidade.
*   **SMB/CIFS (File Level):** Os Datasets foram compartilhados na rede para que as máquinas clientes e servidores pudessem mapear unidades de rede nativas.

> **Conexão iSCSI Estabelecida:**
> ![iSCSI Status](imagens/truenas_core_11.png)
> 
> **Unidades Mapeadas no Client Windows (This PC):**
> ![Unidades Windows](imagens/truenas_core_19.jpg)

---

## 3. Gestão de Backups e Retenção (Veeam)

A estratégia de backup foi desenhada para garantir eficiência de armazenamento e resiliência. Foram configurados múltiplos repositórios no **Veeam Backup & Replication**, incluindo o armazenamento iSCSI de alta performance (Tier 1) e um HD Externo de grande capacidade (Tier 2 / Backup Copy).

Durante a execução, o escopo dos jobs foi otimizado (migrando de *Entire Computer* para *Volume-level backup*), garantindo que os dados coubessem perfeitamente no LUN alocado sem esgotar o armazenamento do Storage.

> **Repositórios de Infraestrutura:**
> ![Repositórios Veeam](imagens/truenas_core_7.png)
> 
> **Job de Backup Processado com Sucesso:**
> ![Veeam Job](imagens/truenas_core_15.jpg)

---

## 4. Disaster Recovery e Nuvem (Cloud Sync)

Seguindo a regra de backup 3-2-1, uma camada de resiliência externa (*off-site*) foi configurada nativamente no storage. Os dados críticos são sincronizados diariamente (modo PULL/COPY) para o **Google Drive**, garantindo recuperação em caso de falha física total do laboratório.

> **Sincronização em Nuvem (Google Drive):**
> ![Cloud Sync](imagens/truenas_core_13.png)

---

## 5. Observabilidade, Monitoramento e Alertas

Para garantir a disponibilidade contínua dos serviços, todo o ecossistema é monitorado 24/7.
O **Zabbix** faz a coleta de métricas via SNMP (para o TrueNAS e pfSense) e via Zabbix Agent (para os servidores Windows e Linux). 

Triggers foram configuradas para monitorar uso de CPU, swap, perda de pacotes ICMP e espaço em disco. O **Grafana** consome a API do Zabbix para entregar visualizações em tempo real, e automações (Action Rules) disparam e-mails para a equipe em caso de incidentes.

> **Inventário de Hosts Monitorados (Zabbix):**
> ![Hosts Zabbix](imagens/truenas_core_6.png)
> 
> **Dashboards de Tempo Real (Grafana):**
> ![Dashboard Grafana](imagens/truenas_core_1.png)
> 
> **Validação de Notificação por E-mail (SMTP):**
> ![Alerta de Email](imagens/truenas_core_5.png)

---
*Projeto desenvolvido e documentado com foco em arquitetura corporativa real, *troubleshooting* avançado de redes/armazenamento e melhores práticas de infraestrutura de TI.*
