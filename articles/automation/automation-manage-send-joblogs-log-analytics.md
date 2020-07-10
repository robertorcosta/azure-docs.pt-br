---
title: Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor
description: Este artigo mostra como enviar status de trabalho e fluxos de trabalho de runbook para logs do Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0c8863615446f8b14043cd7bd13e529b7efa1e46
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186190"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor

A Automação do Azure pode enviar fluxos de trabalho e status de trabalho de runbook para seu workspace do Log Analytics. Esse processo não envolve a vinculação de workspace e é completamente independente. Os logs e fluxos de trabalho podem ser vistos no portal do Azure ou com o PowerShell, no caso de trabalhos individuais, e isso permite a você fazer investigações simples. Agora, com os logs do Azure Monitor, você pode:

* Obter informações sobre o status de seus trabalhos de Automação.
* Disparar um email ou um alerta com base no status de trabalho de runbook (por exemplo, com falha ou suspenso).
* Escrever consultas avançadas em seus fluxos de trabalho.
* Correlacionar trabalhos em Contas de automação.
* Usar exibições personalizadas e consultas de pesquisa para visualizar os resultados do runbook, o status do trabalho de runbook e outros indicadores ou métricas principais relacionados.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implantação

Para começar a enviar seus logs de Automação para os logs do Azure Monitor, você precisa:

* Da última versão do [Azure PowerShell](/powershell/azureps-cmdlets-docs/).
* Um workspace do Log Analytics. Para obter mais informações, confira [Introdução aos logs do Azure Monitor](../azure-monitor/overview.md).
* A ID do recurso para sua conta de Automação do Azure.

Use o seguinte comando para localizar a ID de recurso da sua conta de Automação do Azure:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para localizar a ID do recurso para seu workspace do Log Analytics, execute o seguinte comando do PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se você tiver mais de uma conta de Automação ou workspace na saída dos comandos anteriores, poderá encontrar o nome e outras propriedades relacionadas que fazem parte da ID do recurso completa da sua conta de Automação executando o seguinte:

