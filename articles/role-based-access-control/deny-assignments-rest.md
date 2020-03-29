---
title: Lista negar atribuições para recursos do Azure com a API REST
description: Saiba como listar atribuições de negar para usuários, grupos e aplicativos usando o RBAC (Role-Based Access Control, controle de acesso baseado em função) para recursos do Azure e para a API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063024"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Listar atribuições de negação para recursos do Azure usando a API REST

[Negar atribuições](deny-assignments.md) bloqueie os usuários de executar ações específicas de recursos do Azure, mesmo que uma atribuição de função lhes conceda acesso. Este artigo descreve como listar atribuições de negação usando a API REST.

> [!NOTE]
> Você não pode criar diretamente suas próprias atribuições de negação. Para obter informações sobre como as atribuições de negação são criadas, consulte [As atribuições de Negar](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, você deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria [das funções incorporadas para os recursos do Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista uma única atribuição de negação

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as atribuições de negação.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{deny-assignment-id}* pelo identificador de atribuição de negação que você deseja recuperar.

## <a name="list-multiple-deny-assignments"></a>Listar várias atribuições de negação

1. Inicie com uma das seguintes solicitações:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Com parâmetros opcionais:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as atribuições de negação.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subscription |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuição de negação.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | (sem filtro) | As listas negam todas as atribuições em, acima e abaixo do escopo especificado. |
    > | `$filter=atScope()` | As listas negam atribuições apenas para o escopo especificado e acima. Não inclui as atribuições de negação em sub-escopos. |
    > | `$filter=assignedTo('{objectId}')` | As listas negam atribuições para o usuário ou o diretor de serviço especificado.<br/>Se o usuário é membro de um grupo que tem uma atribuição de negação, essa atribuição de negação também está listada. Este filtro é transitivo para grupos, o que significa que se o usuário é membro de um grupo e esse grupo é membro de outro grupo que tem uma atribuição de negação, que negar a atribuição também é listado.<br/>Este filtro só aceita um ID de objeto para um usuário ou um diretor de serviço. Você não pode passar um ID de objeto para um grupo. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | As listas negam atribuições para o usuário ou principal de serviço especificado e no escopo especificado. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | As listas negam atribuições com o nome especificado. |
    > | `$filter=principalId+eq+'{objectId}'` | As listas negam atribuições para o usuário, grupo ou principal de serviço especificado. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Listar atribuições de negação no escopo raiz (/)

1. Elevar o acesso conforme descrito em [Elevar acesso para um Administrador Global no Azure Active Directory](elevate-access-global-admin.md).

1. Envie a seguinte solicitação:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuição de negação. Um filtro é exigido.

    > [!div class="mx-tableFixed"]
    > | Filtrar | Descrição |
    > | --- | --- |
    > | `$filter=atScope()` | Listar atribuições de negação apenas no escopo raiz. Não inclui as atribuições de negação em sub-escopos. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Listar atribuições de negação com o nome especificado. |

1. Remover acesso elevado.

## <a name="next-steps"></a>Próximas etapas

- [Compreender atribuições de negação dos recursos do Azure](deny-assignments.md)
- [Elevar o acesso para um administrador global no Azure Active Directory](elevate-access-global-admin.md)
- [Referência da API Azure REST](/rest/api/azure/)
