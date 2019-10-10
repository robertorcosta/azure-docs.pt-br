---
title: Encaminhe os dados do relatório de configuração de estado da automação do Azure para Azure Monitor logs
description: Este artigo demonstra como enviar dados de relatório de DSC (configuração de estado desejado) da configuração de estado de automação do Azure para Azure Monitor logs para fornecer informações e gerenciamento adicionais.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5905afdb9832f32e837dc4496e4a951fca41b8b0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243544"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Encaminhe os dados do relatório de configuração de estado da automação do Azure para Azure Monitor logs

A configuração de estado da automação do Azure retém os dados de status do nó por 30 dias.
Você pode enviar dados de status do nó para seu espaço de trabalho do Log Analytics se preferir manter esses dados por um período mais longo.
O status de conformidade é visível no portal do Microsoft Azure, ou com o PowerShell, para nós e recursos individuais de DSC em configurações de nó.
Com os logs de Azure Monitor, você pode:

- Obter informações de conformidade para nós gerenciados e recursos individuais
- Disparar um email ou alerta com base no status de conformidade
- Escrever consultas avançadas em seus nós gerenciados
- Correlacionar o status de conformidade em contas de Automação
- Visualizar o histórico de conformidade do nó ao longo do tempo

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus relatórios de configuração de estado de automação para logs de Azure Monitor, você precisa:

