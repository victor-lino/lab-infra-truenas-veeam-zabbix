# 📊 03 - Observabilidade e Alertas (Zabbix & Grafana)

## 🎯 Objetivo
Garantir o monitoramento proativo de disponibilidade, performance e capacidade de toda a infraestrutura de rede, servidores e armazenamento, provendo visualizações ricas e alertas automatizados em tempo real.

## ⚙️ Configurações Realizadas

### 1. Monitoramento Distribuído (Zabbix Server)
O ecossistema completo foi inserido na malha de monitoramento do Zabbix, combinando protocolos adequados para cada tecnologia:
*   **TrueNAS e pfSense:** Monitorados via protocolo **SNMP** (porta 161) com templates oficiais (TrueNAS-Core e Network Interfaces), rastreando tráfego, uso de pool, ICMP loss e Load Average.
*   **Controladores de Domínio e Linux:** Monitorados via **Zabbix Agent** (porta 10050).

![Inventário de Hosts no Zabbix](../images/truenas_core_6.png)

### 2. Automação de Alertas (Action Rules e SMTP)
Foram configuradas lógicas de disparo (*Triggers*) para anomalias como *High CPU utilization*, *High memory utilization* e uso elevado de Swap. 
* O Zabbix conta com a Action Rule `Alertas Automáticos - Produção` e integrações SMTP que direcionam notificações de severidade *Warning* ou superior diretamente para o e-mail da equipe de administração/NOC.

![Validação de Alerta por E-mail](../images/truenas_core_5.png)

### 3. Painéis de Visualização (Grafana)
Para facilitar a resposta a incidentes e o acompanhamento gerencial, o Grafana foi conectado à API do Zabbix.
*   Foi desenvolvido o dashboard interativo **TrueNAS Monitor**, centralizando os principais medidores (*Gauges*) de CPU e Memória Disponível, além da plotagem gráfica contínua do Tráfego de Rede (Bits recebidos vs. enviados).

![Dashboard do Grafana](../images/truenas_core_1.png)