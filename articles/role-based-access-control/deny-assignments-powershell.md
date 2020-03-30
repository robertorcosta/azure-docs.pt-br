---
title: Lista nega atribuições para recursos do Azure com o Azure PowerShell
description: Saiba como listar os usuários, grupos, diretores de serviços e identidades gerenciadas que tiveram acesso negado a ações específicas de recursos do Azure em escopos específicos usando o Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137389"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Lista negar atribuições para recursos do Azure usando o Azure PowerShell

[Negar atribuições](deny-assignments.md) bloqueie os usuários de executar ações específicas de recursos do Azure, mesmo que uma atribuição de função lhes conceda acesso. Este artigo descreve como listar atribuições de negar usando o Azure PowerShell.

> [!NOTE]
> Você não pode criar diretamente suas próprias atribuições de negação. Para obter informações sobre como as atribuições de negação são criadas, consulte [As atribuições de Negar](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, você deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria [das funções incorporadas para os recursos do Azure](built-in-roles.md)
- [PowerShell em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Listar atribuições de negação

### <a name="list-all-deny-assignments"></a>Liste todas as atribuições de negar

Para listar todas as atribuições de negar para a assinatura atual, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Listar atribuições de negar em um escopo de grupo de recursos

Para listar todas as atribuições de negar em um escopo de grupo de recursos, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Listar negar atribuições em um escopo de assinatura

Para listar todas as atribuições de negação em um escopo de assinatura, use [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Para obter o ID de assinatura, você pode encontrá-lo na lâmina **assinaturas** no portal Azure ou você pode usar [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Próximas etapas

- [Compreender atribuições de negação dos recursos do Azure](deny-assignments.md)
- [Lista negar atribuições para recursos do Azure usando o portal Azure](deny-assignments-portal.md)
- [Listar atribuições de negação para recursos do Azure usando a API REST](deny-assignments-rest.md)