1. Na portal do Azure, selecione sua conta de Automação na página **Contas de Automação**. 
2. Na página da conta de Automação selecionada, em **Configurações da Conta**, selecione **Propriedades**.  
3. Na página **Propriedades**, observe os detalhes mostrados abaixo.

    ![Propriedades da Conta de Automação](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor

O diagnóstico da Automação do Azure cria dois tipos de registros em logs do Azure Monitor, marcados como `AzureDiagnostics`. As tabelas nas próximas seções são exemplos de registros que a Automação do Azure gera e os tipos de dados que aparecem nos resultados da pesquisa de logs.

### <a name="job-logs"></a>Logs de trabalho

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |A GUID que identifica o trabalho do runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- Novo<br>– Criado<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Com falha<br>– Concluído |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobLogs. |
| OperationName | O tipo de operação executada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta de Automação |
| SourceSystem | O sistema que os logs do Azure Monitor usam para coletar os dados. O valor é sempre Azure para o diagnóstico do Azure. |
| ResultDescription |O estado do resultado do trabalho de runbook. Os valores possíveis são:<br>- O trabalho foi iniciado<br>- O trabalho falhou<br>- Trabalho Concluído |
| CorrelationId |O GUID de correlação do trabalho de runbook. |
| ResourceId |A ID de recurso da conta de Automação do Azure do runbook. |
| SubscriptionId | O GUID da assinatura do Azure para a conta de Automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de Automação. |
| ResourceProvider | O provedor de recursos. O valor é MICROSOFT.AUTOMATION. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Transmissões de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>\- Andamento<br>- Saída<br>- Aviso<br>- Erro<br>- Depurar<br>- Detalhado |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |A GUID que identifica o trabalho do runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- em andamento |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobStreams. |
| OperationName | O tipo de operação executada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta de Automação. |
| SourceSystem | O sistema que os logs do Azure Monitor usam para coletar os dados. O valor é sempre Azure para o diagnóstico do Azure. |
| ResultDescription |Descrição que inclui o fluxo de saída do runbook. |
| CorrelationId |O GUID de correlação do trabalho de runbook. |
| ResourceId |A ID de recurso da conta de Automação do Azure do runbook. |
| SubscriptionId | O GUID da assinatura do Azure para a conta de Automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de Automação. |
| ResourceProvider | O provedor de recursos. O valor é MICROSOFT.AUTOMATION. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurar a integração com os logs do Azure Monitor

1. No computador, inicie o Windows PowerShell na tela **Iniciar**.
2. Execute os comandos do PowerShell a seguir e edite os valores para `$automationAccountId` e `$workspaceId` com os valores da seção anterior.

   ```powershell-interactive
   $workspaceId = "resource ID of the log analytics workspace"
   $automationAccountId = "resource ID of your Automation account"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Após a execução do script, pode levar uma hora para você começar a ver registros nos logs do Azure Monitor de novos `JobLogs` ou `JobStreams` gravados.

Para ver os logs, execute a seguinte consulta na pesquisa de logs da análise de logs: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificar a configuração

Para confirmar que sua conta de Automação está enviando logs para o workspace do Log Analytics, verifique se os diagnósticos estão configurados corretamente na conta de Automação usando o comando do PowerShell a seguir.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Na saída, verifique se:

* Em `Logs`, o valor de `Enabled` é True.
* `WorkspaceId` é definido como o valor `ResourceId` para o workspace do Log Analytics.

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Exibir logs de Automação em logs do Azure Monitor

Agora que você começou a enviar seus logs de trabalho de Automação para os logs do Azure Monitor, vamos ver o que é possível fazer com eles nos logs do Azure Monitor.

Para ver os logs, execute a seguinte consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Enviar um email quando um trabalho de runbook falhar ou for suspenso

As etapas a seguir mostram como configurar alertas no Azure Monitor para notificá-lo quando algo der errado com um trabalho de runbook.

Para criar uma regra de alerta, comece criando uma pesquisa de logs para os registros de trabalho de runbook que devem invocar o alerta. Clique no botão **Alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do workspace do Log Analytics, clique em **Exibir logs**.

2. Crie uma consulta de pesquisa de logs para seu alerta digitando a seguinte pesquisa no campo de consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Você também pode agrupar pelo nome do runbook usando: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se você configurou logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por Conta de automação. O nome da conta de Automação pode ser encontrado no campo `Resource` na pesquisa de `JobLogs`.

3. Para abrir a tela **Criar regra**, clique em **Nova Regra de Alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [ Logar alertas no Azure ](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Localizar todos os trabalhos que foram concluídos com erros

Além de alertas de falhas, você pode descobrir quando um trabalho de runbook tem um erro não fatal. Nesses casos, o PowerShell produz um fluxo de erro, mas os erros de não encerramento não fazem com que seu trabalho seja suspenso ou falhe.

1. No workspace do Log Analytics, clique em **Logs**.

2. No campo de consulta, digite `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.

3. Clique no botão **Pesquisar**.

### <a name="view-job-streams-for-a-job"></a>Exibir fluxos de trabalho para um trabalho

Ao depurar um trabalho, também pode ser conveniente examinar os fluxos de trabalho. A consulta abaixo mostra todos os fluxos para um único trabalho com GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Exibir o status do histórico de trabalho

Por fim, você pode visualizar o histórico de trabalhos ao longo do tempo. Você pode usar essa consulta para pesquisar o status dos trabalhos ao longo do tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Gráfico de status de trabalho histórico do Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Remover as configurações de diagnóstico

Para remover a configuração de diagnóstico da conta de Automação, execute o seguinte comando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>Próximas etapas

* Para saber como construir consultas de pesquisa e examinar os logs de trabalho de Automação com os logs do Azure Monitor, confira [Pesquisas de logs em logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
* Para entender como criar e recuperar mensagens de erro e de saída de runbooks, confira [Monitorar a saída do runbook](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Execução de runbook na Automação do Azure](automation-runbook-execution.md).
* Para saber mais sobre os logs do Azure Monitor e as fontes de coleta de dados, confira a [Visão geral sobre a coleta de dados do armazenamento do Azure nos logs do Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
* Para obter ajuda na solução de problemas do Log Analytics, confira [Solucionar problemas se o Log Analytics não está mais coletando dados](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
