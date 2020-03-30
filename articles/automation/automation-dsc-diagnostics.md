---
title: Consulte os registros de configuração do Estado de automação do Azure no Azure Monitor
description: Este artigo demonstra como enviar dados de relatórios de configuração de estado desejado (DSC) da configuração do estado de automação do Azure para logs do Monitor Do Azure para fornecer insights e gerenciamento adicionais.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430713"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Consulte os registros de configuração do Estado de automação do Azure no Azure Monitor

A configuração do estado de automação do Azure retém dados de status do nó por 30 dias.
Você pode enviar dados de status de nó para o espaço de trabalho do Log Analytics se preferir reter esses dados por um período mais longo.
O status de conformidade é visível no portal do Microsoft Azure, ou com o PowerShell, para nós e recursos individuais de DSC em configurações de nó.
Com os registros do Monitor do Azure, você pode:

- Obter informações de conformidade para nós gerenciados e recursos individuais
- Disparar um email ou alerta com base no status de conformidade
- Escrever consultas avançadas em seus nós gerenciados
- Correlacionar o status de conformidade em contas de Automação
- Visualizar o histórico de conformidade do nó ao longo do tempo

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus relatórios de configuração do estado de automação para logs do Monitor do Azure, você precisa:

- Versão de novembro 2016 ou posterior do [Azure PowerShell](/powershell/azure/overview) ( versão 2.3.0).
- Uma conta de Automação do Azure. Para obter mais informações, consulte [Uma introdução ao Azure Automation](automation-intro.md).
- Um espaço de trabalho log analytics com uma oferta de serviço de automação & control. Para obter mais informações, confira [Introdução ao Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- No mínimo, um nó de Configuração do Estado de Automação do Azure. Para obter mais informações, consulte [máquinas de onboarding para gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md).
- O módulo [xDscDiagnostics,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) versão 2.7.0.0 ou superior. Para as etapas de instalação, consulte [Solução de problemas Configuração do estado desejado do Azure Automação](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configure a integração com os logs do Monitor do Azure

Para começar a importar dados do Azure Automation DSC para logs do Monitor do Azure, complete as seguintes etapas:

1. Faça logon em sua conta do Azure no PowerShell. Veja [Entrar com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Obtenha o ID de recurso da sua conta de automação executando o seguinte cmdlet PowerShell. Se você tiver mais de uma conta de automação, escolha o ID de recurso para a conta que deseja configurar.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenha o ID de recurso do seu espaço de trabalho do Log Analytics executando o seguinte cmdlet PowerShell. Se você tiver mais de um espaço de trabalho, escolha o ID de recurso para o espaço de trabalho que deseja configurar.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Execute o cmdlet PowerShell `<AutomationResourceId>` a `<WorkspaceResourceId>` seguir, substituindo e com os valores *ResourceId* de cada uma das etapas anteriores.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se você quiser parar de importar dados da configuração do estado de automação do Azure em logs do Monitor do Azure, execute o cmdlet do PowerShell a seguir.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Exibir os logs de configuração de estado

Depois de configurar a integração com os logs do Monitor do Azure para os dados de configuração do estado de automação, você pode visualizá-los selecionando **Logs** na seção **Monitoramento** na página dsc (left pane of the State configuration).

![Logs](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

O **painel Pesquisa** de log é aberto com uma região de consulta escopo para o recurso da conta de Automação. Você pode pesquisar os registros de configuração do estado para operações de DSC pesquisando nos registros do Monitor do Azure. Os registros das operações de DSC são armazenados na tabela AzureDiagnostics. Por exemplo, para encontrar nós que não estejam em conformidade, digite a seguinte consulta.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Detalhes de filtragem:

* Filtrar *em DscNodeStatusData* para retornar as operações para cada nó de configuração do estado.
* Filtrar no *DscResourceStatusData* para retornar as operações de cada recurso DSC chamado na configuração de nó aplicada a esse recurso. 
* Filtre *no DscResourceStatusData* para retornar as informações de erro para quaisquer recursos DSC que falham.

Para saber mais sobre a construção de consultas de log para encontrar dados, consulte [Visão geral das consultas de log no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Enviar um email quando uma verificação de conformidade da Configuração do Estado falhar

Uma das principais solicitações de nossos clientes é a capacidade de enviar um email ou uma mensagem de texto quando algo dá errado com uma configuração de DSC.

Para criar uma regra de alerta, inicie criando uma pesquisa de log para os registros do relatório de configuração do estado que devem invocar o alerta. Clique no botão ** + Nova Regra de Alerta ** para criar e configurar a regra de alerta.

1. Na página 'Visão geral do espaço de trabalho' log analytics, clique **em Logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se você tiver configurado logs de mais de uma Conta de automação ou assinatura para o workspace, também poderá agrupar os alertas por assinatura e por conta de Automação. Obtenha o nome da conta Automação do campo Recurso na pesquisa do DscNodeStatusData.
1. Para abrir a tela ** Criar regra **, clique em ** + Nova regra de alerta ** na parte superior da página. 

Para obter mais informações sobre as opções para configurar o alerta, consulte [Criar uma regra de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontrar recursos DSC com falha em todos os nós

Uma vantagem de usar os registros do Monitor Do Azure é que você pode procurar verificações com falha em nenhum nó.
Para encontrar todas as instâncias dos recursos do DSC que falharam:

1. Na página Visão geral do espaço de trabalho do Log Analytics, clique **em Logs**.
1. Crie uma consulta de pesquisa de log para o alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Exibir status do nó DSC histórico

Para visualizar o histórico de status do nó DSC ao longo do tempo, você pode usar esta consulta:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Esta consulta exibe um gráfico do status do nó ao longo do tempo.

## <a name="azure-monitor-logs-records"></a>Registros de registros do Azure Monitor

Os diagnósticos do Azure Automation criam duas categorias de registros nos registros do Azure Monitor:

* Dados de status do nó (DscNodeStatusData)
* Dados de status de recursos (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscNodeStatusData. |
| ResultType |Se o nó está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| NodeComplianceStatus_s |Se o nó está em conformidade. |
| DscReportStatus |Se a verificação de conformidade foi executada com êxito. |
| ConfigurationMode | Como a configuração é aplicada ao nó. Os valores possíveis são: <ul><li>*ApplyOnly*: o DSC aplica-se à configuração e não faz nada além disso, a menos que uma nova configuração seja enviada para o nó de destino ou quando é efetuado pull de uma nova configuração de um servidor. Depois da aplicação inicial de uma nova configuração, o DSC não procura descompasso de um estado previamente configurado. O DSC tenta aplicar a configuração até que ela seja bem sucedida antes que o valor *ApplyOnly* entre em vigor. </li><li>*ApplyAndMonitor*: Este é o valor padrão. O LCM aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs. O DSC tenta aplicar a configuração até que ela seja bem sucedida antes que o valor *ApplyAndMonitor* entre em vigor.</li><li>*ApplyAndAutoCorrect*: a DSC aplica as novas configurações. Depois da aplicação inicial de uma nova configuração, se o nó de destino tiver um descompasso do estado desejado, o DSC relatará a discrepância nos logs e, em seguida, reaplicará a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerenciado. |
| IPAddress | O endereço IPv4 do nó gerenciado. |
| Categoria | DscNodeStatus. |
| Recurso | O nome da conta de Automação do Azure. |
| Tenant_g | GUID que identifica o locatário para o Chamador. |
| NodeId_g |A GUID que identifica o nó gerenciado. |
| DscReportId_g |A GUID que identifica o relatório. |
| LastSeenTime_t |Data e hora em que o relatório foi exibido pela última vez. |
| ReportStartTime_t |Data e hora em que o relatório foi iniciado. |
| ReportEndTime_t |Data e hora em que o relatório foi concluído. |
| NumberOfResources_d |O número de recursos de DSC chamado na configuração aplicada ao nó. |
| SourceSystem | Como os registros do Monitor Do Azure coletaram os dados. Sempre "Azure" para diagnósticos do Azure. |
| ResourceId |Identificador da conta Azure Automation. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | O ID de assinatura do Azure (GUID) para a conta automação. |
| ResourceGroup | Nome do grupo de recursos para a Conta de automação. |
| ResourceProvider | Microsoft. Automação. |
| ResourceType | CONTAS DE AUTOMAÇÃO. |
| CorrelationId |GUID que é o identificador de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscResourceStatusData|
| ResultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| Categoria | DscNodeStatus. |
| Recurso | O nome da conta de Automação do Azure. |
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
| SourceSystem | Como os registros do Monitor Do Azure coletaram os dados. Sempre *Azure* para o Diagnóstico do Azure. |
| ResourceId |Especifica a conta de Automação do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | O ID de assinatura do Azure (GUID) para a conta automação. |
| ResourceGroup | Nome do grupo de recursos para a Conta de automação. |
| ResourceProvider | Microsoft. Automação. |
| ResourceType | CONTAS DE AUTOMAÇÃO. |
| CorrelationId |GUID que é o ID de Correlação do relatório de conformidade. |

## <a name="summary"></a>Resumo

Ao enviar seus dados de configuração do estado de automação para logs do Monitor do Azure, você pode obter uma melhor visão sobre o status dos seus nós de configuração do estado de automação por:

- Configurando alertas para notificá-lo quando houver um problema
- Usando exibições personalizadas e consultas de pesquisa para visualizar os resultados de runbook, o status do trabalho de runbook e outros principais indicadores ou métricas relacionadas.

Os registros do Monitor do Azure fornecem maior visibilidade operacional aos dados de configuração do estado de automação e podem ajudar a resolver incidentes mais rapidamente.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [Configuração de Estado da Automação do Azure](automation-dsc-overview.md)
- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
- Para saber mais sobre como construir diferentes consultas de pesquisa e revisar os logs de configuração do estado de automação com logs do Monitor do Azure, consulte [pesquisas de log nos registros do Monitor do Azure](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre os registros do Azure Monitor e as fontes de coleta de dados, consulte [Collecting Azure storage data in Azure Monitor logs overview](../azure-monitor/platform/collect-azure-metrics-logs.md)
