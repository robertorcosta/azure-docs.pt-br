---
title: Gerenciar identidades gerenciadas atribuídas pelo usuário usando REST-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade gerenciada atribuída ao usuário para fazer chamadas à API REST.
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
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547424"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Criar, listar ou excluir uma identidade gerenciada atribuída ao usuário usando chamadas à API REST

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades gerenciadas para recursos do Azure fornecem aos serviços do Azure a capacidade de autenticação em serviços que dão suporte à autenticação do Azure AD, sem a necessidade de credenciais no código. 

Neste artigo, você aprenderá como criar, listar e excluir uma identidade gerenciada atribuída ao usuário usando CURL para fazer chamadas à API REST.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- Se você usa o [subsistema do Windows para Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) ou um [SO de distribuição do Linux](/cli/azure/install-azure-cli), [instale o console local da CLI do Azure](https://msdn.microsoft.com/commandline/wsl/about).
- Se você estiver usando console local da CLI do Azure, entre no Azure usando `az login` com uma conta associada à assinatura do Azure que você quer para implantar ou recuperar informações de identidade gerenciada atribuída ao usuário.
- Recupere um token de acesso do Portador usando `az account get-access-token` para executar as seguintes operações de identidade gerenciada atribuída ao usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário 

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função [Administrador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Obrigatórios. Defina como `application/json`.        |
|*Nesse*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        |

**Corpo da solicitação**

|Nome  |Descrição  |
|---------|---------|
|local     | Obrigatórios. Local do recurso.        |

## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas ao usuário

Para listar/ler uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Obrigatórios. Defina como `application/json`.        |
|*Nesse*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída ao usuário

Para excluir uma identidade gerenciada atribuída ao usuário, a conta precisa da atribuição de função [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída ao usuário não removerá a referência de nenhum recurso ao qual foi atribuída. Para remover uma identidade gerenciada atribuída ao usuário de uma VM usando CURL, consulte [Remover uma identidade atribuída ao usuário de uma VM do Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Cabeçalho da solicitação  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Obrigatórios. Defina como `application/json`.        |
|*Nesse*     | Obrigatórios. Defina como um `Bearer` token de acesso válido.        |

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como atribuir uma identidade gerenciada atribuída ao usuário a uma VM/VMSS do Azure usando CURL, consulte, [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando chamadas à API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) e [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando chamadas à API REST](qs-configure-rest-vmss.md#user-assigned-managed-identity).
