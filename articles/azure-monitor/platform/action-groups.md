---
title: Criar e gerenciar grupos de ações no portal do Azure
description: Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6b3d1ff76d4f7611da8e08dd4ce42293c805978e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423858"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Alertas do Azure Monitor e da Integridade do Serviço usam grupos de ações para notificar usuários de que um alerta foi disparado. Vários alertas podem usar o mesmo grupo de ação ou grupos de ações diferentes dependendo dos requisitos do usuário. Você pode configurar até 2 mil grupos de ação em uma assinatura.

You configure an action to notify a person by email or SMS, they receive a confirmation indicating they have been added to the action group.

Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Cada ação é composta das seguintes propriedades:

* **Nome:** um identificador exclusivo dentro do grupo de ações.  
* **Action type**: The action performed. Exemplos incluem o envio de um email de chamada, SMS, voz; ou disparando vários tipos de ações automatizadas. Veja os tipos mais adiante neste artigo.
* **Details**: The corresponding details that vary by *action type*.

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure

1. In the [Azure portal](https://portal.azure.com), search for and select **Monitor**. The **Monitor** pane consolidates all your monitoring settings and data in one view.

1. Selecione **Alertas**, em seguida, selecione **Gerenciar ações**.

    ![Manage Actions button](./media/action-groups/manage-action-groups.png)
    
1. Selecione **Adicionar grupo de ações** e preencha os campos.

    ![O comando "Adicionar grupo de ações"](./media/action-groups/add-action-group.png)
    
1. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/action-groups/action-group-define.png)

1. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo.

1. Selecione o **Grupo de recursos** no qual o grupo de ações é salvo.

1. Define a list of actions. Provide the following for each action:

    1. **Nome**: insira um identificador exclusivo para esta ação.

    1. **Action Type**: selecione Email/SMS/Push/Voz, Aplicativo Lógico, Webhook, ITSM ou Runbook de Automação.

    1. **Detalhes**: de acordo com o tipo de ação, insira um número de telefone, endereço de email, URI de webhook, aplicativo do Azure, conexão de ITSM ou runbook de Automação. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM.
    
    1. **Common alert schema**: You can choose to enable the [common alert schema](https://aka.ms/commonAlertSchemaDocs), which provides the advantage of having a single extensible and unified alert payload across all the alert services in Azure Monitor.

1. Selecione **OK** para criar o grupo de ações.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação

After you create an action group, it's visible in the **Action groups** section of the **Monitor** pane. Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="action-specific-information"></a>Informações específicas da ação

> [!NOTE]
> See [Subscription Service Limits for Monitoring](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) for numeric limits on each of the items below.  

### <a name="automation-runbook"></a>Automation Runbook
Refer to the [Azure subscription service limits](../../azure-subscription-service-limits.md) for limits on Runbook payloads.

You may have a limited number of Runbook actions in an Action Group. 

### <a name="azure-app-push-notifications"></a>Azure app Push Notifications
You may have a limited number of Azure app actions in an Action Group.

### <a name="email"></a>Email
Os emails serão enviados dos endereços de email a seguir. Certifique-se de que a filtragem de email esteja configurada adequadamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

You may have a limited number of email actions in an Action Group. See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

### <a name="email-azure-resource-manager-role"></a>Email Azure Resource Manager Role
Send email to the members of the subscription's role.

You may have a limited number of email actions in an Action Group. See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

### <a name="function"></a>Função
The function keys for Function Apps configured as actions are read through the Functions API, which currently requires v2 function apps to configure the app setting “AzureWebJobsSecretStorageType” to “files”. For more information, see [Changes to Key Management in Functions V2]( https://aka.ms/funcsecrets).

You may have a limited number of Function actions in an Action Group.

### <a name="itsm"></a>ITSM
Ação de ITSM exige uma Conexão de ITSM. Saiba como criar uma [Conexão de ITSM](../../azure-monitor/platform/itsmc-overview.md).

You may have a limited number of ITSM actions in an Action Group. 

### <a name="logic-app"></a>Aplicativo de lógica
You may have a limited number of Logic App actions in an Action Group.

### <a name="secure-webhook"></a>Secure Webhook
**The Secure Webhook functionality is currently in Preview.**

The Action Groups Webhook action enables you to take advantage of Azure Active Directory to secure the connection between your action group and your protected web API (webhook endpoint). The overall workflow for taking advantage of this functionality is described below. For an overview of Azure AD Applications and service principals, see [Microsoft identity platform (v2.0) overview](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Create an Azure AD Application for your protected web API. Confira https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure your protected API to be called by a daemon app.
    
1. Enable Action Groups to use your Azure AD Application.

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
    - Modify the PowerShell script's Connect-AzureAD call to use your Azure AD Tenant ID.
    - Modify the PowerShell script's variable $myAzureADApplicationObjectId to use the Object ID of your Azure AD Application
    - Run the modified script.
    
1. Configure the Action Group Secure Webhook action.
    - Copy the value $myApp.ObjectId from the script and enter it in the Application Object ID field in the Webhook action definition.
    
    ![Secure Webhook action](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Secure Webhook PowerShell Script

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) and [SMS alert behavior](../../azure-monitor/platform/alerts-sms-behavior.md) for additional important information.

You may have a limited number of SMS actions in an Action Group.  

### <a name="voice"></a>Voz
See the [rate limiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) article.

You may have a limited number of Voice actions in an Action Group.

### <a name="webhook"></a>webhook
Webhooks are retried using the following rules. The webhook call is retried a maximum of 2 times when the following HTTP status codes are returned: 408, 429, 503, 504 or the HTTP endpoint does not respond. A primeira nova tentativa ocorre após 10 segundos. A segunda tentativa ocorre após 100 segundos. After two failures, no action group will call the endpoint for 30 minutes. 

Intervalos de endereços IP de fonte
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

To receive updates about changes to these IP addresses, we recommend you configure a Service Health alert, which monitors for Informational notifications about the Action Groups service.

You may have a limited number of Webhook actions in an Action Group.



## <a name="next-steps"></a>Próximos passos
* Saiba mais sobre o [comportamento de alertas por SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Saiba mais sobre o [Conector de ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Saiba mais sobre a [limitação de taxa](../../azure-monitor/platform/alerts-rate-limiting.md) para alertas.
* Obtenha uma [visão geral dos alertas do log de atividades](../../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
