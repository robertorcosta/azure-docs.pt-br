---
title: Arquivo & relatório com o Azure Monitor - Gerenciamento de direitos azure AD
description: Saiba como arquivar logs e criar relatórios com o Azure Monitor no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 070b7c5e0fef7d50f84271190432a65d29699bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128632"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Registros de arquivos e relatórios sobre o gerenciamento de direitos do Azure AD no Azure Monitor

O Azure AD armazena eventos de auditoria por até 30 dias no registro de auditoria. No entanto, você pode manter os dados da auditoria por mais tempo do que o período de retenção padrão, descrito em [Quanto tempo o Azure AD armazena dados de relatórios?](../reports-monitoring/reference-reports-data-retention.md) Em seguida, você pode usar pastas de trabalho e consultas personalizadas e relatórios sobre esses dados.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Configure o Azure AD para usar o Monitor Do Azure
Antes de usar as regras de trabalho do Azure Monitor, você deve configurar o Azure AD para enviar uma cópia de seus registros de auditoria para o Azure Monitor.

O arquivamento de registros de auditoria do Azure AD exige que você tenha o Azure Monitor em uma assinatura do Azure. Você pode ler mais sobre os pré-requisitos e os custos estimados do uso do Azure Monitor no [Azure AD logs de atividade no Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Papel pré-requisito**: Admin Global

1. Faça login no portal Azure como um usuário que é um administrador global. Certifique-se de ter acesso ao grupo de recursos que contém o espaço de trabalho do Monitor Do Azure.
 
1. Selecione **O Diretório Ativo do Azure** e clique em **Configurações de diagnóstico** em Monitoramento no menu de navegação à esquerda. Verifique se já existe uma configuração para enviar os registros de auditoria para esse espaço de trabalho.

1. Se ainda não houver uma configuração, clique em **Adicionar configuração de diagnóstico**. Use as instruções do artigo Integre os [logs do Azure AD com os logs do Monitor Do Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) para enviar o registro de auditoria do Azure AD para o espaço de trabalho do Monitor Do Azure.

    ![Painel Configurações de diagnóstico](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Depois que o log for enviado ao Azure Monitor, selecione **os espaços de trabalho do Log Analytics**e selecione o espaço de trabalho que contém os logs de auditoria do Azure AD.

1. Selecione **Uso e custos estimados** e clique **em Retenção de Dados**. Altere o controle deslizante para o número de dias que deseja manter os dados para atender aos seus requisitos de auditoria.

    ![Painel de espaços de trabalho do Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Crie consultas personalizadas do Azure Monitor usando o portal Azure
Você pode criar suas próprias consultas em eventos de auditoria Azure AD, incluindo eventos de gerenciamento de direitos.  

1. No Azure Active Directory do portal Azure, clique em **Logs** na seção Monitoramento no menu de navegação à esquerda para criar uma nova página de consulta.

1. Seu espaço de trabalho deve ser mostrado no canto superior esquerdo da página de consulta. Se você tiver vários espaços de trabalho do Azure Monitor e o espaço de trabalho que você está usando para armazenar eventos de auditoria AD do Azure não for mostrado, clique em **Selecionar escopo**. Em seguida, selecione a assinatura e o espaço de trabalho corretos.

1. Em seguida, na área de texto da consulta, exclua a string "search *" e substitua-a pela seguinte consulta:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Em seguida, clique em **Executar**. 

    ![Clique em Executar para iniciar a consulta](./media/entitlement-management-logs-and-reporting/run-query.png)

A tabela mostrará os eventos do registro de auditoria para o gerenciamento de direitos da última hora por padrão. Você pode alterar a configuração "Intervalo de tempo" para visualizar eventos mais antigos. No entanto, alterar essa configuração só mostrará eventos ocorridos após a configuração do Azure AD para enviar eventos ao Azure Monitor.

Se você quiser conhecer os eventos de auditoria mais antigos e mais recentes realizados no Azure Monitor, use a seguinte consulta:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Para obter mais informações sobre as colunas armazenadas para eventos de auditoria no Azure Monitor, consulte Interpretar o esquema de [logs de auditoria AD do Azure no Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Crie consultas personalizadas do Monitor Azure usando o Azure PowerShell

Você pode acessar logs através do PowerShell depois de configurar o Azure AD para enviar logs para o Azure Monitor. Em seguida, envie consultas de scripts ou da linha de comando PowerShell, sem precisar ser um admin global no inquilino. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Certifique-se de que o usuário ou o diretor de serviço tenha a atribuição correta da função

Certifique-se de que você, o usuário ou o diretor de serviço que irá autenticar o Azure AD, esteja na função Azure apropriada no espaço de trabalho do Log Analytics. As opções de função são o Log Analytics Reader ou o Log Analytics Contributor. Se você já estiver em uma dessas funções, pule para recuperar o [ID do Log Analytics com uma assinatura do Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Para definir a atribuição de função e criar uma consulta, faça as seguintes etapas:
1. No portal Azure, localize o [espaço de trabalho log analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecione **controle de acesso (IAM)**.

1. Em seguida, clique **em Adicionar** para adicionar uma atribuição de função.

    ![Adicionar uma atribuição de função](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instalar o módulo Azure PowerShell

Depois de ter a atribuição de função apropriada, inicie o PowerShell e [instale o módulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (se você ainda não tiver), digitando:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Agora você está pronto para autenticar o Azure AD e recuperar o id do espaço de trabalho do Log Analytics que você está consultando.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Recupere o ID do Log Analytics com uma assinatura do Azure
Se você tiver apenas uma assinatura única do Azure e um único espaço de trabalho do Log Analytics, digite o seguinte para autenticar o Azure AD, conecte-se a essa assinatura e recupere esse espaço de trabalho:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Recupere o ID do Log Analytics com várias assinaturas do Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) opera em uma assinatura por vez. Então, se você tem várias assinaturas do Azure, você vai querer ter certeza de que você se conecta ao que tem o espaço de trabalho do Log Analytics com os logs Azure AD. 
 
 Os cmdlets a seguir exibem uma lista de assinaturas e encontram o id da assinatura que tem o espaço de trabalho do Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Você pode reautenticar e associar sua sessão PowerShell a `Connect-AzAccount –Subscription $subs[0].id`essa assinatura usando um comando como . Para saber mais sobre como autenticar o Azure no PowerShell, incluindo não interativamente, consulte [Entrar com o Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Se você tiver vários espaços de trabalho do Log Analytics nessa assinatura, o cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) retorna a lista de espaços de trabalho. Então você pode encontrar o que tem os logs Azure AD. O `CustomerId` campo retornado por este cmdlet é o mesmo que o valor do "ID do Espaço de Trabalho" exibido no portal Azure na visão geral do espaço de trabalho do Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Envie a consulta para o espaço de trabalho do Log Analytics
Finalmente, uma vez identificado um espaço de trabalho, você pode usar [o Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) para enviar uma consulta kusto para esse espaço de trabalho. Essas consultas são [escritas no idioma de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).
 
Por exemplo, você pode recuperar o intervalo de datas dos registros de eventos de auditoria do espaço de trabalho do Log Analytics, com cmdlets do PowerShell para enviar uma consulta como:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Você também pode recuperar eventos de gerenciamento de direitos usando uma consulta como:

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Próximas etapas:
- [Crie relatórios interativos com pastas de trabalho do Azure Monitor](../../azure-monitor/app/usage-workbooks.md) 

