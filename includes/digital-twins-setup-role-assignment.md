---
author: baanders
description: arquivo de inclusão da etapa permissões de acesso na instalação do gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 6f43a55b3a409a84e3baf99dae24af7616ea4385
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87408228"
---
O Azure digital gêmeos usa o [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) para o RBAC (controle de acesso baseado em função). Isso significa que, antes que um usuário possa fazer chamadas de plano de dados para sua instância do gêmeos digital do Azure, esse usuário precisa receber uma função com as permissões apropriadas para ele.

Para o Azure digital gêmeos, essa função é o _**proprietário do gêmeos digital do Azure (versão prévia)**_. Você pode ler mais sobre funções e segurança em [*conceitos: segurança para soluções de gêmeos digitais do Azure*](../articles/digital-twins/concepts-security.md).

Esta seção mostrará como criar uma atribuição de função para um usuário em sua instância do gêmeos digital do Azure, usando o email desse usuário no locatário do Azure AD em sua assinatura do Azure. Dependendo de sua função em sua organização, você pode configurar essa permissão para si mesmo ou configurá-la em nome de outra pessoa que gerenciará a instância de gêmeos digital do Azure.

### <a name="assign-the-role"></a>Atribuir a função

Para conceder a um usuário permissões para gerenciar uma instância do gêmeos digital do Azure, você deve atribuir a função de _**proprietário do gêmeos digital do Azure (versão prévia)**_ na instância.

> [!NOTE]
> Observe que essa função é diferente da função de *proprietário* do Azure AD, que também pode ser atribuída no escopo da instância de gêmeos digital do Azure. Essas são duas funções de gerenciamento distintas, e o *proprietário* do Azure ad não concede acesso aos recursos do plano de dados que são concedidos com o *proprietário do Azure digital gêmeos (versão prévia)*.