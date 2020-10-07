---
title: Configurar classificações de permissão com o Azure AD
description: Saiba como gerenciar classificações de permissão delegadas.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0add5001e2e62e26d448b06927210c14f17729f1
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804281"
---
# <a name="configure-permission-classifications"></a>Configurar classificações de permissão

As classificações de permissão permitem que você identifique o impacto que as diferentes permissões têm de acordo com as políticas e avaliações de risco de sua organização. Por exemplo, você pode usar classificações de permissão em políticas de consentimento para identificar o conjunto de permissões no qual os usuários têm permissão para consentir.

## <a name="manage-permission-classifications"></a>Gerenciar classificações de permissão

Atualmente, há suporte apenas à classificação de permissão "Pouco impacto". Somente as permissões delegadas que não exijam consentimento de administrador podem ser classificadas como de "Pouco impacto".

> [!TIP]
> As permissões mínimas necessárias para a entrada básica são `openid` , `profile` , `email` `User.Read` e `offline_access` , que são todas as permissões delegadas no Microsoft Graph. Com essas permissões, um aplicativo pode ler os detalhes do perfil completo do usuário conectado e pode manter esse acesso mesmo quando o usuário não estiver mais usando o aplicativo.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estas etapas para classificar permissões usando o portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) como um [Administrador Global](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Consentimento e permissões** > **Classificações de permissão**.
1. Escolha **Adicionar permissões** para classificar outra permissão como de "Pouco impacto".
1. Selecione a API e as permissões delegadas.

Neste exemplo, classificamos o conjunto mínimo de permissões obrigatórias para o logon único:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Classificações de permissão":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Você pode usar o módulo de versão prévia mais recente do PowerShell do Azure AD, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), para classificar permissões. As classificações de permissão são configuradas no objeto **ServicePrincipal** da API que publica as permissões.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Listar as classificações de permissão atuais para uma API

1. Recupere o objeto **ServicePrincipal** para a API. Aqui, recuperamos o objeto ServicePrincipal para a API do Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Leia as classificações de permissão delegadas para a API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Classificar uma permissão como "impacto baixo"

1. Recupere o objeto **ServicePrincipal** para a API. Aqui, recuperamos o objeto ServicePrincipal para a API do Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Localize a permissão delegada que você deseja classificar:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Defina a classificação de permissão usando o nome e a ID da permissão:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Remover uma classificação de permissão delegada

1. Recupere o objeto **ServicePrincipal** para a API. Aqui, recuperamos o objeto ServicePrincipal para a API do Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Localize a classificação de permissão delegada que você deseja remover:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Exclua a classificação de permissão:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Próximas etapas

Para saber mais:

* [Definir configurações de consentimento do usuário](configure-user-consent.md)
* [Configurar o fluxo de trabalho de consentimento do administrador](configure-admin-consent-workflow.md)
* [Saiba como gerenciar o consentimento em aplicativos e avaliar solicitações de consentimento](manage-consent-requests.md)
* [Conceder consentimento de administrador em todo o locatário para um aplicativo](grant-admin-consent.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

Para obter ajuda ou encontrar respostas às suas perguntas:
* [Azure AD no StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
