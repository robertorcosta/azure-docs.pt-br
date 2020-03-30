---
title: Listar atribuições de função usando Azure RBAC e Azure CLI
description: Saiba como determinar quais recursos usuários, grupos, diretores de serviço ou identidades gerenciadas têm acesso ao controle de acesso baseado em função (RBAC) e CLI do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385054"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Listar atribuições de função usando Azure RBAC e Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de função usando o Azure CLI.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa [o gerenciamento de recursos delegado do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="prerequisites"></a>Pré-requisitos

- [Bash em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Listar as atribuições de função de um usuário

Para listar as atribuições de função para um usuário específico, use a [lista de atribuições de função az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Por padrão, somente as atribuições de função para a assinatura atual serão exibidas. Para exibir atribuições de função para a `--all` assinatura atual e abaixo, adicione o parâmetro. Para exibir atribuições de função `--include-inherited` herdadas, adicione o parâmetro.

O exemplo a seguir lista as atribuições de função atribuídas diretamente ao usuário *de contoso.com\@patlong:*

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Listar as atribuições para um grupo de recursos

Para listar as atribuições de função existentes em um escopo de grupo de recursos, use [a lista de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

O exemplo a seguir lista as atribuições de função para o grupo de recursos *de vendas farmacêuticas:*

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Listar atribuições de função de um usuário

Para listar todas as atribuições de função em um escopo de assinatura, use [a lista de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-list). Para obter o ID de assinatura, você pode encontrá-lo na lâmina **assinaturas** no portal Azure ou você pode usar [a lista de contas az](/cli/azure/account#az-account-list).

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Exemplo:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Listar as atribuições de função para um grupo de gerenciamento

Para listar todas as atribuições de função em um escopo de grupo de gerenciamento, use [a lista de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-list). Para obter o ID do grupo de gerenciamento, você pode encontrá-lo na lâmina **de grupos de gerenciamento** no portal Azure ou pode usar a lista [az account management-group](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Exemplo:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada

1. Obtenha o ID do objeto da identidade gerenciada atribuída pelo sistema ou atribuída pelo usuário.

    Para obter o ID do objeto de uma identidade gerenciada atribuída pelo usuário, você pode usar [a lista az ad sp](/cli/azure/ad/sp#az-ad-sp-list) ou [az identity list](/cli/azure/identity#az-identity-list).

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    Para obter o ID do objeto de uma identidade gerenciada atribuída ao sistema, você pode usar [a lista az ad sp](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. Para listar as atribuições de função, use [a lista de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-list).

    Por padrão, somente as atribuições de função para a assinatura atual serão exibidas. Para exibir atribuições de função para a `--all` assinatura atual e abaixo, adicione o parâmetro. Para exibir atribuições de função `--include-inherited` herdadas, adicione o parâmetro.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou remover atribuições de função usando O Zure RBAC e a Cli do Azure](role-assignments-cli.md)
