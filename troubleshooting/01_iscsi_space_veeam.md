# 🚨 Troubleshooting 01: Esgotamento de Capacidade no LUN iSCSI (Veeam)

## 📌 Cenário e Sintoma do Incidente
Durante a execução inicial da rotina de backup direcionada ao repositório iSCSI (`Repo-TrueNAS-iSCSI`), o job falhou com a seguinte mensagem de erro nos logs do Veeam Backup & Replication:

> *"Error: Espaço insuficiente no disco Asynchronous request operation has failed... Backup location [V:\] is getting low on free disk space."*

O LUN provisionado via TrueNAS possuía **15 GB** (posteriormente expandido para 25 GB durante os testes), mas a operação era interrompida durante a fase de transferência ou finalização, estourando a capacidade da unidade.

## 🔍 Análise e Causa Raiz
Ao investigar as estatísticas avançadas do job falho, foi identificado que o volume de leitura (`Read`) estava ultrapassando **71 GB**. 

A causa raiz foi localizada nas configurações do *Backup Mode* do Veeam Agent:
* O job estava configurado incorretamente como **"Entire computer"** (Computador inteiro).
* Isso forçava o Veeam a tentar criar uma imagem de todo o sistema operacional Windows, excedendo em muito o limite de 15 GB projetado para o armazenamento em bloco deste laboratório.

## 🛠️ Plano de Ação e Resolução

Para adequar a rotina à arquitetura projetada sem estourar o limite físico do LUN, as seguintes ações foram tomadas:

1. **Alteração do Escopo do Backup:**
   * O job foi editado e o *Backup Mode* foi alterado de `Entire computer` para **`Volume level backup`**.
   * Nas configurações de objetos, o disco do Sistema Operacional (C:\) foi desmarcado, selecionando especificamente apenas o volume de dados planejado para o laboratório.
2. **Reestruturação de Metadados (Prevenção de Erros OIB):**
   * Para evitar conflitos de *Object Index Block* (`Oib already exists in point`) gerados pela falha anterior e mudanças de repositório, o cache do job foi limpo.
3. **Reexecução do Job:**
   * O serviço foi reiniciado e o job foi disparado manualmente (*Active Full*).

## ✅ Resultado e Validação
A correção foi efetiva. O job de backup respeitou a volumetria alocada, processando exatamente **14,8 GB** de dados e finalizando com status de sucesso no repositório `Repo-TrueNAS-iSCSI`, confirmando a estabilidade da comunicação iSCSI com o TrueNAS.