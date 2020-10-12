---
title: Adicionar ou remover atribuições de função do Azure usando o Azure PowerShell-RBAC do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o Azure PowerShell e o Azure RBAC (controle de acesso baseado em função) do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9b0df4337a5e5faff3427222fb66caf8e02184a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86146653"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Adicionar ou remover atribuições de função do Azure usando o Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Este artigo descreve como atribuir funções usando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [PowerShell em Azure cloud Shell](/azure/cloud-shell/overview) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Obter IDs de objeto

Para adicionar ou remover atribuições de função, talvez seja necessário especificar a ID exclusiva de um objeto. A ID tem o formato: `11111111-1111-1111-1111-111111111111`. Você pode obter a ID usando o portal do Azure ou Azure PowerShell.

### <a name="user"></a>Usuário

Para obter a ID de objeto para um usuário do Azure AD, você pode usar [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Grupo

Para obter a ID de objeto para um grupo do Azure AD, você pode usar [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Aplicativo

Para obter a ID de objeto para uma entidade de serviço do Azure AD (identidade usada por um aplicativo), você pode usar [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Para uma entidade de serviço, use a ID de objeto, e **não** a ID do aplicativo.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

Para conceder acesso no Azure RBAC, adicione uma atribuição de função.

### <a name="user-at-a-resource-group-scope"></a>Usuário em um escopo de grupo de recursos

Para adicionar uma atribuição de função para um usuário em um escopo de grupo de recursos, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Usando a ID de função exclusiva

Há algumas ocasiões em que um nome de função pode ser alterado, por exemplo:

- Você está usando sua própria função personalizada e decide alterar o nome.
- Você está usando uma função de visualização que tem **(visualização)** no nome. Quando a função é liberada, a função é renomeada.

> [!IMPORTANT]
> Uma versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mesmo que uma função seja renomeada, a ID da função não será alterada. Se você estiver usando scripts ou automação para criar atribuições de função, é uma prática recomendada usar a ID de função exclusiva em vez do nome da função. Portanto, se uma função for renomeada, os scripts provavelmente funcionarão.

Para adicionar uma atribuição de função usando a ID de função exclusiva em vez do nome da função, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -Scope <resource_group_name/resource/management groups>
```

O exemplo a seguir atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao *usuário \@ Alain example.com* no escopo do grupo de recursos *Pharma-Sales* . Para obter a ID de função exclusiva, você pode usar [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou ver [funções internas do Azure](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Agrupar em um escopo de recurso

Para adicionar uma atribuição de função para um grupo em um escopo de recurso, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter informações sobre como obter a ID de objeto do grupo, consulte [obter IDs de objeto](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Aplicativo em um escopo de assinatura

Para adicionar uma atribuição de função para um aplicativo em um escopo de assinatura, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter informações sobre como obter a ID de objeto do aplicativo, consulte [obter IDs de objeto](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Usuário em um escopo do grupo de gerenciamento

Para adicionar uma atribuição de função para um usuário em um escopo de grupo de gerenciamento, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter a ID do grupo de gerenciamento, você pode encontrá-la na folha **grupos de gerenciamento** no portal do Azure ou pode usar [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No RBAC do Azure, para remover o acesso, você remove uma atribuição de função usando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

O exemplo a seguir remove a atribuição de função de *colaborador de máquina virtual* do usuário *Alain \@ example.com* no grupo de recursos *Pharma-Sales* :

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

O exemplo a seguir remove o <role_name> função do <object_id> em um escopo de assinatura.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

O exemplo a seguir remove o <role_name> função do <object_id> no escopo do grupo de gerenciamento.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Se você receber a mensagem de erro: "as informações fornecidas não são mapeadas para uma atribuição de função", certifique-se de especificar também os `-Scope` `-ResourceGroupName` parâmetros ou. Para obter mais informações, consulte [solucionar problemas do RBAC do Azure](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função do Azure usando Azure PowerShell](role-assignments-list-powershell.md)
- [Tutorial: Conceder a um grupo acesso aos recursos do Azure usando o Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Tutorial: Criar uma função personalizada do Azure usando o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Gerenciar recursos com o Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
