# 🗄️ 01 - Armazenamento e Proteção (TrueNAS Core)

## 🎯 Objetivo
Estruturar um servidor de armazenamento centralizado de alta performance e resiliência, utilizando o sistema de arquivos ZFS para prover tanto armazenamento em bloco (iSCSI) quanto compartilhamento de arquivos em rede (SMB), com políticas nativas de proteção de dados.

## ⚙️ Configurações Realizadas

### 1. Estruturação de Pools e Datasets (File Level)
Foi provisionado o pool principal `Storage` e segmentado de forma lógica para atender diferentes demandas departamentais e de infraestrutura:
*   **Datasets Organizacionais:** Criados os datasets `Storage-Financeiro` e `Storage-TI` para compartilhamento via protocolo SMB/CIFS, permitindo o mapeamento direto no Windows Server e em estações clientes.
*   **Dataset de Suporte:** Criado o `Veeam-Repo` como área de staging e suporte adicional.

![Visão Geral dos Datasets](../images/truenas_core_10.png)

### 2. Provisionamento de Block Storage (iSCSI)
Para suportar as operações de backup de forma isolada e com alta taxa de I/O, foi configurado um armazenamento em bloco:
*   **Zvol:** Criado o volume `Veeam-Disk` com alocação de ~15 GB.
*   **Target iSCSI:** Configurado o target `iqn.2005-10.org.freenas.ctl:veeam-target`.
*   **Iniciador Windows:** A máquina servidora conectou-se com sucesso ao target, formatando o LUN em NTFS e disponibilizando-o como unidade `V:\`.

![Iniciador iSCSI Conectado](../images/truenas_core_11.png)
![Unidades Mapeadas no Windows](../images/truenas_core_19.jpg)

### 3. Proteção de Dados Nativa (Snapshots)
Para garantir proteção contra falhas lógicas ou ransomware, o ZFS foi configurado com rotinas de snapshots automatizadas no dataset principal.
*   **Nomenclatura:** `auto-%Y-%m-%d_%H-%M`
*   **Frequência:** Execução periódica diária configurada para o pool `Storage/Data`.

![Rotina de Snapshots](../images/truenas_core_2.png)