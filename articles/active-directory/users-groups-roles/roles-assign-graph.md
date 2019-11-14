---
title: Atribuir funções de administrador do Azure AD com a API Microsoft Graph | Microsoft Docs
description: Atribuir e remover funções de administrador do Azure AD com o API do Graph no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f5be5829843e9857239ca5ea9a7395f569f563a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025334"
---
# <a name="assign-custom-admin-roles-using-graph-api-in-azure-active-directory"></a>Atribuir funções de administrador personalizadas usando API do Graph no Azure Active Directory 

Você pode automatizar a forma como atribui funções a contas de usuário Microsoft Graph API. Este artigo aborda as operações POST, GET e DELETE no roleAssignments.

## <a name="required-permissions"></a>Permissões necessárias

Conecte-se ao seu locatário do Azure AD usando uma conta de administrador global ou administrador de identidade privilegiada para atribuir ou remover funções.

## <a name="post-operations-on-roleassignment"></a>Operações POST no RoleAssignment

Solicitação HTTP para criar uma atribuição de função entre um usuário e uma definição de função.

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

corpo

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

resposta

``` HTTP
HTTP/1.1 201 Created
```

Solicitação HTTP para criar uma atribuição de função na qual a definição de entidade ou função não existe

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

corpo

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

resposta

``` HTTP
HTTP/1.1 404 Not Found
```

Solicitação HTTP para criar uma atribuição de função com escopo de recurso único em uma definição de função interna.

> [!NOTE] 
> Atualmente, as funções internas têm uma limitação em que podem ser escopos somente para o escopo "/" de toda a organização ou o escopo "/AU/*". O escopo de recurso único não funciona para funções internas, mas funciona para funções personalizadas.

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

corpo

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
}
```

resposta

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>OBTER operações no RoleAssignment

Solicitação HTTP para obter uma atribuição de função para uma determinada entidade

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=principalId eq ‘<object-id-of-principal>’
```

resposta

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

Solicitação HTTP para obter uma atribuição de função para uma determinada definição de função.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

resposta

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

Solicitação HTTP para obter uma atribuição de função por ID.

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

resposta

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>Operações de exclusão no RoleAssignment

Solicitação HTTP para excluir uma atribuição de função entre um usuário e uma definição de função.

EXCLUIR

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

resposta
``` HTTP
HTTP/1.1 204 No Content
```

Solicitação HTTP para excluir uma atribuição de função que não existe mais

EXCLUIR

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

resposta

``` HTTP
HTTP/1.1 404 Not Found
```

Solicitação HTTP para excluir uma atribuição de função entre a definição de função própria e interna

EXCLUIR

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

resposta

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](directory-assign-admin-roles.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