- Versão de novembro 2016 ou posterior do [Azure PowerShell](/powershell/azure/overview) ( versão 2.3.0).
- Uma conta de Automação do Azure. Para saber mais, veja [Introdução à Automação do Azure](automation-offering-get-started.md)
- Um espaço de trabalho do Log Analytics com uma oferta de serviço **Automação e Controle**. Para obter mais informações, consulte Introdução [aos logs de Azure monitor](../log-analytics/log-analytics-get-started.md).
- No mínimo, um nó de Configuração do Estado de Automação do Azure. Para saber mais, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)
- O módulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) , versão 2.7.0.0 ou superior. Para obter as etapas de instalação, consulte [Exibir logs de DSC em seu nó](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurar a integração com os logs de Azure Monitor

Para começar a importar dados do Azure DSC de Automação para Azure Monitor logs, conclua as seguintes etapas:

1. Faça logon em sua conta do Azure no PowerShell. Veja [Fazer logon com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Obtenha o _ResourceId_ de sua conta de automação executando o seguinte comando do PowerShell: (se você tiver mais de uma conta de automação, escolha o _ResourceID_ para a que deseja configurar).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenha o _ResourceId_ de seu espaço de trabalho do Log Analytics executando o seguinte comando do PowerShell: (se você tiver mais de um espaço de trabalho, escolha o _ResourceID_ para o que deseja configurar).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Execute o seguinte comando do PowerShell, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` pelos valores de _ResourceId_ de cada uma das etapas anteriores:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

Se você quiser parar de importar dados da configuração de estado da automação do Azure para logs de Azure Monitor, execute o seguinte comando do PowerShell:

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Exibir os logs de configuração de estado

Depois de configurar a integração com os logs de Azure Monitor para os dados de configuração de estado de automação, um botão de **pesquisa de log** será exibido na folha **nós DSC** da sua conta de automação. Clique no botão **Pesquisa de Log** para exibir os logs para dados do nó DSC.

![Botão Pesquisar Log](media/automation-dsc-diagnostics/log-search-button.png)

A folha **Pesquisa de Log** é aberta e você verá uma operação **DscNodeStatusData** para cada nó da Configuração do Estado e uma operação **DscResourceStatusData** para cada [Recurso DSC](/powershell/scripting/dsc/resources/resources) chamado na configuração do nó aplicada a esse nó.

A operação **DscResourceStatusData** contém informações de erro para todos os recursos DSC que falharam.

Clique em cada operação na lista para ver os dados para essa operação.

Você também pode exibir os logs pesquisando em logs de Azure Monitor.
Veja [Localizar dados usando pesquisas de logs](../log-analytics/log-analytics-log-searches.md).
Digite a consulta a seguir para localizar os logs da Configuração do Estado: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Você também pode restringir a consulta pelo nome da operação. Por exemplo: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Enviar um email quando uma verificação de conformidade da Configuração do Estado falhar

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado com uma configuração de DSC.

Para criar uma regra de alerta, você começa criando uma pesquisa de log para os registros do relatório da Configuração do Estado que devem invocar o alerta. Clique no botão **+ Nova Regra de Alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do espaço de trabalho Log Analytics, clique em **logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se você tiver configurado logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por conta de Automação.
   O nome da conta de Automação pode ser derivado do campo de Recurso na pesquisa de DscNodeStatusData.
1. Para abrir a tela **Criar regra**, clique em **+ Nova regra de alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [criar uma regra de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontrar recursos DSC com falha em todos os nós

Uma vantagem de usar os logs de Azure Monitor é que você pode procurar verificações com falha nos nós.
Para localizar todas as instâncias de recursos DSC que falharam.

1. Na página Visão geral do espaço de trabalho Log Analytics, clique em **logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Exibir status do nó DSC histórico

Finalmente, talvez você queira visualizar o histórico de status do nó DSC ao longo do tempo.
Você pode usar essa consulta para pesquisar o status do nó DSC ao longo do tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Isso exibirá um gráfico do status do nó ao longo do tempo.

## <a name="azure-monitor-logs-records"></a>Registros de logs de Azure Monitor

O diagnóstico da automação do Azure cria duas categorias de registros em logs de Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | DESCRIÇÃO |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscNodeStatusData |
| ResultType |Se o nó está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| NodeComplianceStatus_s |Se o nó está em conformidade. |
| DscReportStatus |Se a verificação de conformidade foi executada com êxito. |
| ConfigurationMode | Como a configuração é aplicada ao nó. Os valores possíveis são __"ApplyOnly"__ , __"ApplyandMonitior"__ e __"ApplyandAutoCorrect"__ . <ul><li>__ApplyOnly__: O DSC somente aplica a configuração e nada mais, exceto se uma nova configuração for enviada por push ao nó de destino ou quando uma nova configuração é efetuada por pull de um servidor. Depois da aplicação inicial de uma nova configuração, o DSC não procura descompasso de um estado previamente configurado. O DSC tenta aplicar a configuração até que seja bem-sucedida antes __ApplyOnly__ entrar em vigor. </li><li> __ApplyAndMonitor__: Este é o valor padrão. O LCM aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs. O DSC tenta aplicar a configuração até que seja bem-sucedida antes __ApplyAndMonitor__ entrar em vigor.</li><li>__ApplyAndAutoCorrect__: O DSC aplica novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs e, em seguida, reaplicará a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerenciado. |
| IPAddress | O endereço IPv4 do nó gerenciado. |
| Categoria | DscNodeStatus |
| Resource | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário para o Chamador. |
| NodeId_g |A GUID que identifica o nó gerenciado. |
| DscReportId_g |A GUID que identifica o relatório. |
| LastSeenTime_t |Data e hora em que o relatório foi exibido pela última vez. |
| ReportStartTime_t |Data e hora em que o relatório foi iniciado. |
| ReportEndTime_t |Data e hora em que o relatório foi concluído. |
| NumberOfResources_d |O número de recursos de DSC chamado na configuração aplicada ao nó. |
| SourceSystem | Como os logs de Azure Monitor coletaram os dados. Sempre *Azure* para o Diagnóstico do Azure. |
| ResourceId |Especifica a conta de Automação do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | O GUID (ID de assinatura do Azure) para a Conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a Conta de automação. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |O GUID que é a Id de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | DESCRIÇÃO |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscResourceStatusData|
| ResultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| Categoria | DscNodeStatus |
| Resource | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário para o Chamador. |
| NodeId_g |A GUID que identifica o nó gerenciado. |
| DscReportId_g |A GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância do recurso de DSC. |
| DscResourceName_s |O nome do recurso de DSC. |
| DscResourceStatus_s |Se o recurso de DSC está em conformidade. |
| DscModuleName_s |O nome do módulo do PowerShell que contém o recurso de DSC. |
| DscModuleVersion_s |A versão do módulo do PowerShell que contém o recurso de DSC. |
| DscConfigurationName_s |O nome da configuração aplicada ao nó. |
| ErrorCode_s | O código de erro se o recurso tiver falhado. |
| ErrorMessage_s |A mensagem de erro se o recurso tiver falhado. |
| DscResourceDuration_d |O tempo, em segundos, em que o recurso de DSC foi executado. |
| SourceSystem | Como os logs de Azure Monitor coletaram os dados. Sempre *Azure* para o Diagnóstico do Azure. |
| ResourceId |Especifica a conta de Automação do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | O GUID (ID de assinatura do Azure) para a Conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a Conta de automação. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |O GUID que é a Id de correlação do relatório de conformidade. |

## <a name="summary"></a>Resumo

Ao enviar seus dados de configuração de estado de automação para logs de Azure Monitor, você pode obter uma melhor percepção do status de seus nós de configuração de estado de automação:

- Configurando alertas para notificá-lo quando houver um problema
- Usando exibições personalizadas e consultas de pesquisa para visualizar os resultados de runbook, o status do trabalho de runbook e outros principais indicadores ou métricas relacionadas.

Os logs de Azure Monitor fornecem maior visibilidade operacional para os dados de configuração do estado de automação e podem ajudar a resolver incidentes mais rapidamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [Configuração de Estado da Automação do Azure](automation-dsc-overview.md)
- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
- Para saber mais sobre como construir diferentes consultas de pesquisa e examinar os logs de configuração de estado de automação com logs de Azure Monitor, consulte [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre os logs de Azure Monitor e fontes de coleta de dados, confira [visão geral sobre como coletar dados do armazenamento do Azure em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md)
