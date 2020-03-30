---
title: Entrega segura do WebHook com a Azure AD na grade de eventos do Azure
description: Descreve como entregar eventos em endpoints HTTPS protegidos pelo Azure Active Directory usando o Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931549"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicar eventos para os pontos finais protegidos do Azure Active Directory

Este artigo descreve como aproveitar o Azure Active Directory para garantir a conexão entre sua Assinatura de Evento e seu ponto final do webhook. Para obter uma visão geral dos aplicativos ad e dos princípios de serviços do Azure, consulte [a visão geral da plataforma de identidade da Microsoft (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

Este artigo usa o portal Azure para demonstração, no entanto, o recurso também pode ser habilitado usando CLI, PowerShell ou SDKs.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Criar um aplicativo AD do Azure

Comece criando um aplicativo Azure AD para o seu ponto final protegido. Consulte https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configure sua API protegida para ser chamada por um aplicativo daemon.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Habilite a grade de eventos para usar seu aplicativo Azure AD

Use o script PowerShell abaixo para criar um princípio de função e serviço em seu aplicativo Azure AD. Você precisará do ID do inquilino e do ID do objeto do aplicativo Azure AD:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Modifique o $myTenantId do script PowerShell para usar o ID do inquilino azure AD.
1. Modifique o $myAzureADApplicationObjectId do script PowerShell de usar o ID do objeto do seu aplicativo Azure AD
1. Execute o script modificado.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
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
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Configure a assinatura do evento

No fluxo de criação da assinatura do evento, selecione o tipo de ponto final 'Web Hook'. Depois de dar seu URI de ponto final, clique na guia de recursos adicionais na parte superior da lâmina de assinaturas de eventos criar.

![Selecione webhook tipo ponto final](./media/secure-webhook-delivery/select-webhook.png)

Na guia recursos adicionais, marque a caixa para 'Usar autenticação AAD' e configure o ID do inquilino e o ID do aplicativo:

* Copie o ID do inquilino Azure AD da saída do script e digite-o no campo ID do inquilino AAD.
* Copie o ID do aplicativo Azure AD da saída do script e digite-o no campo ID da aplicação AAD.

    ![Ação secure Webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o monitoramento de entregas de evento, consulte [Entrega de mensagens da Grade de Eventos do Monitor](monitor-event-delivery.md).
* Para saber mais sobre a chave de autenticação, confira [Event Grid security and authentication](security-authentication.md) (Segurança e autenticação da Grade de Eventos).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
