---
title: Gerenciar identidade gerenciada atribuída pelo usuário-CLI do Azure-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade gerenciada atribuída ao usuário usando a CLI do Azure.
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
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 760de0d0bd1f11a34aa5c3998c923d1c7c80185d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232257"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Criar, listar ou excluir uma identidade gerenciada atribuída ao usuário usando a CLI do Azure


As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure AD sem precisar ter as credenciais no seu código. 

Neste artigo, você aprende como criar, listar e excluir uma identidade gerenciada atribuída ao usuário usando a CLI do Azure.

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a seção de [visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-it-work)** .
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, você tem três opções:
    - Usar o [Azure Cloud Shell](../../cloud-shell/overview.md) no Portal do Azure (confira a próxima seção).
    - Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou mais recente), se você preferir usar um console da CLI local. Entre no Azure usando `az login`, usando uma conta associada à assinatura do Azure na qual você quer implantar a identidade gerenciada atribuída ao usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário 

Para criar uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Use o comando [az identity create](/cli/azure/identity#az-identity-create) para criar uma identidade gerenciada atribuída ao usuário. O parâmetro `-g` especifica o grupo de recursos no qual a identidade gerenciada atribuída ao usuário é criada e o parâmetro `-n` especifica o nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas ao usuário

Para listar/ler uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Para listar as identidades gerenciadas atribuídas ao usuário, use o comando [az identity list](/cli/azure/identity#az-identity-list). Substitua o `<RESOURCE GROUP>` por seu próprio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Na resposta json, as identidades gerenciadas atribuídas ao usuário têm valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` retornado para a chave, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída ao usuário

Para excluir uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Para excluir uma identidade gerenciada atribuída ao usuário, use o comando [az identity delete](/cli/azure/identity#az-identity-delete).  O parâmetro -n especifica o nome e o parâmetro -g especifica o grupo de recursos em que a identidade gerenciada atribuída ao usuário foi criada. Substitua os valores de parâmetros `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` pelos seus próprios valores:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída ao usuário não removerá a referência de nenhum recurso ao qual foi atribuída. Remova-as da VM/VMSS usando o comando `az vm/vmss identity remove`

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista completa de comandos de identidade da CLI do Azure, consulte [az identity](/cli/azure/identity).

Para obter informações sobre como atribuir uma identidade gerenciada atribuída ao usuário a uma VM do Azure, consulte, [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
