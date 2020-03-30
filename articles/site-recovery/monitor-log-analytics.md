---
title: Monitore a recuperação do site do Azure com logs de monitor esquetadores do Azure
description: Saiba como monitorar a recuperação do site do Azure com o Log Analytics do Monitor Do Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133423"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorar Site Recovery com os logs do Azure Monitor

Este artigo descreve como monitorar máquinas replicadas pelo Azure [Site Recovery,](site-recovery-overview.md)usando [o Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md)e [o Log Analytics](../azure-monitor/log-query/log-query-overview.md).

O Azure Monitor Logs fornece uma plataforma de dados de log que coleta registros de atividades e diagnósticos, juntamente com outros dados de monitoramento. No Azure Monitor Logs, você usa o Log Analytics para escrever e testar consultas de log e para analisar interativamente os dados de log. Você pode visualizar e consultar os resultados do registro e configurar alertas para tomar ações com base em dados monitorados.

Para recuperação de site, você pode acessar logs do Monitor do Azure para ajudá-lo a fazer o seguinte:

- **Monitore a saúde e o estado de recuperação do site**. Por exemplo, você pode monitorar a saúde da replicação, testar o status de failover, eventos de recuperação do site, rpos (recovery point objectives, objetivos de ponto de recuperação) para máquinas protegidas e taxas de alteração de disco/dados.
- **Configure alertas para recuperação de site**. Por exemplo, você pode configurar alertas para a saúde da máquina, o status de failover do teste ou o status do trabalho de recuperação do site.

O uso do Azure Monitor Logs com recuperação de site é suportado para replicação **do Azure para o Azure** e **o VMware VM/servidor físico para** a replicação do Azure.

> [!NOTE]
> Para obter os registros de dados de churn e os registros de taxa de upload para VMware e máquinas físicas, você precisa instalar um agente de monitoramento microsoft no Process Server. Este agente envia os registros das máquinas de replicação para o espaço de trabalho. Este recurso está disponível apenas para a versão do agente de mobilidade 9.30 em diante.

## <a name="before-you-start"></a>Antes de começar

Você precisa do seguinte:

- Pelo menos uma máquina protegida em um cofre dos Serviços de Recuperação.
- Um espaço de trabalho do Log Analytics para armazenar logs de recuperação de site. [Aprenda a](../azure-monitor/learn/quick-create-workspace.md) montar um espaço de trabalho.
- Uma compreensão básica de como escrever, executar e analisar consultas de log no Log Analytics. [Saiba mais](../azure-monitor/log-query/get-started-portal.md).

Recomendamos que você revise [perguntas comuns](monitoring-common-questions.md) de monitoramento antes de começar.

## <a name="configure-site-recovery-to-send-logs"></a>Configurar a recuperação do site para enviar logs

1. No cofre, clique em **Configurações** > de diagnóstico**Adicionar configuração de diagnóstico**.

    ![Selecione o registro de diagnóstico](./media/monitoring-log-analytics/add-diagnostic.png)

2. Em **Configurações de diagnóstico,** especifique um nome e marque a caixa **Enviar para Log Analytics**.
3. Selecione a assinatura do Azure Monitor Logs e o espaço de trabalho do Log Analytics.
4. Selecione **Azure Diagnostics** no alternador.
5. Na lista de logs, selecione todos os logs com o prefixo **AzureSiteRecovery**. Em seguida, clique em **OK**.

    ![Selecione o workspace](./media/monitoring-log-analytics/select-workspace.png)

Os logs de recuperação do site começam a ser alimentados em uma tabela **(AzureDiagnostics)** no espaço de trabalho selecionado.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Configure o agente de monitoramento da Microsoft no Process Server para enviar logs de taxa de churn e upload

Você pode capturar as informações da taxa de rotatividade de dados e as informações da taxa de upload de dados de origem para suas máquinas VMware/physical no local. Para habilitar isso, é necessário que um agente de monitoramento microsoft seja instalado no Process Server.

1. Vá para o espaço de trabalho do Log Analytics e clique em **Configurações Avançadas**.
2. Clique na página **Fontes Conectadas** e selecione ainda **os servidores do Windows**.
3. Baixe o Agente Windows (64 bits) no Servidor de Processo. 
4. [Obtenha o ID e a chave do espaço de trabalho](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Configurar agente para usar o TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Complete a instalação do agente](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) fornecendo o ID e a chave do espaço de trabalho obtidos.
7. Uma vez que a instalação esteja concluída, vá para o espaço de trabalho do Log Analytics e clique em **Configurações Avançadas**. Vá para a página **Dados** e clique ainda em **Contadores de Desempenho do Windows**. 
8. Clique em **'+'** para adicionar os dois contadores a seguir com intervalo de amostra de 300 segundos:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

Os dados da taxa de churn e upload começarão a se alimentar no espaço de trabalho.


## <a name="query-the-logs---examples"></a>Consultar os logs - exemplos

Você recupera dados de logs usando consultas de log escritas com o [idioma de consulta Kusto](../azure-monitor/log-query/get-started-queries.md). Esta seção fornece alguns exemplos de consultas comuns que você pode usar para monitoramento de recuperação de site.

> [!NOTE]
> Alguns dos exemplos usam **replicationProviderName_s** definido como **A2A**. Isso recupera as VMs do Azure que são replicadas para uma região Secundária do Azure usando a Recuperação do Site. Nestes exemplos, você pode substituir **o A2A** pelo **InMageAzureV2**, se quiser recuperar VMMs vMware ou servidores físicos que são replicados no Azure usando o Site Recovery.


### <a name="query-replication-health"></a>Saúde de replicação de consulta

