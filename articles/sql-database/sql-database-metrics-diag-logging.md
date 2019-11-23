---
title: Log de diagnósticos e métricas
description: Learn how to enable diagnostics in Azure SQL Database to store information about resource utilization and query execution statistics.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/15/2019
ms.openlocfilehash: 27810f2ee1bc95c924003cd8a5944860df40db14
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420812"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de banco de dados SQL do Azure e o log de diagnóstico

In this topic, you will learn how to configure logging of diagnostics telemetry for Azure SQL Database through the Azure portal, PowerShell, Azure CLI, Azure Monitor REST API, and Azure Resource Manager template. These diagnostics can be used to gauge resource utilization and query execution statistics.

Bancos de dados individuais, bancos de dados em pool em pools elásticos e bancos de dados de instância em uma instância gerenciada podem transmitir logs de diagnóstico e métricas para facilitar o monitoramento de desempenho. Você pode configurar um banco de dados para transmitir o uso de recursos, trabalhos e sessões e conectividade para um dos seguintes recursos do Azure:

- **Análise de SQL do Azure**: para obter monitoramento inteligente de seus bancos de dados SQL do Azure que inclui relatórios de desempenho, alertas e recomendações de mitigação.
- **Hubs de Eventos do Azure**: para integrar telemetria de Banco de Dados SQL com suas soluções de monitoramento personalizadas ou pipelines ativos.
- **Armazenamento do Microsoft Azure**: para arquivar grandes quantidades de telemetria por uma fração do preço.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

Para obter mais informações sobre as categorias de log e métricas com suporte a vários serviços do Azure, veja:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral do log de diagnóstico do Azure](../azure-monitor/platform/resource-logs-overview.md)

Este artigo apresenta diretrizes para ajudá-lo a habilitar a telemetria de diagnóstico para bancos de dados SQL do Azure, pools elásticos e instâncias gerenciadas. Ele também pode ajudar você entender como configurar a Análise de SQL do Azure como uma ferramenta de monitoramento para exibir a telemetria de diagnóstico do banco de dados.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Habilitar o log de telemetria de diagnóstico

É possível habilitar e gerenciar as métricas e o log de diagnósticos usando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- Azure CLI
- API REST do Azure Monitor
- Modelo do Azure Resource Manager

When you enable metrics and diagnostics logging, you need to specify the Azure resource destination for collecting the diagnostics telemetry. As opções disponíveis incluem:

- Azure SQL Analytics
- Hubs de Eventos do Azure
- Armazenamento do Azure

Você pode provisionar um novo recurso do Azure ou selecionar um recurso existente. Depois de escolher um recurso usando a opção **Configurações de diagnóstico**, especifique quais dados coletar.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Supported diagnostic logging for Azure SQL databases, and instance databases

Habilitar o registro em log de métricas e diagnóstico em bancos de dados SQL – não estão habilitadas por padrão.

You can set up Azure SQL databases, and instance databases to collect the following diagnostics telemetry:

| Telemetria de monitoramento para bancos de dados | Suporte a banco de dados individual e banco de dados em pool | Instance database support |
| :------------------- | ----- | ----- |
| [Basic metrics](#basic-metrics): Contains DTU/CPU percentage, DTU/CPU limit, physical data read percentage, log write percentage, Successful/Failed/Blocked by firewall connections, sessions percentage, workers percentage, storage, storage percentage, and XTP storage percentage. | SIM | Não |
| [Instance and App Advanced](#advanced-metrics):  Contains tempdb system database data and log file size and tempdb percent log file used. | SIM | Não |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Contains information about the query runtime statistics such as CPU usage and query duration statistics. | SIM | SIM |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Contains information about the query wait statistics (what your queries waited on) such are CPU, LOG, and LOCKING. | SIM | SIM |
| [Errors](#errors-dataset): Contains information about SQL errors on a database. | SIM | SIM |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): contém informações sobre a quantidade de tempo de espera de um banco de dados em diferentes tipos de espera. | SIM | Não |
| [Timeouts](#time-outs-dataset): Contains information about timeouts on a database. | SIM | Não |
| [Blocks](#blockings-dataset): Contains information about blocking events on a database. | SIM | Não |
| [Deadlocks](#deadlocks-dataset): Contains information about deadlock events on a database. | SIM | Não |
| [AutomaticTuning](#automatic-tuning-dataset): Contains information about automatic tuning recommendations for a database. | SIM | Não |
| [SQLInsights](#intelligent-insights-dataset): Contains Intelligent Insights into performance for a database. Para saber mais, veja [Intelligent Insights](sql-database-intelligent-insights.md). | SIM | SIM |

> [!IMPORTANT]
> Elastic pools and managed instances have their own separate diagnostics telemetry from databases they contain. This is important to note as diagnostics telemetry is configured separately for each of these resources, as documented below.

> [!NOTE]
> Security Audit and SQLSecurityAuditEvents logs can't be enabled from the database diagnostics settings (although showing on the screen). To enable audit log streaming, see [Set up auditing for your database](sql-database-auditing.md#subheading-2), and [auditing logs in Azure Monitor logs and Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Portal do Azure

You can use the **Diagnostics settings** menu for each single, pooled, or instance database in Azure portal to configure streaming of diagnostics telemetry. In addition, diagnostic telemetry can also be configured separately for database containers: elastic pools and managed instances. You can set the following destinations to stream the diagnostics telemetry: Azure Storage, Azure Event Hubs, and Azure Monitor logs.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configurar a transmissão de telemetria de diagnóstico para pools elásticos

   ![Ícone de pool Elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Você pode configurar um recurso de pool elástico para coletar a telemetria de diagnóstico a seguir:

| Grupos | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Pool elástico** | [Basic metrics](sql-database-metrics-diag-logging.md#basic-metrics) contains eDTU/CPU percentage, eDTU/CPU limit, physical data read percentage, log write percentage, sessions percentage, workers percentage, storage, storage percentage, storage limit, and XTP storage percentage. |

To configure streaming of diagnostics telemetry for elastic pools and databases in elastic pools, you will need to separately configure **both** of the following:

- Enable streaming of diagnostics telemetry for an elastic pool, **and**
- Enable streaming of diagnostics telemetry for each database in elastic pool

This is because elastic pool is a database container with its own telemetry being separate from an individual database telemetry.

Para habilitar o streaming de telemetria de diagnóstico para recursos de um pool elástico, siga estas etapas:

1. Go to the **elastic pool** resource in Azure portal.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para pools elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. For log analytics, select **Configure** and create a new workspace by selecting **+Create New Workspace**, or select an existing workspace.
1. Select the check box for elastic pool diagnostics telemetry: **Basic** metrics.
   ![Configure diagnostics for elastic pools](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

1. Clique em **Salvar**.
1. In addition, configure streaming of diagnostics telemetry for each database within the elastic pool you want to monitor by following steps described in the next section.

> [!IMPORTANT]
> In addition to configuring diagnostics telemetry for an elastic pool, you also need to configure diagnostics telemetry for each database in elastic pool, as documented below.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configure streaming of diagnostics telemetry for single database, or database in elastic pool

   ![Ícone do Banco de Dados SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

To enable streaming of diagnostics telemetry for single or pooled databases, follow these steps:

1. Go to Azure **SQL database** resource.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três conexões paralelas para transmitir telemetria de diagnóstico.
   - Select **Add diagnostic setting** to configure parallel streaming of diagnostics data to multiple resources.

   ![Habilitar o diagnóstico de bancos de dados de instância, individuais ou em pool](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. For the standard, event-based monitoring experience, select the following check boxes for database diagnostics log telemetry: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**, and **Deadlocks**.
1. For an advanced, one-minute-based monitoring experience, select the check box for **Basic** metrics.
   ![Configure diagnostics for single, pooled, or instance databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Clique em **Salvar**.
1. Repeat these steps for each database you want to monitor.

> [!NOTE]
> Security Audit and SQLSecurityAuditEvents logs can't be enabled from the database diagnostics settings (although shown on the screen). To enable audit log streaming, see [Set up auditing for your database](sql-database-auditing.md#subheading-2), and [auditing logs in Azure Monitor logs and Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

> [!TIP]
> Repita essas etapas para cada Banco de Dados SQL do Azure que você deseja monitorar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configurar a transmissão de telemetria de diagnóstico para as instâncias gerenciadas

   ![Ícone da Instância Gerenciada](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Você pode configurar um recurso de instância gerenciada para coletar a telemetria de diagnóstico a seguir:

| Grupos | Telemetria de monitoramento |
| :------------------- | ------------------- |
| **Instância gerenciada** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) contém a contagem de vCores, o percentual médio de CPU, as solicitações de E/S, os bytes lidos/gravados, o espaço de armazenamento reservado e o espaço de armazenamento usado. |

To configure streaming of diagnostics telemetry for managed instance and instance databases, you will need to separately configure **both** of the following:

- Enable streaming of diagnostics telemetry for managed instance, **and**
- Enable streaming of diagnostics telemetry for each instance database

This is because managed instance is a database container with its own telemetry, separate from an individual instance database telemetry.

Para habilitar o streaming de telemetria de diagnóstico para um recurso de instância gerenciada, siga estas etapas:

1. Go to the **managed instance** resource in Azure portal.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.

   ![Habilitar o diagnóstico para a instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. For log analytics, select **Configure** and create a new workspace by selecting **+Create New Workspace**, or use an existing workspace.
1. Select the check box for instance diagnostics telemetry: **ResourceUsageStats**.

   ![Configurar o diagnóstico para a instância gerenciada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

1. Clique em **Salvar**.
1. In addition, configure streaming of diagnostics telemetry for each instance database within the managed instance you want to monitor by following the steps described in the next section.

> [!IMPORTANT]
> In addition to configuring diagnostics telemetry for a managed instance, you also need to configure diagnostics telemetry for each instance database, as documented below.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configure streaming of diagnostics telemetry for instance databases

   ![Ícone de banco de dados de instância na instância gerenciada](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

To enable streaming of diagnostics telemetry for instance databases, follow these steps:

1. Go to **instance database** resource within managed instance.
1. Selecionar **configurações de Diagnóstico**.
1. Selecione **Ativar diagnóstico** se nenhuma configuração anterior existir ou selecione **Editar configuração** para editar uma configuração anterior.
   - Você pode criar até três (3) conexões paralelas para transmitir telemetria de diagnóstico.
   - Selecione **+Adicionar configuração de diagnóstico** para configurar o streaming de dados de diagnóstico para vários recursos.

   ![Habilitar o diagnóstico de bancos de dados de instância](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Insira um nome de configuração para sua própria referência.
1. Select a destination resource for the streaming diagnostics data: **Archive to storage account**, **Stream to an event hub**, or **Send to Log Analytics**.
1. Select the check boxes for database diagnostics telemetry: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** and **Errors**.
   ![Configure diagnostics for instance databases](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Clique em **Salvar**.
1. Repeat these steps for each instance database you want to monitor.

> [!TIP]
> Repeat these steps for each instance database you want to monitor.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

Você pode habilitar o log de diagnóstico e métricas usando o PowerShell.

- Para habilitar o armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar o streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da regra do Barramento de Serviço do Azure é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio de logs de diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- É possível obter a ID de recurso do espaço de trabalho do Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="to-configure-multiple-azure-resources"></a>Configurar vários recursos do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Insira a ID de recurso do workspace \<$WSID\> como um parâmetro ao executar o script `Enable-AzureRMDiagnostics.ps1` para enviar dados de diagnóstico de diversos recursos para o workspace.

- Para obter a ID do workspace \<$WSID\> de destino para seus dados de diagnóstico, use o seguinte script:

    ```powershell
    $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Substitua \<subID\> pela ID de assinatura \<RG_NAME\> com o nome do grupo de recursos e \<WS_NAME\> com o nome do workspace.

### <a name="azure-cli"></a>Azure CLI

Você pode habilitar o log de diagnóstico e métricas usando a CLI do Azure.

> [!NOTE]
> Scripts to enable diagnostics logging are supported for Azure CLI v1.0. Please note that CLI v2.0 is unsupported at this time.

- Para habilitar armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A ID da conta de armazenamento é a ID do recurso para a conta de armazenamento de destino.

- Para habilitar streaming dos logs de diagnóstico para um hub de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A ID da regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar envio dos logs de diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Stream em Análise de SQL do Azure

A Análise de SQL do Azure é uma solução de nuvem que monitora o desempenho de bancos de dados SQL do Azure, de pools elásticos e das instâncias gerenciadas em escala e em várias assinaturas. Pode ajudá-lo a coletar e visualizar as métricas de desempenho do Banco de Dados SQL do Azure e tem uma inteligência interna para solução de problemas de desempenho.

![Visão geral da Análise de SQL do Azure](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Logs de diagnóstico e métricas de banco de dados SQL podem ser transmitidos para Análise de SQL do Azure usando o interno **enviar para Log Analytics** opção na guia de configurações de diagnóstico no portal. You can also enable log analytics by using a diagnostics setting via PowerShell cmdlets, the Azure CLI, or the Azure Monitor REST API.

### <a name="installation-overview"></a>Visão geral da instalação

Você pode monitorar uma frota de Bancos de Dados SQL com a Análise de SQL do Azure. Execute as seguintes etapas:

1. Criar uma solução de Análise de SQL do Azure usando o Azure Marketplace.
2. Criar workspace de monitoramento na solução.
3. Configurar bancos de dados para transmitir telemetria de diagnóstico para o workspace.

Se você estiver usando pools elásticos ou instâncias gerenciadas, também precisará configurar a telemetria de diagnóstico de streaming desses recursos.

### <a name="create-azure-sql-analytics-resource"></a>Criar recurso de Análise de SQL do Azure

1. Pesquise a Análise de SQL do Azure no Azure Marketplace e selecione-o.

   ![Pesquisar para Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Selecione **Criar** na tela de visão geral da solução.

3. Preencha o formulário de Análise de SQL do Azure com as informações adicionais necessárias: nome do workspace, assinatura, grupo de recursos, local e tipo de preço.

   ![Configurar Análise de SQL do Azure no portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Selecione **OK** para confirmar e, em seguida, selecione **Criar**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurar bancos de dados para gravar logs de diagnóstico e métricas

The easiest way to configure where databases record metrics is by using the Azure portal. Conforme descrito anteriormente, vá até o recurso de Banco de Dados SQL no portal do Azure e selecione **Configurações de diagnóstico**.

Se você estiver usando pools elásticos ou instância gerenciada, também precisará definir as configurações de diagnóstico nestes recursos para habilitar a telemetria de diagnóstico para transmitir para o workspace.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>Use the SQL Analytics solution for monitoring and alerting

Você pode usar a Análise do SQL como um painel hierárquico para exibir os recursos de Banco de Dados SQL.

- Para saber como usar a solução de Análise de SQL, consulte [Monitorar o Banco de Dados SQL usando a solução de Análise de SQL](../log-analytics/log-analytics-azure-sql.md).
- To learn how to setup alerts for SQL Database and managed instance based on SQL Analytics, see [Creating alerts for SQL Database and managed instance](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Transmitir para os Hubs de Eventos

Você pode transmitir métricas e logs de diagnóstico do Banco de Dados SQL para Hubs de Eventos usando a opção interna **Transmitir para um hub de eventos** no portal do Azure. Também é possível habilitar a ID de regra do Barramento de Serviço usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>O que fazer com as métricas e os logs de diagnóstico nos Hubs de Eventos

Depois que os dados selecionados são transmitidos para os Hubs de Eventos, os habilitar cenários de monitoramento avançado estarão mais próximos. Os Hubs de Eventos atuam como uma porta da frente para um pipeline de eventos. Depois que os dados são coletados para um hub de eventos, eles podem ser transformados e armazenados usando um provedor de análise em tempo real ou um adaptador de armazenamento. Os Hubs de Eventos separam a produção de um fluxo de eventos do consumo desses eventos. Dessa forma, os consumidores de evento podem acessá-los em suas próprias agendas. Para obter mais informações sobre os Hubs de Eventos, consulte:

- [O que são Hubs de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Você pode usar métricas de streaming em Hubs de Eventos para:

- **View service health by streaming hot-path data to Power BI**

   Ao utilizar os Hubs de Eventos, o Stream Analytics e o Power BI, é fácil transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para obter uma visão geral de como configurar um hub de eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída, consulte [Stream Analytics e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Stream logs to third-party logging and telemetry streams**

   Ao utilizar a transmissão dos Hubs de Eventos, é possível obter métricas e logs de diagnóstico em diversas soluções de monitoramento de terceiros e análise de logs.

- **Build a custom telemetry and logging platform**

   Fazer você já tem uma plataforma de telemetria personalizada ou está pensando em criar uma? A natureza altamente escalonável de publicação-assinatura de Hubs de Eventos permite flexibilidade para ingerir logs de diagnóstico. Consulte [o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmitir para o Armazenamento

Você pode armazenar métricas de Banco de Dados SQL e logs de diagnóstico no Armazenamento do Azure usando a opção interna **Arquivar em uma conta de armazenamento** no portal do Azure. Também é possível habilitar o Armazenamento usando uma configuração de diagnóstico por meio dos cmdlets do PowerShell, da CLI do Azure ou da API REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas e logs de diagnóstico na conta de armazenamento

Depois que você configurar a coleta de métricas e logs de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento selecionada quando as primeiras linhas de dados estão disponíveis. A estrutura dos blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ou, simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

For example, a blob name for Basic metrics might be:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Um nome de blob para armazenar dados de um pool elástico se parece com:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Política de retenção de dados e preços

Ao selecionar os Hubs de Eventos ou uma conta de Armazenamento, é possível especificar uma política de retenção. Essa política exclui dados mais antigos que um período de tempo selecionado. Se você especificar a análise de Log, a política de retenção depende do tipo de preço selecionado. Nesse caso, as unidades gratuitas de ingestão de dados fornecidas podem habilitar o monitoramento gratuito de vários bancos de dados por mês. Qualquer consumo de telemetria de diagnóstico que exceda as unidades gratuitas poderá gerar custos. Lembre-se de que os bancos de dados ativos com cargas de trabalho mais pesadas ingerem mais dados do que bancos de dados ociosos. For more information, see [Log analytics pricing](https://azure.microsoft.com/pricing/details/monitor/).

Se você estiver usando a Análise de SQL do Azure, poderá monitorar o consumo de ingestão de dados na solução selecionando o **Workspace OMS** no menu de navegação da Análise de SQL do Azure e, em seguida, selecionando **Uso** e **Custos Estimados**.

## <a name="metrics-and-logs-available"></a>Métricas e logs disponíveis

Monitoring telemetry available for Azure SQL Database, elastic pools and managed instance is documented below. Collected monitoring telemetry inside SQL Analytics can be used for your own custom analysis and application development using [Azure Monitor log queries](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) language.

## <a name="basic-metrics"></a>Basic metrics

Refer to the following tables for details about Basic metrics by resource.

> [!NOTE]
> Basic metrics option was formerly known as All metrics. The change made was to the naming only and there was no change to the metrics monitored. This change was initiated to allow for introduction of additional metric categories in the future.

### <a name="basic-metrics-for-elastic-pools"></a>Basic metrics for elastic pools

|**Recurso**|**Métricas**|
|---|---|
|Pool elástico|O percentual de eDTU, eDTU usado, limite de eDTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, limite de armazenamento, percentual de armazenamento XTP |

### <a name="basic-metrics-for-azure-sql-databases"></a>Basic metrics for Azure SQL Databases

|**Recurso**|**Métricas**|
|---|---|
|Banco de Dados SQL do Azure|O percentual de DTU, DTU usado, o limite de DTU, percentual de CPU, percentual de leitura de dados físicos, percentual de gravação de log, êxito/falha/bloqueio por conexões de firewall, percentual de sessões, percentual de funcionários, armazenamento, percentual de armazenamento, percentual de armazenamento XTP e deadlocks |

## <a name="advanced-metrics"></a>Advanced metrics

Refer to the following table for details about advanced metrics.

|**Métrica**|**Nome de exibição da métrica**|**Descrição**|
|---|---|---|
|tempdb_data_size| Tempdb Data File Size Kilobytes |Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models. |
|tempdb_log_size| Tempdb Log File Size Kilobytes |Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models. |
|tempdb_log_used_percent| Tempdb Percent Log Used |Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models. |

## <a name="basic-logs"></a>Basic logs

Details of telemetry available for all logs are documented in the tables below. Please see [supported diagnostic logging](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) to understand which logs are supported for a particular database flavor - Azure SQL single, pooled, or instance database.

### <a name="resource-usage-stats-for-managed-instance"></a>Resource usage stats for managed instance

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: ResourceUsageStats |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: MANAGEDINSTANCES |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome da instância gerenciada |
|ResourceId|URI de recurso |
|SKU_s|SKU do produto da instância gerenciada |
|virtual_core_count_s|Número de vCores disponíveis |
|avg_cpu_percent_s|Percentual médio de CPU |
|reserved_storage_mb_s|Capacidade de armazenamento reservada na instância gerenciada |
|storage_space_used_mb_s|Armazenamento usado na instância gerenciada |
|io_requests_s|Contagem de IOPS |
|io_bytes_read_s|Bytes de IOPS lidos |
|io_bytes_written_s|Bytes de IOPS gravados |

### <a name="query-store-runtime-statistics"></a>Estatísticas de runtime do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|query_hash_s|Hash de consulta |
|query_plan_hash_s|Hash do plano de consulta |
|statement_sql_handle_s|Identificador de sql da instrução |
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|logical_io_writes_d|Número total de gravações lógicas de E/S |
|max_logical_io_writes_d|Número máximo de gravações lógicas de E/S por execução |
|physical_io_reads_d|Número total de leituras físicas de E/S |
|max_physical_io_reads_d|Número máximo de leituras lógicas de E/S por execução |
|logical_io_reads_d|Número total de leituras lógicas de E/S |
|max_logical_io_reads_d|Número máximo de leituras lógicas de E/S por execução |
|execution_type_d|Tipo de execução |
|count_executions_d|Número de execuções da consulta |
|cpu_time_d|Tempo total de CPU consumido pela consulta em microssegundos |
|max_cpu_time_d|Tempo máximo de CPU do consumidor por uma execução única em microssegundos |
|dop_d|Soma de graus de paralelismo |
|max_dop_d|Grau máximo de paralelismo usado para execução única |
|rowcount_d|Número total de linhas retornadas |
|max_rowcount_d|Número máximo de linhas retornadas em uma única execução |
|query_max_used_memory_d|Quantidade total de memória usada em KB |
|max_query_max_used_memory_d|Quantidade máxima de memória usada por uma única execução em KB |
|duration_d|Tempo total de execução em microssegundos |
|max_duration_d|Tempo máximo de execução de uma única execução |
|num_physical_io_reads_d|Número total de leituras físicas |
|max_num_physical_io_reads_d|Número máximo de leituras físicas por execução |
|log_bytes_used_d|Quantidade total de bytes de log usados |
|max_log_bytes_used_d|Quantidade máxima de bytes de log usados por execução |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano no Repositório de Consultas |

Saiba mais sobre os [Dados de estatísticas de runtime do Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estatísticas de espera do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics |
|OperationName|Nome da operação. Sempre: QueryStoreWaitStatisticsEvent |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_category_s|Categoria da espera |
|is_parameterizable_s|É a consulta parametrizável |
|statement_type_s|Tipo da instrução |
|statement_key_hash_s|Hash de chave de instrução |
|exec_type_d|Tipo de execução |
|total_query_wait_time_ms_d|Tempo total de espera da consulta na categoria de espera específica |
|max_query_wait_time_ms_d|Tempo máximo de espera da consulta em execução individual na categoria de espera específica |
|query_param_type_d|0 |
|query_hash_s|Hash de consulta no Repositório de Consultas |
|query_plan_hash_s|Hash de plano de consulta no Repositório de consultas |
|statement_sql_handle_s|Identificador de instrução no Repositório de consultas |
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1 |
|count_executions_d|Contagem de execuções da consulta |
|query_id_d|ID da consulta no Repositório de Consultas |
|plan_id_d|ID do plano no Repositório de Consultas |

Saiba mais sobre os [Dados de estatísticas de espera no Repositório de Consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Erros |
|OperationName|Nome da operação. Sempre: ErrorEvent |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|Mensagem|Mensagem de erro em texto sem formatação |
|user_defined_b|É o bit de erro definido pelo usuário |
|error_number_d|Código do erro |
|Gravidade|Gravidade do erro |
|state_d|Estado do erro |
|query_hash_s|Hash de consulta da consulta com falha, se disponível |
|query_plan_hash_s|Hash do plano de consulta da consulta com falha, se disponível |

Saiba mais sobre as [Mensagens de erro do SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

### <a name="database-wait-statistics-dataset"></a>Conjunto de dados das estatísticas de espera do banco de dados

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics |
|OperationName|Nome da operação. Sempre: DatabaseWaitStatisticsEvent |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|wait_type_s|Nome do tipo de espera |
|start_utc_date_t [UTC]|Hora de início do período de medida |
|end_utc_date_t [UTC]|Hora de término do período de medida |
|delta_max_wait_time_ms_d|Tempo máximo de espera por execução |
|delta_signal_wait_time_ms_d|Tempo de espera total de sinais |
|delta_wait_time_ms_d|Tempo total de espera no período |
|delta_waiting_tasks_count_d|Número de tarefas em espera |

Saiba mais sobre as [estatísticas de espera no banco de dados](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de dados de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: tempos limite |
|OperationName|Nome da operação. Sempre: TimeoutEvent |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|error_state_d|Código do estado de erro |
|query_hash_s|Hash de consulta, se disponível |
|query_plan_hash_s|Hash do plano de consulta, se disponível |

### <a name="blockings-dataset"></a>Conjunto de dados de boqueios

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: blocos |
|OperationName|Nome da operação. Sempre: BlockEvent |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|lock_mode_s|Modo de bloqueio usado pela consulta |
|resource_owner_type_s|Proprietário do bloqueio |
|blocked_process_filtered_s|XML de relatório de processo bloqueado |
|duration_d|Duração do bloqueio em microssegundos |

### <a name="deadlocks-dataset"></a>Conjunto de dados de deadlocks

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC] |Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: Deadlocks |
|OperationName|Nome da operação. Sempre: Deadlocks |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|deadlock_xml_s|Relatório de deadlock XML |

### <a name="automatic-tuning-dataset"></a>Conjunto de dados de ajuste automático

|Propriedade|Descrição|
|---|---|
|TenantId|Sua ID de locatário |
|SourceSystem|Sempre: Azure |
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado |
|Type|Sempre: AzureDiagnostics |
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL |
|Categoria|Nome da categoria. Sempre: AutomaticTuning |
|Grupos|Nome do recurso |
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS |
|SubscriptionId|GUID de assinatura para o banco de dados |
|ResourceGroup|Nome do grupo de recursos para o banco de dados |
|LogicalServerName_s|Nome do servidor do banco de dados |
|LogicalDatabaseName_s|Nome do banco de dados |
|ElasticPoolName_s|Nome do pool elástico do banco de dados, se houver |
|DatabaseName_s|Nome do banco de dados |
|ResourceId|URI de recurso |
|RecommendationHash_s|Hash exclusivo de recomendação de ajuste automático |
|OptionName_s|Opções de ajuste automático |
|Schema_s|Esquema de banco de dados |
|Table_s|Tabela afetada |
|IndexName_s|Nome do índice |
|IndexColumns_s|Nome da coluna |
|IncludedColumns_s|Colunas incluídas |
|EstimatedImpact_s|Estimado impacto recomendação de ajuste automático JSON |
|Event_s|Tipo de evento de ajuste automático |
|Timestamp_t|Última atualização de carimbo de data/hora |

### <a name="intelligent-insights-dataset"></a>Conjunto de dados do Insights inteligentes

Saiba mais sobre o [formato de log do Insights Inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Próximos passos

Para saber como habilitar o registro em log e entender as categorias de métrica e log com suporte dos vários serviços do Azure, veja:

- [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Visão geral do log de diagnóstico do Azure](../azure-monitor/platform/resource-logs-overview.md)

Para saber mais sobre os Hubs de Evento, leia:

- [O que é Hub de Eventos do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

To learn how to setup alerts based on telemetry from log analytics see:

- [Creating alerts for SQL Database and managed instance](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
