---
title: Encaminhe os dados do relatório de configuração de estado da automação do Azure para Azure Monitor logs
description: Este artigo demonstra como enviar dados de relatório de DSC (configuração de estado desejado) da configuração de estado de automação do Azure para Azure Monitor logs para fornecer informações e gerenciamento adicionais.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: badd8ba676ef25c33a5034bb04d616faeb4ef1b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392108"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Encaminhe os dados do relatório de configuração de estado da automação do Azure para Azure Monitor logs

A configuração de estado da automação do Azure retém os dados de status do nó por 30 dias. Você pode enviar dados de status do nó para seu espaço de trabalho do Log Analytics se preferir manter esses dados por um período mais longo. O status de conformidade é visível no portal do Microsoft Azure, ou com o PowerShell, para nós e recursos individuais de DSC em configurações de nó. 

Os logs de Azure Monitor fornecem maior visibilidade operacional para os dados de configuração do estado de automação e podem ajudar a resolver incidentes mais rapidamente. Com os logs de Azure Monitor, você pode:

- Obtenha informações de conformidade para nós gerenciados e recursos individuais.
- Dispare um email ou alerta com base no status de conformidade.
- Grave consultas avançadas em seus nós gerenciados.
- Correlacione o status de conformidade nas contas de automação.
- Use exibições personalizadas e consultas de pesquisa para visualizar os resultados do runbook, o status do trabalho de runbook e outros indicadores ou métricas de chave relacionados.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus relatórios de configuração de estado de automação para logs de Azure Monitor, você precisa:

