---
title: Listar atribuições de função usando o Azure RBAC e a API REST
description: Saiba como determinar quais recursos usuários, grupos, diretores de serviço ou identidades gerenciadas têm acesso ao controle de acesso baseado em função do Azure (RBAC) e à API REST.
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
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062149"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Listar atribuições de função usando o Azure RBAC e a API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de função usando a API REST.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa [o gerenciamento de recursos delegado do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="list-role-assignments"></a>Listar atribuições de função

No RBAC, para listar o acesso, você lista as atribuições de função. Para listar as atribuições de função, use uma das APIs REST [Atribuições de Função - Listar](/rest/api/authorization/roleassignments/list). Para refinar seus resultados, especifique um escopo e um filtro opcional.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções de atribuição.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

    No exemplo anterior, o microsoft.web é um provedor de recursos que se refere a uma instância do App Service. Da mesma forma, você pode usar qualquer outro provedor de recursos e especificar o escopo. Para obter mais informações, consulte [os provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md) e as operações de [provedor de recursos do Azure Resource Manager](resource-provider-operations.md)suportadas.  
     
1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuições de função.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | `$filter=atScope()` | Lista atribuições de função apenas para o escopo especificado, sem incluir as atribuições de função em subscópios. |
    > | `$filter=assignedTo('{objectId}')` | Lista as atribuições de função para um usuário ou diretor de serviço especificado.<br/>Se o usuário for membro de um grupo que tenha uma atribuição de função, essa atribuição de função também será listada. Este filtro é transitivo para grupos, o que significa que se o usuário é membro de um grupo e esse grupo é membro de outro grupo que tem uma atribuição de função, essa atribuição de função também é listada.<br/>Este filtro só aceita um ID de objeto para um usuário ou um diretor de serviço. Você não pode passar um ID de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Lista as atribuições de função para o usuário ou principal de serviço especificado e no escopo especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | Lista as atribuições de função para um usuário, grupo ou diretor de serviço especificado. |

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou remover atribuições de função usando o Azure RBAC e a API REST](role-assignments-rest.md)
- [Referência da API Azure REST](/rest/api/azure/)