Esta consulta plota um gráfico de tortas para a saúde de replicação atual de todas as VMs Azure protegidas, divididas em três estados: Normal, Aviso ou Crítico.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Versão do serviço de consulta Mobility

Esta consulta plota um gráfico de tortas para VMs do Azure replicado com o Site Recovery, dividido pela versão do agente de mobilidade que eles estão executando.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Consulta tempo rpo

Esta consulta plota um gráfico de barras de VMs do Azure replicado com o Site Recovery, dividido por RPO (Recovery Point Objective, objetivo de ponto de recuperação): menos de 15 minutos, entre 15-30 minutos, mais de 30 minutos.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![RPO de consulta](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Trabalhos de recuperação do site de consulta

Esta consulta recupera todos os trabalhos de Recuperação de Site (para todos os cenários de recuperação de desastres), desencadeados nas últimas 72 horas, e seu estado de conclusão.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Eventos de recuperação do site de consulta

Esta consulta recupera todos os eventos de recuperação de site (para todos os cenários de recuperação de desastres) levantados nas últimas 72 horas, juntamente com sua gravidade. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Estado de failover do teste de consulta (gráfico de tortas)

Esta consulta plota um gráfico de tortas para o status de failover de teste das VMs do Azure replicadas com recuperação de site.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Estado de failover do teste de consulta (tabela)

Esta consulta traça uma tabela para o status de failover de teste das VMs do Azure replicada com recuperação de site.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>RPO da máquina de consulta

Esta consulta traça um gráfico de tendência que rastreia o RPO de uma VM Azure específica (ContosoVM123) nas últimas 72 horas.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![RPO da máquina de consulta](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Consulta taxa de alteração de dados (churn) e taxa de upload para uma VM Azure

Esta consulta plota um gráfico de tendência para uma VM Azure específica (ContosoVM123), que representa a taxa de alteração de dados (Write Bytes por Segundo) e a taxa de upload de dados. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Alteração de dados de consulta](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Consulta taxa de alteração de dados (churn) e taxa de upload para um VMware ou máquina física

> [!Note]
> Certifique-se de configurar o agente de monitoramento no Servidor de Processo para buscar esses logs. Consulte [as etapas para configurar o agente de monitoramento](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Esta consulta plota um gráfico de tendência para um disco específico **de um** item replicado **win-9r7sfh9qlru**, que representa a taxa de alteração de dados (Write Bytes por Segundo) e a taxa de upload de dados. Você pode encontrar o nome do disco na lâmina **Discos** do item replicado no cofre de serviços de recuperação. Nome de instância a ser usado na consulta é o nome DNS da máquina seguido por _ e nome do disco como neste exemplo.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
O Process Server empurra esses dados a cada 5 minutos para o espaço de trabalho do Log Analytics. Esses pontos de dados representam a média calculada por 5 minutos.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Resumo de recuperação de desastres de consulta (Azure para Azure)

Esta consulta plota uma tabela de resumo para VMs Azure replicadas em uma região Azure secundária.  Ele mostra o nome da VM, o status de replicação e proteção, RPO, status de failover de teste, versão do agente de mobilidade, quaisquer erros de replicação ativa e o local de origem.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Resumo de recuperação de desastres de consulta (VMware/servidores físicos)

Esta consulta plota uma tabela de resumo para VMware VMs e servidores físicos replicados no Azure.  Ele mostra o nome da máquina, o status de replicação e proteção, RPO, status de failover de teste, versão do agente de mobilidade, quaisquer erros de replicação ativa e o servidor de processo relevante.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Configurar alertas - exemplos

Você pode configurar alertas de recuperação de site com base nos dados do Azure Monitor. [Saiba mais](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) sobre como configurar alertas de log. 

> [!NOTE]
> Alguns dos exemplos usam **replicationProviderName_s** definido como **A2A**. Isso define alertas para VMs azure que são replicados para uma região Azure secundária. Nestes exemplos, você pode substituir **o A2A** pelo **InMageAzureV2** se quiser definir alertas para VMMs VMware ou servidores físicos no local replicados no Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Múltiplas máquinas em um estado crítico

Configure um alerta se mais de 20 VMs Azure replicadas entrarem em estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Para o alerta, defina **o valor limite** para 20.

### <a name="single-machine-in-a-critical-state"></a>Máquina única em um estado crítico

Configure um alerta se uma VM Azure replicada específica entrar em estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, defina **o valor limite** para 1.

### <a name="multiple-machines-exceed-rpo"></a>Várias máquinas excedem o RPO

Configure um alerta se o RPO para mais de 20 VMs Azure exceder 30 minutos.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Para o alerta, defina **o valor limite** para 20.

### <a name="single-machine-exceeds-rpo"></a>Uma única máquina excede o RPO

Configure um alerta se o RPO de uma única VM Azure exceder 30 minutos.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Para o alerta, defina **o valor limite** para 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Failover de teste para várias máquinas excede 90 dias

Configure um alerta se o último failover de teste bem sucedido foi de mais de 90 dias, para mais de 20 VMs. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, defina **o valor limite** para 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Failover de teste para máquina única excede 90 dias

Configure um alerta se o último failover de teste bem sucedido para uma VM específica foi há mais de 90 dias.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, defina **o valor limite** para 1.

### <a name="site-recovery-job-fails"></a>O trabalho de recuperação do site falha

Configure um alerta se um trabalho de recuperação de site (neste caso o trabalho Reprotect) falhar para qualquer cenário de Recuperação de Site, durante o último dia. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Para o alerta, defina o **valor de Limite** para 1 e O **Período** para 1440 minutos, para verificar falhas no último dia.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre](site-recovery-monitor-and-troubleshoot.md) o monitoramento de recuperação de sites embutidos.
