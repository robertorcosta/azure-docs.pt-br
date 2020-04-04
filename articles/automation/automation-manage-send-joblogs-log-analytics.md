---
title: Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor
description: Este artigo demonstra como enviar fluxos de trabalho e fluxos de trabalho de runbook para os registros do Azure Monitor para fornecer insights e gerenciamento adicionais.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 54f77f55a127cd712d43419eb6a85fd5d93a478c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652169"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Encaminhe o status do trabalho e os fluxos de trabalho dos registros de automação para o Monitor do Azure

A Automação pode enviar status de trabalho do runbook e fluxos de trabalho para o espaço de trabalho do Log Analytics. Esse processo não envolve a vinculação de workspace e é completamente independente. Os logs e fluxos de trabalho podem ser vistos no portal do Azure ou com o PowerShell, no caso de trabalhos individuais, e isso permite a você fazer investigações simples. Agora, com os logs do Monitor do Azure, você pode:

* Obter informações sobre os Trabalhos de automação.
* Disparar um email ou um alerta com base no status de trabalho de runbook (por exemplo, com falha ou suspenso).
* Escrever consultas avançadas em seus fluxos de trabalho.
* Correlacionar trabalhos em Contas de automação.
* Visualizar o histórico de trabalhos ao longo do tempo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implantação

Para começar a enviar seus registros de automação para os registros do Monitor do Azure, você precisa:

