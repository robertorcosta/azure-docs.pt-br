---
title: Adicionar ou remover atribuições de função usando O Zure RBAC e a Cli do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, diretores de serviçoou identidades gerenciadas usando o RBAC (Role-Based Access Control, controle de acesso baseado em função) do Azure e do Azure CLI.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245662"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Adicionar ou remover atribuições de função usando O Zure RBAC e a Cli do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [Bash em Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>Obtenha iDs de objeto

Para adicionar ou remover atribuições de função, talvez seja necessário especificar o ID exclusivo de um objeto. O ID tem `11111111-1111-1111-1111-111111111111`o formato: . Você pode obter o ID usando o portal Azure ou Azure CLI.

### <a name="user"></a>Usuário

Para obter o ID do objeto para um usuário Azure AD, você pode usar [aaz ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Agrupar

Para obter o ID do objeto para um grupo Azure AD, você pode usar [a az ad group show](/cli/azure/ad/group#az-ad-group-show) ou [az ad group list](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Aplicativo

Para obter o ID do objeto para um principal de serviço Azure AD (identidade usada por um aplicativo), você pode usar [a lista az ad sp](/cli/azure/ad/sp#az-ad-sp-list). Para um diretor de serviço, use o ID do objeto e **não** o ID do aplicativo.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC, para conceder acesso, você adiciona uma atribuição de função.

### <a name="user-at-a-resource-group-scope"></a>Usuário em um escopo de grupo de recursos

Para adicionar uma atribuição de função para um usuário em um escopo de grupo de recursos, use [a criação de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo a seguir atribui a função *de Contribuinte de Máquina Virtual* ao usuário *patlong\@contoso.com* no escopo do grupo de recursos de *vendas farmacêuticas:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Usando o ID de função única

Há algumas vezes em que um nome de função pode mudar, por exemplo:

- Você está usando seu próprio papel personalizado e decide mudar o nome.
- Você está usando uma função de visualização que tem **(Visualização)** no nome. Quando o papel é lançado, o papel é renomeado.

> [!IMPORTANT]
> Uma versão de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mesmo que um papel seja renomeado, o ID de função não muda. Se você estiver usando scripts ou automação para criar suas atribuições de função, é uma prática recomendada usar o ID de função única em vez do nome da função. Portanto, se uma função for renomeada, seus scripts são mais propensos a funcionar.

Para adicionar uma atribuição de função usando o ID de função exclusiva em vez do nome da função, use [a criação de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

O exemplo a seguir atribui a função [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao usuário de contoso.com *patlong\@* no escopo do grupo de recursos de *vendas farmacêuticas.* Para obter o ID de função exclusivo, você pode usar [a lista de definição de funções az](/cli/azure/role/definition#az-role-definition-list) ou ver [funções incorporadas para recursos do Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Grupo em um escopo de assinatura

Para adicionar uma atribuição de função para um grupo, use [a criação de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter o ID do objeto do grupo, consulte [Obter IDs de objeto](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui o papel *de Leitor* ao grupo Ann *Mack Team* com iD 22222222-2222-2222-2222-2222-22222222222 22 em um escopo de assinatura.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Grupo em um escopo de recursos

Para adicionar uma atribuição de função para um grupo, use [a criação de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter o ID do objeto do grupo, consulte [Obter IDs de objeto](#get-object-ids).

O exemplo a seguir atribui a função *de Contribuinte de Máquina Virtual* ao grupo Ann Mack *Team* com ID 2222222-2222-2222-2222-2222-2222222222 22 22 22 222 em um escopo de recursos para uma rede virtual chamada *pharma-sales-project-network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Aplicativo em um escopo de grupo de recursos

Para adicionar uma atribuição de função para um aplicativo, use [a criação de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create). Para obter informações sobre como obter o ID do objeto do aplicativo, consulte [Obter IDs de objeto](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

O exemplo a seguir atribui a função *contribuinte de máquina virtual* a um aplicativo com iD de objeto 444444444-4444-4444-4444-44444444444444444444nono no escopo do grupo de recursos de *vendas farmacêuticas.*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Usuário em um escopo de assinatura

Para adicionar uma atribuição de função para um usuário em um escopo de assinatura, use [a criação de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create). Para obter o ID de assinatura, você pode encontrá-lo na lâmina **assinaturas** no portal Azure ou você pode usar [a lista de contas az](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

O exemplo a seguir atribui a função *Reader* ao *usuário\@example.com em* um escopo de assinatura.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Usuário em um escopo de grupo de gerenciamento

Para adicionar uma atribuição de função para um usuário em um escopo de grupo de gerenciamento, use [a criação de atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create). Para obter o ID do grupo de gerenciamento, você pode encontrá-lo na lâmina **de grupos de gerenciamento** no portal Azure ou pode usar a lista [az account management-group](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

O exemplo a seguir atribui a função *do Leitor de Faturamento* ao usuário *alain\@example.com* em um escopo de grupo de gerenciamento.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Novo diretor de serviços

Se você criar um novo diretor de serviço e tentar imediatamente atribuir uma função a esse diretor de serviço, essa atribuição de função pode falhar em alguns casos. Por exemplo, se você usar um script para criar uma nova identidade gerenciada e, em seguida, tentar atribuir uma função a esse principal de serviço, a atribuição de função pode falhar. A razão para essa falha é provavelmente um atraso de replicação. O principal serviço é criado em uma região; no entanto, a atribuição de função pode ocorrer em uma região diferente que ainda não replicou o principal de serviço. Para abordar esse cenário, você deve especificar o tipo principal ao criar a atribuição de função.

Para adicionar uma atribuição de função, use a `--assignee-object-id`criação de `--assignee-principal-type` `ServicePrincipal` [atribuição de função az,](/cli/azure/role/assignment#az-role-assignment-create)especifique um valor para e, em seguida, defina para .

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui a função *contribuinte de máquina virtual* à identidade gerenciada pelo teste *msi* no escopo do grupo de recursos *de vendas farmacêuticas:*

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No RBAC, para remover o acesso, você remove uma atribuição de função usando [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

O exemplo a seguir remove a atribuição de *função Contribuinte de Máquina Virtual* do usuário de contoso.com *patlong\@* no grupo de recursos de *vendas farmacêuticas:*

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

O exemplo a seguir remove a função *Reader* do grupo *Ann Mack Team* com iD 22222222-2222-2222-2222-2222-22222222222 22 em um escopo de assinatura. Para obter informações sobre como obter o ID do objeto do grupo, consulte [Obter IDs de objeto](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

O exemplo a seguir remove a função *Leitor de Faturamento* do usuário *alain\@example.com* no escopo do grupo de gerenciamento. Para obter o ID do grupo de gerenciamento, você pode usar [a lista az account management-group](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função usando Azure RBAC e Azure CLI](role-assignments-list-cli.md)
- [Use a CLI do Azure para gerenciar recursos e grupos de recursos do Azure](../azure-resource-manager/cli-azure-resource-manager.md)
