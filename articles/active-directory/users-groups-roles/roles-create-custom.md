---
title: Crie funções personalizadas no controle de acesso baseado em funções do Azure AD | Microsoft Docs
description: Crie e atribua funções personalizadas do Azure AD com escopo de recursos nos recursos do Azure Active Directory.
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
ms.openlocfilehash: c2cb19c82f8c19bf87eeef755adb5756b2452512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025284"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Criar e atribuir uma função personalizada no Diretório Ativo do Azure

Este artigo descreve como criar novas funções personalizadas no Azure Active Directory (Azure AD). Para o básico das funções personalizadas, consulte a [visão geral das funções personalizadas.](roles-custom-overview.md) A função pode ser atribuída apenas no escopo do nível do diretório ou apenas em um escopo de recurso de registro de aplicativo.

As funções personalizadas podem ser criadas na guia [Funções e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na página de visão geral do Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Crie um papel no portal Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Crie uma nova função personalizada para conceder acesso para gerenciar registros de aplicativos

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com permissões de administrador de funções privilegiadas ou administrador global na organização Azure AD.
1. Selecione > **Funções e administradores** > ativos **do Azure****Nova função personalizada**.

   ![Criar ou editar funções a partir da página Funções e administradores](./media/roles-create-custom/new-custom-role.png)

1. Na guia **Noções Básicas,** forneça um nome e uma descrição para a função e clique em **Seguir**.

   ![fornecer um nome e descrição para uma função personalizada na guia Basics](./media/roles-create-custom/basics-tab.png)

1. Na guia **Permissões,** selecione as permissões necessárias para gerenciar propriedades básicas e propriedades credenciais de registros de aplicativos. Para obter uma descrição detalhada de cada permissão, consulte [subtipos e permissões de registro de aplicativos no Azure Active Directory](./roles-custom-available-permissions.md).
   1. Primeiro, digite "credenciais" na `microsoft.directory/applications/credentials/update` barra de pesquisa e selecione a permissão.

      ![Selecione as permissões para uma função personalizada na guia Permissões](./media/roles-create-custom/permissions-tab.png)

   1. Em seguida, digite "básico" na `microsoft.directory/applications/basic/update` barra de pesquisa, selecione a permissão e clique **em Avançar**.
1. Na **guia 'Revisar + criar',** revise as permissões e selecione **Criar**.

Sua função personalizada aparecerá na lista de funções disponíveis a serem atribuídas.

## <a name="create-a-role-using-powershell"></a>Crie uma função usando o PowerShell

### <a name="prepare-powershell"></a>Prepare PowerShell

Primeiro, você deve [baixar o módulo Azure AD Preview PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Para instalar o módulo PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto para ser usado, use o seguinte comando:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Criar a função personalizada

Crie uma nova função usando o seguinte script PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribuir a função personalizada usando o Azure AD PowerShell

Atribuir a função usando o script powershell abaixo:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Crie um papel com a API do Gráfico

1. Crie a definição do papel.

    Solicitação HTTP para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Corpo

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. Crie a atribuição de função.

    Solicitação HTTP para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Corpo

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Atribuir uma função personalizada escopo a um recurso

Como funções incorporadas, as funções personalizadas são atribuídas por padrão no escopo padrão de toda a organização para conceder permissões de acesso sobre todos os registros de aplicativos em sua organização. Mas, ao contrário das funções incorporadas, as funções personalizadas também podem ser atribuídas no escopo de um único recurso Azure AD. Isso permite que você dê ao usuário a permissão para atualizar credenciais e propriedades básicas de um único aplicativo sem ter que criar uma segunda função personalizada.

1. Faça login no centro de administrador [esporárse do Azure AD](https://aad.portal.azure.com) com permissões de desenvolvedor de aplicativos na organização Azure AD.
1. Selecione **inscrições do Aplicativo**.
1. Selecione o registro do aplicativo ao qual você está concedendo acesso para gerenciar. Você pode ter que selecionar **Todos os aplicativos** para ver a lista completa de registros de aplicativos em sua organização Azure AD.

    ![Selecione o registro do aplicativo como um escopo de recurso para uma atribuição de função](./media/roles-create-custom/appreg-all-apps.png)

1. No registro do aplicativo, selecione **Funções e administradores**. Se você ainda não criou um, as instruções estão no [procedimento anterior](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Selecione a função para abrir a página **Atribuições.**
1. Selecione **Adicionar atribuição** para adicionar um usuário. O usuário receberá quaisquer permissões apenas sobre o registro do aplicativo selecionado.

## <a name="next-steps"></a>Próximas etapas

- Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](directory-assign-admin-roles.md).
- Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
