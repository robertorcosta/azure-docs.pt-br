---
title: Exibir atribuições de função personalizadas no portal Azure AD | Microsoft Docs
description: Agora você pode ver e gerenciar membros de uma função de administrador Azure AD no centro de administração Azure AD.
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
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259702"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Exibir atribuições de função personalizadas no Diretório Ativo do Azure

Este artigo descreve como visualizar funções personalizadas que você atribuiu no Azure Active Directory (Azure AD). No Azure Active Directory (Azure AD), as funções podem ser atribuídas em um escopo de toda a organização ou com um escopo de um único aplicativo.

- As atribuições de função no escopo de toda a organização são adicionadas e podem ser vistas na lista de atribuições de função única.
- As atribuições de função no escopo de um único aplicativo não são adicionadas e não podem ser vistas na lista de atribuições escopo de toda a organização.

## <a name="view-role-assignments-in-the-azure-portal"></a>Ver atribuições de função no portal Azure

Este procedimento descreve a visualização de atribuições de uma função com escopo de toda a organização.

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com permissões de administrador de funções privilegiadas ou administrador global na organização Azure AD.
1. Selecione **O Diretório Ativo do Azure,** selecione **Funções e administradores**e selecione uma função para abri-lo e visualizar suas propriedades.
1. Selecione **Atribuições** para exibir as atribuições da função.

    ![Exibir atribuições de função e permissões ao abrir uma função da lista](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Exibir atribuições de função usando o Azure AD PowerShell

Esta seção descreve a visualização de atribuições de uma função com escopo de toda a organização. Este artigo usa o módulo [PowerShell Versão 2 do Active Directory do Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) Para exibir as atribuições de escopo de um único aplicativo usando o PowerShell, você pode usar os cmdlets em [Atribuir funções personalizadas com o PowerShell](roles-assign-powershell.md).

### <a name="prepare-powershell"></a>Prepare PowerShell

Primeiro, você deve [baixar o módulo de visualização PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

Para instalar o módulo PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Para verificar se o módulo está pronto para ser usado, use o seguinte comando:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Ver as atribuições de um papel

Exemplo de visualização das atribuições de um papel.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Exibir atribuições de função usando a API do Microsoft Graph

Esta seção descreve a visualização de atribuições de uma função com escopo de toda a organização.  Para exibir as atribuições do escopo de um único aplicativo usando a API do Gráfico, você pode usar as operações em [Atribuir funções personalizadas com a API do Graph](roles-assign-graph.md).

Solicitação HTTP para obter uma atribuição de função para uma determinada definição de função.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Exibir atribuições do escopo de um único aplicativo

Esta seção descreve a visualização de atribuições de uma função com escopo de um único aplicativo. Esse recurso está atualmente em visualização pública.

1. Faça login no centro de administração [do Azure AD](https://aad.portal.azure.com) com permissões de administrador de funções privilegiadas ou administrador global na organização Azure AD.
1. Selecione **registros do aplicativo**e selecione o registro do aplicativo para visualizar suas propriedades. Você pode ter que selecionar **Todos os aplicativos** para ver a lista completa de registros de aplicativos em sua organização Azure AD.

    ![Crie ou edite registros de aplicativos na página de registros do Aplicativo](./media/roles-create-custom/appreg-all-apps.png)

1. No registro do aplicativo, selecione **Funções e administradores**e selecione uma função para visualizar suas propriedades.

    ![Veja as atribuições das atribuições das vagas de registro do aplicativo na página de registros do Aplicativo](./media/roles-view-assignments/appreg-assignments.png)

1. Selecione **Atribuições** para exibir as atribuições da função. A abertura da exibição de atribuições dentro do registro do aplicativo mostra as atribuições que estão escopo deste recurso Azure AD.

    ![Exibir atribuições de função de registro de aplicativos a partir das propriedades de um registro de aplicativo](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](directory-assign-admin-roles.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
