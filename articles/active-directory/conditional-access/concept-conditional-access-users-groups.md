---
title: Usuários e grupos na política de acesso condicional-Azure Active Directory
description: Quem são usuários e grupos em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192123"
---
# <a name="conditional-access-users-and-groups"></a>Acesso condicional: usuários e grupos

Uma política de acesso condicional deve incluir uma atribuição de usuário como um dos sinais no processo de decisão. Os usuários podem ser incluídos ou excluídos das políticas de acesso condicional. 

![Usuário como um sinal nas decisões tomadas pelo acesso condicional](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Incluir usuários

Essa lista de usuários normalmente inclui todos os usuários que uma organização está direcionando em uma política de acesso condicional. 

As opções a seguir estão disponíveis para inclusão ao criar uma política de acesso condicional.

- Nenhum
   - Nenhum usuário selecionado
- todos os usuários
   - Todos os usuários que existem no diretório, incluindo convidados B2B.
- Selecionar Usuários e grupos
   - Todos os usuários convidados e externos (visualização)
      - Essa seleção inclui quaisquer convidados B2B e usuários externos, incluindo qualquer usuário com o atributo `user type` definido como `guest`. Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um provedor de soluções de nuvem (CSP). 
   - Funções de diretório (versão prévia)
      - Permite que os administradores selecionem funções de diretório específicas do Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva em usuários com a função de administrador global atribuída.
   - Usuários e grupos
      - Permite direcionar conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contém todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo de nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

## <a name="exclude-users"></a>Excluir usuários

As exclusões são geralmente usadas para contas de acesso de emergência ou de vidro. Mais informações sobre contas de acesso de emergência e por que elas são importantes podem ser encontradas nos seguintes artigos: 

* [Gerenciar contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Crie uma estratégia de gerenciamento de controle de acesso resiliente com Azure Active Directory](../authentication/concept-resilient-controls.md)

As opções a seguir estão disponíveis para exclusão ao criar uma política de acesso condicional.

- Todos os usuários convidados e externos (visualização)
   - Essa seleção inclui quaisquer convidados B2B e usuários externos, incluindo qualquer usuário com o atributo `user type` definido como `guest`. Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um provedor de soluções de nuvem (CSP). 
- Funções de diretório (versão prévia)
   - Permite que os administradores selecionem funções de diretório específicas do Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva em usuários com a função de administrador global atribuída.
- Usuários e grupos
   - Permite direcionar conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contém todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo de nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional: aplicativos de nuvem ou ações](concept-conditional-access-cloud-apps.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
