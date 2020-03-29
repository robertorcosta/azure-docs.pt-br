---
title: Adicionar ou remover atribuições de função com o RBAC e a API REST
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, diretores de serviçoou identidades gerenciadas usando o RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure) e a API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063006"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Adicionar ou remover atribuições de função usando o Azure RBAC e a API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando a API REST.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC, para conceder acesso, você adiciona uma atribuição de função. Para adicionar uma atribuição de função, use as [atribuições de função - Crie](/rest/api/authorization/roleassignments/create) a API REST e especifique o principal de segurança, a definição da função e o escopo. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleAssignments/write`. Das funções internas, somente [Proprietário](built-in-roles.md#owner) e [Administrador do Acesso do Usuário](built-in-roles.md#user-access-administrator) recebem permissão para acessar essa operação.

1. Use a API REST [Definições de Função - Listar](/rest/api/authorization/roledefinitions/list) ou veja as [Funções internas](built-in-roles.md) para obter o identificador da definição de função que você deseja atribuir.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para atribuição de função personalizada. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com a solicitação e corpo a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo da atribuição de função.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Recurso |

    No exemplo anterior, o microsoft.web é um provedor de recursos que se refere a uma instância do App Service. Da mesma forma, você pode usar qualquer outro provedor de recursos e especificar o escopo. Para obter mais informações, consulte [os provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md) e as operações de [provedor de recursos do Azure Resource Manager](resource-provider-operations.md)suportadas.  

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

1. Dentro do órgão de solicitação, substitua *{escopo}* com o escopo da atribuição da função.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador de definição da função.

1. Substitua *{principalId}* com o identificador de objeto do usuário, grupo ou entidade de serviço que receberá a função.

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No RBAC, para remover o acesso, você deve remover uma atribuição de função. Para remover uma atribuição de função, use a API REST [Atribuição de Função – Excluir](/rest/api/authorization/roleassignments/delete). Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleAssignments/delete`. Das funções internas, somente [Proprietário](built-in-roles.md#owner) e [Administrador do Acesso do Usuário](built-in-roles.md#user-access-administrator) recebem permissão para acessar essa operação.

1. Obtenha o identificador da atribuição de função (GUID). Esse identificador retorna quando você cria pela primeira vez a atribuição de função, ou você pode obtê-lo listando as atribuições de função.

1. Comece com a solicitação a seguir:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para remoção da atribuição de função.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função usando o Azure RBAC e a API REST](role-assignments-list-rest.md)
- [Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
- [Referência da API Azure REST](/rest/api/azure/)
- [Criar funções personalizadas para recursos do Azure usando a API REST](custom-roles-rest.md)
