---
title: Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor
description: Este artigo demonstra como enviar o status do trabalho e os fluxos de trabalho do runbook para Azure Monitor logs para fornecer informações e gerenciamento adicionais.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457681"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Encaminhe o status do trabalho e os fluxos de trabalho da automação para os logs de Azure Monitor

A Automação pode enviar status de trabalho do runbook e fluxos de trabalho para o espaço de trabalho do Log Analytics. Esse processo não envolve a vinculação de workspace e é completamente independente. Os logs e fluxos de trabalho podem ser vistos no portal do Azure ou com o PowerShell, no caso de trabalhos individuais, e isso permite a você fazer investigações simples. Agora, com os logs de Azure Monitor, você pode:

* Obtenha informações sobre o status de seus trabalhos de automação.
* Disparar um email ou um alerta com base no status de trabalho de runbook (por exemplo, com falha ou suspenso).
* Escrever consultas avançadas em seus fluxos de trabalho.
* Correlacionar trabalhos em Contas de automação.
* Use exibições personalizadas e consultas de pesquisa para visualizar os resultados do runbook, o status do trabalho de runbook e outros indicadores ou métricas de chave relacionados.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implantação

Para começar a enviar seus logs de automação para Azure Monitor logs, você precisa:

* A versão mais recente do [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Um espaço de trabalho do Log Analytics. Para obter mais informações, consulte Introdução [aos logs de Azure monitor](../log-analytics/log-analytics-get-started.md).
* A ID de recurso para sua conta de automação do Azure.

Use o seguinte comando para localizar a ID de recurso para sua conta de automação do Azure:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para localizar a ID de recurso para seu espaço de trabalho Log Analytics, execute o seguinte comando do PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se você tiver mais de uma conta de automação ou espaço de trabalho na saída dos comandos anteriores, localize o nome que você precisa configurar e copie o valor para a ID de recurso.

1. Na portal do Azure, selecione sua conta de automação na folha da **conta de automação** e selecione todas as **configurações**. 
2. Na folha **todas as configurações** , em **configurações da conta**, selecione **Propriedades**.  
3. Na folha **Propriedades** , observe as propriedades mostradas abaixo.

    ![Propriedades da conta de automação](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor

O diagnóstico de automação do Azure cria dois tipos de registros em logs de `AzureDiagnostics`Azure monitor, marcados como. As tabelas nas próximas seções são exemplos de registros que a automação do Azure gera e os tipos de dados que aparecem nos resultados da pesquisa de log.

### <a name="job-logs"></a>Logs de trabalho

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |GUID que identifica o trabalho de runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- Novo<br>-Criado<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Com falha<br>– Concluído |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobLogs. |
| OperationName | O tipo de operação executada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta de automação |
| SourceSystem | O sistema que Azure Monitor logs usa para coletar os dados. O valor é sempre o Azure para diagnóstico do Azure. |
| ResultDescription |O estado do resultado do trabalho de runbook. Os valores possíveis são:<br>- O trabalho foi iniciado<br>- O trabalho falhou<br>- Trabalho Concluído |
| CorrelationId |O GUID de correlação do trabalho de runbook. |
| ResourceId |A ID de recurso da conta de automação do Azure do runbook. |
| SubscriptionId | O GUID da assinatura do Azure para a conta de automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de automação. |
| ResourceProvider | O provedor de recursos. O valor é MICROSOFT. Inicia. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Transmissões de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho de runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de email ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Andamento<br>- Saída<br>- Aviso<br>- Erro<br>- Depurar<br>- Detalhado |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |GUID que identifica o trabalho de runbook. |
| ResultType |O status do trabalho de runbook. Os valores possíveis são:<br>- em andamento |
| Categoria | Classificação do tipo de dados. Para a Automação, o valor é JobStreams. |
| OperationName | Tipo de operação executada no Azure. Para a Automação, o valor é Job. |
| Recurso | O nome da conta de automação. |
| SourceSystem | O sistema que Azure Monitor logs usa para coletar os dados. O valor é sempre o Azure para diagnóstico do Azure. |
| ResultDescription |Descrição que inclui o fluxo de saída do runbook. |
| CorrelationId |O GUID de correlação do trabalho de runbook. |
| ResourceId |A ID de recurso da conta de automação do Azure do runbook. |
| SubscriptionId | O GUID da assinatura do Azure para a conta de automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de automação. |
| ResourceProvider | O provedor de recursos. O valor é MICROSOFT. Inicia. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Configurando a integração com os logs de Azure Monitor

1. Em seu computador, inicie o Windows PowerShell na tela **Inicial**.
2. Execute os comandos do PowerShell a seguir e edite os `[your resource ID]` valores `[resource ID of the log analytics workspace]` de e com os valores da seção anterior.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Depois de executar esse script, pode levar uma hora antes de começar a ver registros em Azure Monitor logs de novo `JobLogs` ou `JobStreams` que estão sendo gravados.

Para ver os logs, execute a seguinte consulta na pesquisa de logs do log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificar a configuração

Para confirmar que sua conta de automação está enviando logs para seu espaço de trabalho do Log Analytics, verifique se os diagnósticos estão configurados corretamente na conta de automação usando o comando do PowerShell a seguir.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Na saída, verifique se:

* Em `Logs`, o valor de `Enabled` é true.
* `WorkspaceId`é definido como o `ResourceId` valor para seu espaço de trabalho de log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Exibindo logs de automação em logs de Azure Monitor

Agora que você começou a enviar seus logs de trabalho de automação para Azure Monitor logs, vamos ver o que você pode fazer com esses logs dentro de logs de Azure Monitor.

Para ver os logs, execute a seguinte consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Enviar um email quando um trabalho de runbook falhar ou for suspenso

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado em um trabalho de runbook.

Para criar uma regra de alerta, comece criando uma pesquisa de log para os registros de trabalho de runbook que devem invocar o alerta. Clique no botão **Alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do espaço de trabalho Log Analytics, clique em **Exibir logs**.
2. Crie uma consulta de pesquisa de logs para seu alerta digitando a seguinte pesquisa no campo de consulta:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Você também pode agrupar pelo nome do runbook usando:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se você configurou logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por Conta de automação. O nome da conta de automação pode ser `Resource` encontrado no campo na pesquisa `JobLogs`de.
3. Para abrir a tela **criar regra** , clique em **nova regra de alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [ Logar alertas no Azure ](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Localizar todos os trabalhos que foram concluídos com erros

Além de alertas de falhas, você pode descobrir quando um trabalho de runbook tem um erro não fatal. Nesses casos, o PowerShell produz um fluxo de erro, mas os erros de não encerramento não fazem com que seu trabalho seja suspenso ou falhe.

1. No espaço de trabalho Log Analytics, clique em **logs**.
2. No campo de consulta, digite `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.
3. Clique no botão **Pesquisar** .

### <a name="view-job-streams-for-a-job"></a>Exibir fluxos de trabalho para um trabalho

Quando estiver depurando um trabalho, talvez você também queira examinar os fluxos de trabalho. A consulta abaixo mostra todos os fluxos para um único trabalho com GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Exibir o status do histórico de trabalho

Por fim, talvez você queira Visualizar o histórico do trabalho ao longo do tempo. Você pode usar essa consulta para pesquisar o status dos trabalhos ao longo do tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Gráfico de status de trabalho histórico do Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Removendo configurações de diagnóstico

Para remover a configuração de diagnóstico da conta de automação, execute o seguinte comando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Próximas etapas

* Para obter ajuda para solução de problemas Log Analytics, consulte [Solucionando problemas por que log Analytics não está mais coletando dados](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Para saber mais sobre como construir diferentes consultas de pesquisa e examinar os logs de trabalho de automação com logs de Azure Monitor, consulte [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md).
* Para entender como criar e recuperar mensagens de saída e de erro de runbooks, consulte [saída e mensagens de runbook](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md).
* Para saber mais sobre os logs de Azure Monitor e fontes de coleta de dados, consulte [visão geral sobre coleta de dados do armazenamento do Azure em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).