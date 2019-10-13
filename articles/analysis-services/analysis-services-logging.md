---
title: Log de diagnósticos do Azure Analysis Services | Microsoft Docs
description: Saiba mais sobre como configurar o log de diagnósticos do Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a9684042a76c9c906a75334c319b4ca8ee0b727b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298616"
---
# <a name="setup-diagnostic-logging"></a>Configurar o log de diagnósticos

Uma parte importante de qualquer solução do Analysis Services é o monitoramento do desempenho de seus servidores. Com os [logs de diagnóstico de recursos do Azure](../azure-monitor/platform/resource-logs-overview.md), é possível monitorar e enviar logs para o [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/), e exportá-los para os [los do Azure Monitor](../azure-monitor/azure-monitor-log-hub.md).

![Log de diagnósticos para Armazenamento, Hubs de Eventos ou logs do Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>O que é registrado em log?

Você pode selecionar as categorias **Mecanismo**, **Serviço** e **Métricas**.

### <a name="engine"></a>Motor

Selecionar **Mecanismo** registra todos [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Não é possível selecionar eventos individuais. 

|Categorias de XEvent |Nome do evento  |
|---------|---------|
|Auditoria de Segurança    |   Auditar logon      |
|Auditoria de Segurança    |   Auditar logoff      |
|Auditoria de Segurança    |   Auditar início e parada de servidor      |
|Relatórios de andamento     |   Início de relatório de andamento      |
|Relatórios de andamento     |   Fim de relatório de andamento      |
|Relatórios de andamento     |   Relatório de andamento atual      |
|Consultas     |  Início de consulta       |
|Consultas     |   Término de consulta      |
|Comandos     |  Início de comando       |
|Comandos     |  Término de comando       |
|Erros e avisos     |   Erro      |
|Descobrir     |   Término de descoberta      |
|Notificação     |    Notificação     |
|Session     |  Inicialização de sessão       |
|Bloqueios    |  Deadlock       |
|Processamento de consulta     |   Início da consulta VertiPaq SE      |
|Processamento de consulta     |   Final da consulta VertiPaq SE      |
|Processamento de consulta     |   Correspondência de Cache de consulta VertiPaq SE      |
|Processamento de consulta     |   Início de consulta direta      |
|Processamento de consulta     |  Término de consulta direta       |

### <a name="service"></a>Serviço

|Nome da operação  |Ocorre quando  |
|---------|---------|
|ResumeServer     |    Retomar um servidor     |
|SuspendServer    |   Pausar um servidor      |
|DeleteServer     |    Excluir um servidor     |
|RestartServer    |     Usuário reinicia um servidor por meio do SSMS ou PowerShell    |
|GetServerLogFiles    |    Usuário exporta log de servidor por meio do PowerShell     |
|ExportModel     |   Usuário exporta um modelo no portal usando Abrir no Visual Studio     |

### <a name="all-metrics"></a>Todas as métricas

A categoria de métricas registra as mesmas [métricas de servidor](analysis-services-monitor.md#server-metrics) na tabela AzureMetrics. Se você estiver usando a [expansão](analysis-services-scale-out.md) de consulta e precisar separar métricas para cada réplica de leitura, use a tabela AzureDiagnostics em vez disso, em que **OperationName** é igual a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configurar o log de diagnósticos

### <a name="azure-portal"></a>Portal do Azure

1. Em [Portal do Azure](https://portal.azure.com) > Servidor, clique em **Logs de diagnósticos** na navegação à esquerda e, em seguida, clique em **Ativar diagnóstico**.

    ![Ativar o log de diagnósticos do Azure Cosmos DB no portal do Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. Em **Configurações de diagnóstico**, especifique as opções a seguir: 

    * **Nome**. Insira um nome para os logs a serem criados.

    * **Arquivar em uma conta de armazenamento**. Para usar essa opção, você precisa de uma conta de armazenamento existente à qual se conectar. Consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). Siga as instruções para criar um Gerenciador de Recursos, uma conta de finalidade geral, em seguida, selecione sua conta de armazenamento retornando para esta página no portal. Pode levar alguns minutos para que as contas de armazenamento recém-criadas sejam exibidas no menu suspenso.
    * **Transmitir para um hub de eventos**. Para usar essa opção, é necessário ter um namespace existente do Hub de Eventos e um hub de evento ao qual se conectar. Para saber mais, consulte [Criar um namespace de Hubs de Eventos e um hub de eventos usando o Portal do Azure](../event-hubs/event-hubs-create.md). Em seguida, retorne a esta página no portal para selecionar o namespace e o nome da política do Hub de Eventos.
    * **Enviar para o Azure Monitor (espaço de trabalho do Log Analytics)** . Para usar essa opção, use um workspace existente ou [crie um novo recurso de workspace](../azure-monitor/learn/quick-create-workspace.md) no portal. Para obter mais informações sobre como exibir os logs, confira [Exibir logs no espaço de trabalho do Log Analytics](#view-logs-in-log-analytics-workspace) neste artigo.

    * **Mecanismo**. Selecione esta opção para registrar xEvents. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Serviço**. Selecione esta opção para registrar eventos de nível de serviço. Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.
    * **Métricas**. Selecione esta opção para armazenar dados detalhados em [Métricas](analysis-services-monitor.md#server-metrics). Se você estiver arquivando em uma conta de armazenamento, poderá selecionar o período de retenção para os logs de diagnóstico. Os logs são excluídos automaticamente depois que o período de retenção expira.

3. Clique em **Salvar**.

    Se você receber um erro que diga "Falha ao atualizar o diagnóstico para \<nome do workspace&gt;. A assinatura \<id da assinatura> não está registrada para usar o microsoft.insights." siga as instruções para [Solucionar Problemas de Diagnóstico do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) para registrar a conta, então repita este procedimento.

    Sempre que você quiser alterar como os seus logs de diagnóstico são salvos, retorne a esta página para modificar as configurações.

### <a name="powershell"></a>PowerShell

Aqui estão os comandos básicos para você começar. Se você desejar obter ajuda passo a passo sobre como configurar o registro em log para uma conta de armazenamento usando o PowerShell, consulte o tutorial mais adiante neste artigo.

Para habilitar as métricas e o log de diagnósticos usando o PowerShell, use os seguintes comandos:

- Para habilitar o armazenamento do log de diagnóstico em uma conta de armazenamento, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da conta de armazenamento é a ID de recurso da conta de armazenamento para a qual os logs serão enviados.

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

### <a name="rest-api"></a>API REST

Saiba como [alterar as configurações de diagnóstico usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Saiba como [habilitar as configurações de diagnóstico na criação de recursos usando um modelo do Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Gerenciar seus logs

Os logs estão geralmente disponíveis em algumas horas após a configuração do registro em log. Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos de controle de acesso padrão do Azure para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.
* Certifique-se de definir um período de retenção para que logs antigos sejam excluídos da sua conta de armazenamento.

## <a name="view-logs-in-log-analytics-workspace"></a>Exibir logs no espaço de trabalho do Log Analytics

Os eventos de métricas e de servidor são integrados com o xEvents no recurso de espaço de trabalho do Log Analytics para uma análise lado a lado. O espaço de trabalho do Log Analytics também pode ser configurado para receber eventos de outros serviços do Azure, fornecendo uma exibição holística dos dados de log de diagnósticos em sua arquitetura.

Para exibir seus dados de diagnóstico, no espaço de trabalho do Log Analytics, abra **Logs** no menu à esquerda.

![Opções de pesquisa de logs no portal do Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

No construtor de consultas, expanda **LogManagement** > **AzureDiagnostics**. O AzureDiagnostics inclui eventos de Mecanismo e Serviço. Veja que uma consulta é imediatamente criada. O campo EventClass\_s contém nomes de xEvents, que podem parecer familiares se você já usou xEvents para registro em log local. Clique em **EventClass\_s** ou em um dos nomes de eventos e o espaço de trabalho do Log Analytics continuará a criar uma consulta. Certifique-se de salvar as consultas para reutilizar posteriormente.

### <a name="example-queries"></a>Consultas de exemplo

#### <a name="example-1"></a>Exemplo 1

A consulta a seguir retorna durações para cada evento end/atualizar end da consulta para um banco de dados modelo e um servidor. Se for expandido, os resultados serão divididos por réplica porque o número da réplica está incluído em ServerName_s. O agrupamento por RootActivityId_g reduz a contagem de linhas recuperadas da API REST Diagnóstico do Azure e ajuda a permanecer dentro dos limites, conforme descrito em [limites de taxa de log Analytics](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Exemplo 2

A consulta a seguir retorna a memória e o consumo de QPU para um servidor. Se for expandido, os resultados serão divididos por réplica porque o número da réplica está incluído em ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Exemplo 3

A consulta a seguir retorna os contadores de desempenho de linhas lidas/s Analysis Services mecanismo para um servidor.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Há centenas de consultas que você pode usar. Para saber mais sobre as consultas, confira [Introdução às consultas de log do Azure Monitor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Ativar o registro em log usando o PowerShell

Neste tutorial rápido, você cria uma conta de armazenamento na mesma assinatura e mesmo grupo de recursos que o servidor do Analysis Services. Em seguida, use set-AzDiagnosticSetting para ativar o log de diagnósticos, enviando a saída para a nova conta de armazenamento.

### <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você deve ter os seguintes recursos:

* Um servidor existente do Azure Analysis Services. Para obter instruções de como criar um recurso de servidor, consulte [Criar um servidor no Portal do Azure](analysis-services-create-server.md) ou [Criar um servidor do Azure Analysis Services usando o PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Conectar-se às suas assinaturas

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver várias assinaturas, talvez tenha que indicar uma assinatura específica que tenha sido usada para criar o Cofre de Chaves do Azure. Digite o seguinte para ver as assinaturas da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura associada à conta do Azure Analysis Services que está sendo registrada, digite:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Caso tenha várias assinaturas associadas à sua conta, é importante especificar a assinatura.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Criar uma nova conta de armazenamento para seus logs

Você pode usar uma conta de armazenamento existente para seus logs, desde que ela esteja na mesma assinatura que o servidor. Neste tutorial você cria uma nova conta de armazenamento dedicada aos logs do Analysis Services. Para facilitar, você armazenará os detalhes da conta de armazenamento em uma variável chamada **sa**.

Você também usa o mesmo grupo de recursos que aquele que contém o servidor do Analysis Services. Substitua os valores de `awsales_resgroup`, `awsaleslogs` e `West Central US` pelos seus próprios valores:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificar a conta de servidor para seus logs

Defina o nome da conta do como uma variável chamada **account**, em que ResourceName é o nome da conta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Habilitar o registro em log

Para habilitar o registro em log, use o cmdlet Set-AzDiagnosticSetting junto com as variáveis para a nova conta de armazenamento, conta de servidor e a categoria. Execute o seguinte comando, definindo o sinalizador **-Enabled** como **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

A saída deve ser semelhante ao exemplo a seguir:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Essa saída confirma que o registro em log agora está habilitado para o servidor, salvando as informações na conta de armazenamento.

Você também pode definir a política de retenção para os logs, para que logs mais antigos sejam excluídos automaticamente. Por exemplo, defina a política de retenção usando o sinalizador **-RetentionEnabled** como **$true** e defina o parâmetro **-RetentionInDays** como **90**. Os logs com mais de 90 dias serão automaticamente excluídos.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [o registro em log de diagnóstico de recurso do Azure](../azure-monitor/platform/resource-logs-overview.md).

Consulte [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) na ajuda do PowerShell.
