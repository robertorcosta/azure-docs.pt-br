---
title: Adicionar ou remover atribuições de função com O RBAC e o Azure PowerShell
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, diretores de serviçoou identidades gerenciadas usando o RBAC (Role-Based Access Control, controle de acesso baseado em função) do Azure e do Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283206"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Adicionar ou remover atribuições de função usando o Azure RBAC e o Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando o Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [PowerShell em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Obtenha iDs de objeto

Para adicionar ou remover atribuições de função, talvez seja necessário especificar o ID exclusivo de um objeto. O ID tem `11111111-1111-1111-1111-111111111111`o formato: . Você pode obter o ID usando o portal Azure ou o Azure PowerShell.

### <a name="user"></a>Usuário

Para obter o ID do objeto para um usuário AD do Azure, você pode usar [get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Agrupar

Para obter o ID do objeto para um grupo AD do Azure, você pode usar [o Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Aplicativo

Para obter o ID do objeto para um principal de serviço Azure AD (identidade usada por um aplicativo), você pode usar [get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Para um diretor de serviço, use o ID do objeto e **não** o ID do aplicativo.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC, para conceder acesso, você adiciona uma atribuição de função.

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

### <a name="using-the-unique-role-id"></a>Usando o ID de função única

Há algumas vezes em que um nome de função pode mudar, por exemplo:

- Você está usando seu próprio papel personalizado e decide mudar o nome.
- Você está usando uma função de visualização que tem **(Visualização)** no nome. Quando o papel é lançado, o papel é renomeado.

> [!IMPORTANT]
> Uma versão de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mesmo que um papel seja renomeado, o ID de função não muda. Se você estiver usando scripts ou automação para criar suas atribuições de função, é uma prática recomendada usar o ID de função única em vez do nome da função. Portanto, se uma função for renomeada, seus scripts são mais propensos a funcionar.

Para adicionar uma atribuição de função usando o ID de função exclusivo em vez do nome da função, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

O exemplo a seguir atribui a função [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao usuário *\@de example.com no* escopo do grupo de recursos de *vendas farmacêuticas.* Para obter o ID de função exclusivo, você pode usar [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou ver [funções incorporadas para recursos do Azure](built-in-roles.md).

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

### <a name="group-at-a-resource-scope"></a>Grupo em um escopo de recursos

Para adicionar uma atribuição de função para um grupo em um escopo de recurso, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter informações sobre como obter o ID do objeto do grupo, consulte [Obter IDs de objeto](#get-object-ids).

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

Para adicionar uma atribuição de função para um aplicativo em um escopo de assinatura, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter informações sobre como obter o ID do objeto do aplicativo, consulte [Obter IDs de objeto](#get-object-ids).

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

### <a name="user-at-a-management-group-scope"></a>Usuário em um escopo de grupo de gerenciamento

Para adicionar uma atribuição de função para um usuário em um escopo de grupo de gerenciamento, use [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Para obter o ID do grupo de gerenciamento, você pode encontrá-lo na lâmina **de grupos de gerenciamento** no portal Azure ou usar o [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

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

No RBAC, para remover o acesso, remova uma atribuição de função usando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

O exemplo a seguir remove a atribuição de *função Contribuinte de Máquina Virtual* do usuário *\@de example.com no* grupo de recursos de *vendas farmacêuticas:*

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

O exemplo a seguir remove a função <role_name> de <> object_id em um escopo de assinatura.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

O exemplo a seguir remove o papel <role_name> de <object_id> no escopo do grupo de gestão.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Se você receber a mensagem de erro: "As informações fornecidas não mapeiam para uma atribuição de função", certifique-se de que você também especifique os `-Scope` parâmetros ou `-ResourceGroupName` parâmetros. Para obter mais informações, consulte [Solucionar problemas rBAC para recursos do Azure](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função usando OZure RBAC e O Azure PowerShell](role-assignments-list-powershell.md)
- [Tutorial: Conceda a um grupo acesso aos recursos do Azure usando o RBAC e o Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Tutorial: Crie uma função personalizada para os recursos do Azure usando o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Gerenciar recursos com o Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
