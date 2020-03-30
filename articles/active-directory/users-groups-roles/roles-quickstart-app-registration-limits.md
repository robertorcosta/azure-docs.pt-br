---
title: Remover limites na criação de registros de aplicativos - Azure AD | Microsoft Docs
description: Atribua uma função personalizada para conceder registros irrestritos de aplicativos no Azure AD Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559038"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Quickstart: Permissão de concessão para criar registros ilimitados de aplicativos

Neste quickstart, você criará uma função personalizada com permissão para criar um número ilimitado de registros de aplicativos e, em seguida, atribuirá essa função a um usuário. O usuário designado pode então usar o portal Azure AD, Azure AD PowerShell ou Microsoft Graph API para criar registros de aplicativos. Ao contrário da função de desenvolvedor de aplicativos incorporado, essa função personalizada concede a capacidade de criar um número ilimitado de registros de aplicativos. A função Desenvolvedor de aplicativos concede a capacidade, mas o número total de objetos criados é limitado a 250 para evitar atingir [a cota de objetos em todo o diretório](directory-service-limits-restrictions.md).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisite"></a>Pré-requisito

A função menos privilegiada necessária para criar e atribuir funções personalizadas do Azure AD é o administrador de funções privilegiadas.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Crie uma nova função personalizada usando o portal Azure AD

1. Faça login no centro de administração [azure AD](https://aad.portal.azure.com) com permissões de administrador privileged Role ou Global administrador na organização Azure AD.
1. Selecione **O Diretório Ativo do Azure,** selecione **Funções e administradores**e selecione **Nova função personalizada**.

    ![Criar ou editar funções a partir da página Funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. Na guia **Noções** Básicas, forneça "Criador de Registro de Aplicativo" para o nome da função e "Pode criar um número ilimitado de registros de aplicativos" para a descrição da função e, em seguida, selecione **Next**.

    ![fornecer um nome e descrição para uma função personalizada na guia Basics](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. Na guia **Permissões,** digite "microsoft.directory/applications/create" na caixa de pesquisa e selecione as caixas de seleção ao lado das permissões desejadas e selecione **Next**.

    ![Selecione as permissões para uma função personalizada na guia Permissões](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. Na **guia 'Revisar + criar',** revise as permissões e selecione **Criar**.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Atribuir a função a um usuário usando o portal Azure AD

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com permissões de administrador de funções privilegiadas ou administrador global em sua organização Azure AD.
1. Selecione **o Diretório Ativo do Azure** e selecione **Funções e administradores**.
1. Selecione a função Criador de registro de aplicativo e selecione **Adicionar atribuição**.
1. Selecione o usuário desejado e clique **em Selecionar** para adicionar o usuário à função.

Feito! Neste quickstart, você criou com sucesso uma função personalizada com permissão para criar um número ilimitado de registros de aplicativos e, em seguida, atribuir essa função a um usuário.

> [!TIP]
> Para atribuir a função a um aplicativo usando o portal Azure AD, digite o nome do aplicativo na caixa de pesquisa da página de atribuição. Os aplicativos não são mostrados na lista por padrão, mas são retornados nos resultados da pesquisa.

### <a name="app-registration-permissions"></a>Permissões de registro de aplicativos

Existem duas permissões disponíveis para a concessão da capacidade de criar registros de aplicativos, cada uma com comportamento diferente.

- microsoft.directory/applications/createAsProprietário: Atribuir essa permissão resulta na adição do criador como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado contará com a cota de 250 objetos criados pelo criador.
- microsoft.directory/applicationPolicies/create: Atribuir essa permissão resulta em que o criador não será adicionado como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado não contará com a cota de 250 objetos criados pelo criador. Use essa permissão com cuidado, pois não há nada que impeça o cessionário de criar registros de aplicativos até que a cota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, essa permissão prevalecerá.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Crie uma função personalizada usando o Azure AD PowerShell

Crie uma nova função usando o seguinte script PowerShell:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribuir a função personalizada usando o Azure AD PowerShell

#### <a name="prepare-powershell"></a>Prepare PowerShell

Primeiro, instale o módulo Azure AD PowerShell na [Galeria PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de visualização Azure AD PowerShell, usando o seguinte comando:

```powershell
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, combine a versão retornada pelo seguinte comando ao listado aqui:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>Atribuir a função personalizada

Atribuir a função usando o script powershell abaixo:

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Crie uma função personalizada usando a API do Microsoft Graph

Solicitação HTTP para criar a função personalizada.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Corpo

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Atribuir a função personalizada usando a API do Microsoft Graph

A atribuição de função combina um ID principal de segurança (que pode ser um usuário ou principal de serviço), uma ID de definição de função (função) e um escopo de recurso Azure AD.

Solicitação HTTP para atribuir uma função personalizada.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Próximas etapas

- Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](directory-assign-admin-roles.md).
- Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