* O último lançamento do [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Um espaço de trabalho do Log Analytics. Para obter mais informações, consulte [Comece com os registros do Monitor Do Azure](../log-analytics/log-analytics-get-started.md).
* O ID de recurso para sua conta de Automação Azure.

Use o seguinte comando para encontrar o ID de recurso para sua conta do Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para encontrar o ID de recurso para o espaço de trabalho do Log Analytics, execute o seguinte comando PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se você tiver mais de uma conta de automação ou espaço de trabalho na saída dos comandos anteriores, encontre o nome que você precisa para configurar e copiar o valor para o ID de recurso.

1. No portal Azure, selecione sua conta de Automação na lâmina da **conta Automação** e selecione Todas **as configurações**. 
2. Na **lâmina Todas as configurações,** em **Configurações de conta,** selecione **Propriedades**.  
3. Na lâmina **Propriedades,** observe esses valores.<br> ![Propriedades da](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)conta de automação .


## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor

Os diagnósticos do Azure Automation criam dois tipos de registros `AzureDiagnostics`nos registros do Monitor do Azure, marcados como . As tabelas nas próximas seções são exemplos de registros que o Azure Automation gera e os tipos de dados que aparecem nos resultados de pesquisa de log.

### <a name="job-logs"></a>Logs de trabalho

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O interlocutor que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| JobId_g |GUID que identifica o trabalho de runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- Novo<br>- Criado<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Com falha<br>– Concluído |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobLogs. |
| OperationName | O tipo de operação realizada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta automação |
| SourceSystem | Sistema que os logs do Azure Monitor usam para coletar os dados. O valor é sempre Azure para diagnósticos Do Azure. |
| ResultDescription |O resultado do trabalho do manual é estadual. Os valores possíveis são:<br>- O trabalho foi iniciado<br>- O trabalho falhou<br>- Trabalho Concluído |
| CorrelationId |A correlação GUID do trabalho de runbook. |
| ResourceId |O ID de recurso da conta azure Automation do manual. |
| SubscriptionId | A assinatura do Azure GUID para a conta automação. |
| ResourceGroup | O nome do grupo de recursos para a conta Automação. |
| ResourceProvider | O provedor de recursos. O valor é da MICROSOFT. Automação. |
| ResourceType | O tipo de recurso. O valor é CONTAS DE AUTOMAÇÃO. |

### <a name="job-streams"></a>Transmissões de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O interlocutor que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Andamento<br>- Saída<br>- Aviso<br>- Erro<br>- Depurar<br>- Detalhado |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| JobId_g |GUID que identifica o trabalho de runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- em andamento |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobStreams. |
| OperationName | Tipo de operação realizada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta de Automação. |
| SourceSystem | Sistema que os logs do Azure Monitor usam para coletar os dados. O valor é sempre Azure para diagnósticos Do Azure. |
| ResultDescription |Descrição que inclui o fluxo de saída do runbook. |
| CorrelationId |A correlação GUID do trabalho de runbook. |
| ResourceId |O ID de recurso da conta azure Automation do manual. |
| SubscriptionId | A assinatura do Azure GUID para a conta automação. |
| ResourceGroup | O nome do grupo de recursos para a conta Automação. |
| ResourceProvider | O provedor de recursos. O valor é da MICROSOFT. Automação. |
| ResourceType | O tipo de recurso. O valor é CONTAS DE AUTOMAÇÃO. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Configurando a integração com os registros do Monitor do Azure

1. Em seu computador, inicie o Windows PowerShell na tela **Inicial**.
2. Execute os seguintes comandos PowerShell e `[your resource ID]` `[resource ID of the log analytics workspace]` edite o valor para a e com os valores da seção anterior.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Depois de executar este script, pode levar uma hora antes de você `JobLogs` começar `JobStreams` a ver registros no Azure Monitor logs de novos ou sendo escritos.

Para ver os logs, execute a seguinte consulta na pesquisa de log analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificar a configuração

Para confirmar se sua conta de Automação está enviando logs para o espaço de trabalho do Log Analytics, verifique se os diagnósticos estão configurados corretamente na conta de Automação usando o seguinte comando PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Na saída, certifique-se de que:

* Em `Logs`, o `Enabled` valor para é verdadeiro.
* `WorkspaceId`é definido `ResourceId` como o valor do seu espaço de trabalho log analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visualizando logs de automação nos registros do Monitor do Azure

Agora que você começou a enviar seus registros de trabalho de Automação para logs do Monitor do Azure, vamos ver o que você pode fazer com esses logs dentro dos registros do Monitor do Azure.

Para ver os logs, execute a seguinte consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Enviar um email quando um trabalho de runbook falhar ou for suspenso

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado em um trabalho de runbook.

Para criar uma regra de alerta, comece criando uma pesquisa de registro para os registros de trabalho do manual que devem invocar o alerta. Clique no botão **Alerta** para criar e configurar a regra de alerta.

1. Na página 'Visão geral do espaço de trabalho' log analytics, clique **em Exibir logs**.
2. Crie uma consulta de pesquisa de log para o seu alerta digitando a seguinte pesquisa no campo de consulta:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Você também pode agrupar pelo nome do runbook usando:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se você configurou logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por Conta de automação. O nome da conta `Resource` de automação pode `JobLogs`ser encontrado no campo na busca de .
3. Para abrir a tela ** Criar regra **, clique em ** + Nova regra de alerta ** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [ Logar alertas no Azure ](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Localizar todos os trabalhos que foram concluídos com erros

Além de alertas de falhas, você pode descobrir quando um trabalho de runbook tem um erro não fatal. Nesses casos, o PowerShell produz um fluxo de erros, mas os erros não terminantes não fazem com que seu trabalho suspenda ou falhe.

1. No espaço de trabalho do Log Analytics, clique **em Logs**.
2. No campo de consulta, digite `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.
3. Clique no botão **Pesquisar.**

### <a name="view-job-streams-for-a-job"></a>Exibir fluxos de trabalho para um trabalho

Quando você está depurando um trabalho, você também pode querer olhar para os fluxos de trabalho. A consulta abaixo mostra todos os fluxos para um único trabalho com GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Exibir o status do histórico de trabalho

Finalmente, você pode querer visualizar seu histórico de trabalho ao longo do tempo. Você pode usar essa consulta para pesquisar o status dos trabalhos ao longo do tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Gráfico de status de trabalho histórico do Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Removendo configurações de diagnóstico

Para remover a configuração de diagnóstico da conta Automação, execute o seguinte comando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Resumo

Ao enviar seu status de trabalho de Automação e transmitir dados para logs do Azure Monitor, você pode obter uma melhor visão sobre o status de seus trabalhos de Automação por:
+ Configurando alertas para notificá-lo quando houver um problema.
+ Usando exibições personalizadas e consultas de pesquisa para visualizar os resultados de runbook, o status do trabalho de runbook e outros principais indicadores ou métricas relacionadas.

Os registros do Monitor do Azure fornecem maior visibilidade operacional aos seus trabalhos de Automação e podem ajudar a resolver incidentes mais rapidamente.

## <a name="next-steps"></a>Próximas etapas

* Para ajudar a solucionar problemas no Log Analytics, consulte [Solução de problemas porque o Log Analytics não está mais coletando dados](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Para saber mais sobre como construir diferentes consultas de pesquisa e revisar os registros de trabalho de Automação com logs do Monitor do Azure, consulte [Pesquisas de log nos registros do Monitor do Azure](../log-analytics/log-analytics-log-searches.md).
* Para entender como criar e recuperar mensagens de saída e erro dos runbooks, consulte [saída e mensagens do Runbook](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md).
* Para saber mais sobre os registros do Azure Monitor e as fontes de coleta de dados, consulte [Collecting Azure storage data in Azure Monitor logs overview](../azure-monitor/platform/collect-azure-metrics-logs.md).

