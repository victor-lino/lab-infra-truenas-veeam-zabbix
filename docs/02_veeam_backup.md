# 🛡️ 02 - Gestão de Backup e Cloud Sync (Veeam)

## 🎯 Objetivo
Implementar uma arquitetura de proteção de dados escalável para infraestrutura mista (Windows e Linux), utilizando múltiplos repositórios locais e estabelecendo resiliência off-site em conformidade com as melhores práticas (Regra 3-2-1).

## ⚙️ Configurações Realizadas

### 1. Configuração de Repositórios (Scale-out Híbrido)
A infraestrutura do Veeam Backup & Replication foi estruturada com dois repositórios de armazenamento primários:
*   **Tier 1 (Performance):** `Repo-TrueNAS-iSCSI` mapeado no volume `V:\`, recebendo os dados em nível de bloco com alta velocidade.
*   **Tier 2 (Capacidade):** `External HDD Repository` de 1TB para retenção de longo prazo e cópias secundárias (Backup Copy Jobs).

![Repositórios do Veeam](../images/truenas_core_7.png)

### 2. Rotinas de Backup (Jobs)
Foram estabelecidos jobs de backup com o Veeam Agent (Windows e Linux) para servidores críticos do domínio (DC01, DC02), servidores de aplicação e Firewall. 
* O job `Job Veeam Disk (V:)` opera em modo *Volume-level backup*, garantindo que os pacotes de dados processados não excedam o limite físico do LUN alocado (15 GB), otimizando o I/O da rede.

![Execução do Job de Backup](../images/truenas_core_15.jpg)

### 3. Disaster Recovery (Cloud Sync)
Como camada adicional de segurança, o TrueNAS foi integrado diretamente à nuvem para contornar perdas físicas totais do ambiente.
*   **Serviço:** Google Drive.
*   **Direção:** Rotina em modo PULL/COPY transferindo o conteúdo crítico do diretório `/mnt/Storage`.
*   **Status:** Sincronização diária validada com sucesso.

![Integração Cloud Sync](../images/truenas_core_13.png)