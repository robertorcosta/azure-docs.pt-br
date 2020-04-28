---
title: Arquivar & relatório com Azure Monitor-gerenciamento de direitos do AD do Azure
description: Saiba como arquivar logs e criar relatórios com Azure Monitor em Azure Active Directory gerenciamento de autorização.
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
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380186"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Arquivar logs e relatórios no gerenciamento de direitos do Azure AD no Azure Monitor

O Azure AD armazena eventos de auditoria por até 30 dias no log de auditoria. No entanto, você pode manter os dados de auditoria por mais tempo do que o período de retenção padrão, descrito em [quanto tempo o Azure ad Store relata dados?](../reports-monitoring/reference-reports-data-retention.md), encaminhando-os para uma conta de armazenamento do Azure ou usando Azure monitor. Você pode usar pastas de trabalho e relatórios e consultas personalizadas sobre esses dados.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Configurar o Azure AD para usar o Azure Monitor
Antes de usar as pastas de trabalho do Azure Monitor, você deve configurar o Azure AD para enviar uma cópia dos seus logs de auditoria para Azure Monitor.

O arquivamento de logs de auditoria do Azure AD exige que você tenha Azure Monitor em uma assinatura do Azure. Você pode ler mais sobre os pré-requisitos e os custos estimados do uso de Azure Monitor nos [logs de atividades do Azure AD no Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Função de pré-requisito**: administrador global

1. Entre no portal do Azure como um usuário que seja um administrador global. Verifique se você tem acesso ao grupo de recursos que contém o espaço de trabalho Azure Monitor.
 
1. Selecione **Azure Active Directory** clique em **configurações de diagnóstico** em monitoramento no menu de navegação à esquerda. Verifique se já existe uma configuração para enviar os logs de auditoria para esse espaço de trabalho.

1. Se ainda não houver uma configuração, clique em **Adicionar configuração de diagnóstico**. Use as instruções no artigo [integrar logs do Azure ad aos logs de Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) para enviar o log de auditoria do Azure ad para o espaço de trabalho Azure monitor.

    ![Painel Configurações de diagnóstico](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Depois que o log for enviado para Azure Monitor, selecione **log Analytics espaços de trabalho**e selecione o espaço de trabalho que contém os logs de auditoria do Azure AD.

1. Selecione **uso e custos estimados** e clique em **retenção de dados**. Altere o controle deslizante para o número de dias que você deseja manter os dados para atender aos seus requisitos de auditoria.

    ![Painel Log Analytics espaços de trabalho](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Posteriormente, para ver o intervalo de datas mantidas em seu espaço de trabalho, você pode usar a pasta de trabalho de *intervalo de datas do log arquivado* :  
    
    1. Selecione **Azure Active Directory** , em seguida, clique em **pastas de trabalho**. 
    
    1. Expanda a seção **Azure Active Directory solução de problemas**e clique em **intervalo de datas de log arquivado**. 


## <a name="view-events-for-an-access-package"></a>Exibir eventos para um pacote de acesso  

Para exibir eventos de um pacote do Access, você deve ter acesso ao espaço de trabalho do Azure monitor subjacente (consulte [gerenciar o acesso a dados de log e espaços de trabalho no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) para obter informações) e em uma das seguintes funções: 

- Administrador global  
- Administrador de segurança  
- Leitor de segurança  
- Leitor de relatórios  
- Administrador de aplicativos  

Use o procedimento a seguir para exibir eventos: 

1. Na portal do Azure, selecione **Azure Active Directory** , em seguida, clique em **pastas de trabalho**. Se você tiver apenas uma assinatura, passe para a etapa 3. 

1. Se você tiver várias assinaturas, selecione a assinatura que contém o espaço de trabalho.  

1. Selecione a pasta de trabalho chamada *atividade de pacote de acesso*. 

1. Nessa pasta de trabalho, selecione um intervalo de tempo (altere para **tudo** se não tiver certeza) e selecione uma ID de pacote de acesso na lista suspensa de todos os pacotes de acesso que tiveram atividade durante esse intervalo de tempo. Os eventos relacionados ao pacote de acesso que ocorreu durante o intervalo de tempo selecionado serão exibidos.  

    ![Exibir eventos de pacote de acesso](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Cada linha inclui a hora, a ID do pacote de acesso, o nome da operação, a ID do objeto, o UPN e o nome de exibição do usuário que iniciou a operação.  Detalhes adicionais são incluídos em JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Criar consultas de Azure Monitor personalizadas usando o portal do Azure
Você pode criar suas próprias consultas em eventos de auditoria do Azure AD, incluindo eventos de gerenciamento de direitos.  

1. Em Azure Active Directory do portal do Azure, clique em **logs** na seção monitoramento no menu de navegação à esquerda para criar uma nova página de consulta.

1. Seu espaço de trabalho deve ser mostrado no canto superior esquerdo da página de consulta. Se você tiver vários espaços de trabalho do Azure Monitor e o espaço de trabalho que você está usando para armazenar os eventos de auditoria do Azure AD não for mostrado, clique em **selecionar escopo**. Em seguida, selecione a assinatura e o espaço de trabalho corretos.

1. Em seguida, na área de texto da consulta, exclua a cadeia de caracteres "Search *" e substitua-a pela seguinte consulta:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Em seguida, clique em **Executar**. 

    ![Clique em executar para iniciar a consulta](./media/entitlement-management-logs-and-reporting/run-query.png)

A tabela mostrará os eventos do log de auditoria para gerenciamento de direitos da última hora por padrão. Você pode alterar a configuração de "intervalo de tempo" para exibir eventos mais antigos. No entanto, alterar essa configuração mostrará apenas os eventos que ocorreram após o Azure AD ter sido configurado para enviar eventos para Azure Monitor.

Se você quiser saber os eventos de auditoria mais antigos e mais recentes mantidos em Azure Monitor, use a seguinte consulta:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Para obter mais informações sobre as colunas que são armazenadas para eventos de auditoria no Azure Monitor, consulte [interpretar o esquema de logs de auditoria do Azure AD em Azure monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Criar consultas de Azure Monitor personalizadas usando Azure PowerShell

Você pode acessar os logs por meio do PowerShell depois de configurar o Azure AD para enviar logs para Azure Monitor. Em seguida, envie consultas de scripts ou da linha de comando do PowerShell, sem a necessidade de ser um administrador global no locatário. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Verifique se o usuário ou a entidade de serviço tem a atribuição de função correta

Verifique se você, o usuário ou a entidade de serviço que se autenticará no Azure AD, está na função apropriada do Azure no espaço de trabalho Log Analytics. As opções de função são Log Analytics leitor ou colaborador de Log Analytics. Se você já estiver em uma dessas funções, pule para recuperar a [ID de log Analytics com uma assinatura do Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Para definir a atribuição de função e criar uma consulta, execute as seguintes etapas:

1. Na portal do Azure, localize o [espaço de trabalho log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecione **controle de acesso (iam)**.

1. Em seguida, clique em **Adicionar** para adicionar uma atribuição de função.

    ![Adicionar uma atribuição de função](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instalar o módulo Azure PowerShell

Depois de ter a atribuição de função apropriada, inicie o PowerShell e [Instale o módulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (se ainda não tiver feito isso), digitando:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Agora você está pronto para se autenticar no Azure AD e recuperar a ID do espaço de trabalho Log Analytics que você está consultando.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Recuperar ID de Log Analytics com uma assinatura do Azure
Se você tiver apenas uma única assinatura do Azure e um único espaço de trabalho Log Analytics, digite o seguinte para autenticar no Azure AD, conecte-se a essa assinatura e recupere esse espaço de trabalho:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Recuperar ID de Log Analytics com várias assinaturas do Azure

 O [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) opera em uma assinatura por vez. Portanto, se você tiver várias assinaturas do Azure, convém se conectar a uma que tenha o espaço de trabalho Log Analytics com os logs do Azure AD. 
 
 Os cmdlets a seguir exibem uma lista de assinaturas e localizam a ID da assinatura que tem o espaço de trabalho Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Você pode autenticar e associar sua sessão do PowerShell a essa assinatura usando um comando como `Connect-AzAccount –Subscription $subs[0].id`. Para saber mais sobre como autenticar no Azure do PowerShell, incluindo não interativamente, consulte [entrar com Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Se você tiver vários espaços de trabalho do Log Analytics nessa assinatura, o cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) retornará a lista de espaços de trabalho. Em seguida, você pode encontrar aquele que tem os logs do Azure AD. O `CustomerId` campo retornado por esse cmdlet é o mesmo que o valor da "ID do espaço de trabalho" exibido no portal do Azure na visão geral do espaço de trabalho log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Enviar a consulta para o espaço de trabalho Log Analytics
Por fim, depois que um espaço de trabalho for identificado, você poderá usar [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) para enviar uma consulta Kusto para esse espaço de trabalho. Essas consultas são escritas em [linguagem de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).
 
Por exemplo, você pode recuperar o intervalo de datas dos registros de eventos de auditoria do espaço de trabalho Log Analytics, com cmdlets do PowerShell para enviar uma consulta como:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Você também pode recuperar eventos de gerenciamento de direitos usando uma consulta como:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Próximas etapas:
- [Crie relatórios interativos com pastas de trabalho do Azure Monitor](../../azure-monitor/app/usage-workbooks.md) 

