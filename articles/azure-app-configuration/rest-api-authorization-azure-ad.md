---
title: API REST de configuração de Azure App-Azure Active Directory autorização
description: Usar Azure Active Directory para autorização em relação à configuração de Azure App usando a API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423872"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Autorização de Azure Active Directory – referência da API REST

Quando a autenticação do Azure Active Directory (AD do Azure) é usada, a autorização é manipulada pelo RBAC (controle de acesso baseado em função) do Azure. O RBAC do Azure exige que os usuários sejam atribuídos a funções para conceder acesso aos recursos. Cada função contém um conjunto de ações que os usuários atribuídos à função poderão executar.

## <a name="roles"></a>Funções

As seguintes funções são funções internas que estão disponíveis em assinaturas do Azure por padrão:

- **Proprietário do Azure app de dados de configuração** : essa função fornece acesso completo a todas as operações.
- **Azure app o leitor de dados de configuração** : essa função habilita operações de leitura.

## <a name="actions"></a>Ações

As funções contêm uma lista de ações que os usuários atribuídos a essa função podem executar. Azure App configuração dá suporte às seguintes ações:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Essa ação permite o acesso de leitura aos recursos de valor-chave de configuração de aplicativo, como/kV e/Labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Essa ação permite o acesso de gravação aos recursos chave de configuração de aplicativo-valor.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Essa ação permite que os recursos de chave de configuração de aplicativo sejam excluídos. Observe que a exclusão de um recurso retorna o valor-chave que foi excluído.

## <a name="errors"></a>Errors

```http
HTTP/1.1 403 Forbidden
```

**Motivo:** A entidade de segurança que faz a solicitação não tem as permissões necessárias para executar a operação solicitada.
**Solução:** Atribua a função necessária para executar a operação solicitada para a entidade de segurança que faz a solicitação.

## <a name="managing-role-assignments"></a>Gerenciando atribuições de função

O gerenciamento de atribuições de função é feito usando os procedimentos [do Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) que são padrão em todos os serviços do Azure. É possível fazer isso por meio do CLI do Azure, do PowerShell, do portal do Azure e muito mais. A documentação oficial sobre como fazer atribuições de função pode ser encontrada [aqui](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
