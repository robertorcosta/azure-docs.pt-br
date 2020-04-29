---
title: Criar ou atualizar funções personalizadas para recursos do Azure usando CLI do Azure | Microsoft Docs
description: Saiba como listar, criar, atualizar ou excluir funções personalizadas com RBAC (controle de acesso baseado em função) para recursos do Azure usando CLI do Azure.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062222"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Criar ou atualizar funções personalizadas para recursos do Azure usando CLI do Azure

> [!IMPORTANT]
> A adição de um grupo `AssignableScopes` de gerenciamento ao está em visualização no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se as [funções internas para os recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Este artigo descreve como listar, criar, atualizar ou excluir funções personalizadas usando CLI do Azure.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, consulte [tutorial: criar uma função personalizada para recursos do Azure usando CLI do Azure](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, você precisará:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) ou [CLI do Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar as funções personalizadas disponíveis para atribuição, use a [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list). Os exemplos a seguir listam todas as funções personalizadas na assinatura atual.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Listar uma definição de função personalizada

Para listar uma definição de função personalizada, use a [lista de definição de função AZ](/cli/azure/role/definition#az-role-definition-list). Esse é o mesmo comando que você usaria para uma função interna.

```azurecli
az role definition list --name <role_name>
```

O exemplo a seguir lista a definição da função *operador de máquina virtual* :

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

O exemplo a seguir lista apenas as ações da função *operador de máquina virtual* :

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar um arquivo personalizado, use [criar definição de função az](/cli/azure/role/definition#az-role-definition-create). A definição de função pode ser uma descrição de JSON ou um caminho para um arquivo que contém uma descrição de JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

O exemplo a seguir cria uma função personalizada denominada *Operador de Máquina Virtual*. Essa função personalizada atribui acesso a todas as operações de leitura dos provedores de recursos *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network*, além de atribuir acesso para iniciar, reiniciar e monitorar máquinas virtuais. Essa função personalizada pode ser usada em duas assinaturas. Este exemplo utiliza um arquivo JSON como entrada.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, primeiro use [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list) para recuperar a definição de função. Depois, faça as alterações desejadas na definição da função. Por fim, use [atualizar definição de função az](/cli/azure/role/definition#az-role-definition-update) para salvar a definição de função atualizada.

```azurecli
az role definition update --role-definition <role_definition>
```

O exemplo a seguir adiciona a operação *Microsoft. insights/diagnosticSettings/* ao `Actions` e adiciona um grupo de `AssignableScopes` gerenciamento ao para a função personalizada *operador de máquina virtual* . A adição de um grupo `AssignableScopes` de gerenciamento ao está em visualização no momento.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Para excluir um arquivo personalizado, use [excluir definição de função az](/cli/azure/role/definition#az-role-definition-delete). Para especificar a função a ser excluída, use o nome da função ou a ID de função. Para determinar a ID de função, use [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

O exemplo a seguir exclui a função personalizada *Operador de Máquina Virtual*.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: criar uma função personalizada para recursos do Azure usando CLI do Azure](tutorial-custom-role-cli.md)
- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Operações do provedor de recursos Azure Resource Manager](resource-provider-operations.md)