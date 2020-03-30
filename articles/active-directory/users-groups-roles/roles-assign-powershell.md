---
title: Atribuir funções personalizadas usando o Azure PowerShell - Azure AD | Microsoft Docs
description: Gerencie membros de uma função personalizada de administrador Azure AD com o Azure PowerShell.
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
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025328"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Atribuir funções personalizadas com escopo de recursos usando o PowerShell no Azure Active Directory

Este artigo descreve como criar uma atribuição de função no escopo de toda a organização no Azure Active Directory (Azure AD). Atribuir um papel no escopo de toda a organização concede acesso em toda a organização Azure AD. Para criar uma atribuição de função com um escopo de um único recurso Azure AD, consulte [Como criar uma função personalizada e atribuí-la no escopo de recursos](roles-create-custom.md). Este artigo usa o módulo [PowerShell Versão 2 do Active Directory do Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)

Para obter mais informações sobre as funções de administrador do Azure AD, consulte [Atribuir funções de administrador no Diretório Ativo do Azure](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Permissões necessárias

Conecte-se ao seu inquilino Azure AD usando uma conta de administrador global para atribuir ou remover funções.

## <a name="prepare-powershell"></a>Prepare PowerShell

Instale o módulo Azure AD PowerShell na [Galeria PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de visualização Azure AD PowerShell, usando o seguinte comando:

``` PowerShell
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, combine a versão retornada pelo seguinte comando ao listado aqui:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Agora você pode começar a usar os cmdlets do módulo. Para obter uma descrição completa dos cmdlets no módulo Azure AD, consulte a documentação de referência on-line para o módulo de [visualização Azure AD](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Atribuir uma função a um usuário ou diretor de serviço com escopo de recursos

1. Abra o módulo PowerShell de visualização azure AD.
1. Faça login executando o `Connect-AzureAD`comando.
1. Crie uma nova função usando o seguinte script PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Para atribuir a função a um principal de serviço em vez de um usuário, use o [cmdlet Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Operações em RoleDefinition

Os objetos de definição de função contêm a definição da função incorporada ou personalizada, juntamente com as permissões concedidas por essa atribuição de função. Esse recurso exibe definições de função personalizadas e diretórios incorporadosFunes (que são exibidos em forma equivalente roleDefinition). Hoje, uma organização Azure AD pode ter um máximo de 30 RoleDefinitions personalizados exclusivos definidos.

### <a name="create-operations-on-roledefinition"></a>Criar operações no RoleDefinition

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Ler operações na RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Atualizar operações no RoleDefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Excluir operações no RoleDefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Operações em RoleAssignment

As atribuições da função contêm informações que ligam um determinado princípio de segurança (um diretor de serviço de usuário ou aplicativo) a uma definição de função. Se necessário, você pode adicionar um escopo de um único recurso Azure AD para as permissões atribuídas.  Restringir o escopo de permissões é suportado para funções incorporadas e personalizadas.

### <a name="create-operations-on-roleassignment"></a>Criar operações na atribuição de papéis

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

### <a name="read-operations-on-roleassignment"></a>Ler operações em atribuição de papéis

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Excluir operações na atribuição de papéis

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Próximas etapas

- Compartilhe conosco no [fórum de funções administrativas do Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para obter mais informações sobre funções e azure AD administrator roles, consulte [Atribuir funções de administrador](directory-assign-admin-roles.md).
- Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
