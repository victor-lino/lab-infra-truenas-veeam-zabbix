graph LR
    subgraph Monitoramento
        Zabbix[Zabbix Server] --- Grafana[Dashboards]
    end

    subgraph Infraestrutura
        pfSense[Firewall pfSense]
        DCs[Windows Server DCs]
    end

    subgraph Storage e Backup
        TrueNAS[(TrueNAS Core <br> iSCSI & SMB)]
        Veeam[Veeam Backup]
        HD[(HD Externo)]
    end

    Cloud((Google Drive))

    %% Conexões de Backup
    Veeam -- 1. Lê os dados --> DCs
    Veeam -- 2. Grava via iSCSI --> TrueNAS
    Veeam -- 3. Backup Copy --> HD
    
    %% Conexões de Nuvem
    TrueNAS -- Cloud Sync --> Cloud

    %% Conexões de Monitoramento (pontilhadas)
    Zabbix -. Coleta via SNMP .-> TrueNAS
    Zabbix -. Coleta via Agent .-> DCs
