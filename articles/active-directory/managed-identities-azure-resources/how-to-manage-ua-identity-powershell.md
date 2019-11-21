---
title: Create, list & delete user-assigned managed identity using Azure PowerShell - Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir a identidade gerenciada atribuída ao usuário usando o Azure PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3c4005643de55720864da1e87d8bea82b21749c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232244"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Criar, listar ou excluir uma identidade gerenciada atribuída ao usuário usando o Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você aprenderá como criar, listar e excluir uma identidade gerenciada atribuída ao usuário usando o Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a seção de [visão geral](overview.md). **Não deixe de analisar a [diferença entre uma identidade gerenciada atribuída pelo sistema e atribuída pelo usuário](overview.md#how-does-it-work)** .
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps) se ainda não o fez.
- Se estiver executando o PowerShell localmente, também será possível: 
    - Execute `Connect-AzAccount` para criar uma conexão com o Azure.
    - Instale a [versão mais recente do PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Execute `Install-Module -Name PowerShellGet -AllowPrerelease` para obter a versão de pré-lançamento do módulo `PowerShellGet` (talvez você precise fazer `Exit` da sessão atual do PowerShell depois de executar este comando para instalar o módulo `Az.ManagedServiceIdentity`).
    - Execute `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` para instalar a versão de pré-lançamento do módulo `Az.ManagedServiceIdentity` para executar as operações de identidade gerenciada atribuídas ao usuário neste artigo.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função [Administrador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Para criar uma identidade gerenciada atribuída pelo usuário, use o comando `New-AzUserAssignedIdentity`. O parâmetro `ResourceGroupName` especifica o grupo de recursos no qual a identidade gerenciada atribuída ao usuário é criada e o parâmetro `-Name` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas ao usuário

Para listar/ler uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Para listar as identidades gerenciadas atribuídas pelo usuário, use o comando [Get-AzUserAssigned].  O parâmetro `-ResourceGroupName` especifica o grupo de recursos em que a identidade gerenciada atribuída ao usuário foi criada. Substitua o `<RESOURCE GROUP>` por seu próprio valor:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Na resposta, as identidades gerenciadas atribuídas ao usuário têm `"Microsoft.ManagedIdentity/userAssignedIdentities"` valor retornado para a chave, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída ao usuário

Para excluir uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Para excluir uma identidade gerenciada atribuída pelo usuário, use o comando `Remove-AzUserAssignedIdentity`.  O parâmetro `-ResourceGroupName` especifica o grupo de recursos em que a identidade atribuída ao usuário foi criada e o parâmetro `-Name` especifica o nome. Substitua os valores de parâmetros `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída ao usuário não removerá a referência de nenhum recurso ao qual foi atribuída. Atribuições de identidade devem ser removidas separadamente.

## <a name="next-steps"></a>Próximos passos

Para obter uma lista completa e mais detalhes sobre as identidades gerenciadas do Azure PowerShell para os comandos de recursos do Azure, confira [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