- Versão de novembro 2016 ou posterior do [Azure PowerShell](/powershell/azure/overview) ( versão 2.3.0).
- Uma conta de Automação do Azure. Para obter mais informações, consulte [uma introdução à automação do Azure](automation-intro.md).
- Um espaço de trabalho Log Analytics com uma oferta de serviço de controle de & de automação. Para obter mais informações, confira [Introdução ao Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- No mínimo, um nó de Configuração do Estado de Automação do Azure. Para obter mais informações, consulte [máquinas de integração para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md).
- O módulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) , versão 2.7.0.0 ou superior. Para as etapas de instalação, consulte [solucionar problemas de configuração de estado desejado da automação do Azure](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurar a integração com os logs de Azure Monitor

Para começar a importar dados da configuração de estado da automação do Azure para logs de Azure Monitor, conclua as seguintes etapas:

1. Faça logon em sua conta do Azure no PowerShell. Consulte [entrar com Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Obtenha a ID de recurso da sua conta de automação executando o seguinte cmdlet do PowerShell. Se você tiver mais de uma conta de automação, escolha a ID de recurso da conta que deseja configurar.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenha a ID de recurso do seu espaço de trabalho Log Analytics executando o seguinte cmdlet do PowerShell. Se você tiver mais de um espaço de trabalho, escolha a ID de recurso para o espaço de trabalho que deseja configurar.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Execute o seguinte cmdlet do PowerShell, `<AutomationResourceId>` substituindo `<WorkspaceResourceId>` e `ResourceId` pelos valores de cada uma das etapas anteriores.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se você quiser parar de importar dados da configuração de estado da automação do Azure para logs de Azure Monitor, execute o seguinte cmdlet do PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Exibir os logs de configuração de estado

Depois de configurar a integração com os logs de Azure Monitor para os dados de configuração de estado de automação, você pode exibi-los selecionando **logs** na seção **monitoramento** no painel esquerdo da página de configuração de estado (DSC).

![Logs](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

O painel pesquisa de log é aberto com uma região de consulta com escopo definido para o recurso de conta de automação. Você pode pesquisar os logs de configuração de estado para operações de DSC pesquisando em logs de Azure Monitor. Os registros de operações de DSC são armazenados na `AzureDiagnostics` tabela. Por exemplo, para localizar nós que não são compatíveis, digite a consulta a seguir.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Detalhes da filtragem:

* Filtre `DscNodeStatusData` para retornar operações para cada nó de configuração de estado.
* Filtre `DscResourceStatusData` para retornar operações para cada recurso de DSC chamado na configuração de nó aplicada a esse recurso. 
* Filtre `DscResourceStatusData` para retornar informações de erro para todos os recursos de DSC que falham.

Para saber mais sobre como construir consultas de log para localizar dados, consulte [visão geral das consultas de log no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Enviar um email quando uma verificação de conformidade da Configuração do Estado falhar

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado com uma configuração de DSC.

Para criar uma regra de alerta, comece criando uma pesquisa de log para os registros de relatório de configuração de estado que devem invocar o alerta. Clique no botão **nova regra de alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do espaço de trabalho Log Analytics, clique em **logs**.
1. Crie uma consulta de pesquisa de logs para seu alerta digitando a seguinte pesquisa no campo de consulta:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se você tiver configurado logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por conta de Automação. Derive o nome da conta de `Resource` automação do campo na pesquisa dos `DscNodeStatusData` registros.
1. Para abrir a tela **criar regra** , clique em **nova regra de alerta** na parte superior da página. 

Para obter mais informações sobre as opções para configurar o alerta, consulte [criar uma regra de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontrar recursos DSC com falha em todos os nós

Uma vantagem de usar os logs de Azure Monitor é que você pode procurar verificações com falha nos nós. Para localizar todas as instâncias de recursos de DSC que falharam:

1. Na página Visão geral do Log Analytics espaço de trabalho, clique em **logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Exibir status do nó DSC histórico

Para visualizar o histórico de status do nó DSC ao longo do tempo, você pode usar esta consulta:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Essa consulta exibe um gráfico do status do nó ao longo do tempo.

## <a name="azure-monitor-logs-records"></a>Registros de logs de Azure Monitor

O diagnóstico de automação do Azure cria duas categorias de registros em logs de Azure Monitor:

* Dados de status do`DscNodeStatusData`nó ()
* Dados de status do`DscResourceStatusData`recurso ()

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Valor que indica se o nó está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| NodeComplianceStatus_s |Valor de status que especifica se o nó é compatível. |
| DscReportStatus |Valor de status indicando se a verificação de conformidade foi executada com êxito. |
| ConfigurationMode | O modo usado para aplicar a configuração ao nó. Os valores possíveis são: <ul><li>`ApplyOnly`: O DSC aplica a configuração e não faz nada além disso, a menos que uma nova configuração seja enviada por push ao nó de destino ou quando uma nova configuração for retirada de um servidor. Depois da aplicação inicial de uma nova configuração, o DSC não procura descompasso de um estado previamente configurado. O DSC tenta aplicar a configuração até que ela seja bem-sucedida antes `ApplyOnly` que o valor entre em vigor. </li><li>`ApplyAndMonitor`: é o valor padrão. O LCM aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs. O DSC tenta aplicar a configuração até que ela seja bem-sucedida antes `ApplyAndMonitor` que o valor entre em vigor.</li><li>`ApplyAndAutoCorrect`: O DSC aplica-se a novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs e, em seguida, reaplicará a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerenciado. |
| IPAddress | O endereço IPv4 do nó gerenciado. |
| Categoria | `DscNodeStatus`. |
| Recurso | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário do chamador. |
| NodeId_g | A GUID que identifica o nó gerenciado. |
| DscReportId_g | A GUID que identifica o relatório. |
| LastSeenTime_t | Data e hora em que o relatório foi exibido pela última vez. |
| ReportStartTime_t | Data e hora em que o relatório foi iniciado. |
| ReportEndTime_t | Data e hora em que o relatório foi concluído. |
| NumberOfResources_d | O número de recursos de DSC chamado na configuração aplicada ao nó. |
| SourceSystem | O sistema de origem que identifica como os logs de Azure Monitor coletaram os dados. Sempre `Azure` para o diagnóstico do Azure. |
| ResourceId |O identificador de recurso da conta de automação do Azure. |
| ResultDescription | A descrição do recurso para esta operação. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de automação. |
| ResourceProvider | O. Inicia. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Um GUID que é o identificador de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| Categoria | DscNodeStatus. |
| Recurso | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário do chamador. |
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
| SourceSystem | Como os logs de Azure Monitor coletaram os dados. Sempre `Azure` para o diagnóstico do Azure. |
| ResourceId |O identificador da conta de automação do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de automação. |
| ResourceGroup | O nome do grupo de recursos para a conta de automação. |
| ResourceProvider | O. Inicia. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID que é a ID de correlação do relatório de conformidade. |


## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [configuração de estado da automação do Azure](automation-dsc-overview.md).
- Para começar, consulte [introdução à configuração de estado da automação do Azure](automation-dsc-getting-started.md).
- Para saber mais sobre como compilar configurações de DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado de automação do Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet do PowerShell, consulte [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md).
- Para saber mais sobre como construir diferentes consultas de pesquisa e examinar os logs de configuração de estado de automação com logs de Azure Monitor, consulte [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md).
- Para saber mais sobre os logs de Azure Monitor e fontes de coleta de dados, consulte [visão geral sobre coleta de dados do armazenamento do Azure em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).
