---
title: Usuários e grupos na política de acesso condicional-Azure Active Directory
description: Quem são usuários e grupos em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17312e44714c8bdb20e22ad9aeb950e46eb71e3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755268"
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
   - Todos os usuários convidados e externos
      - Essa seleção inclui quaisquer convidados B2B e usuários externos, incluindo qualquer usuário com `user type` o atributo definido `guest`como. Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um provedor de soluções de nuvem (CSP). 
   - Funções de diretório
      - Permite que os administradores selecionem funções de diretório específicas do Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva em usuários com a função de administrador global atribuída.
   - Usuários e grupos
      - Permite direcionar conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contém todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo de nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

## <a name="exclude-users"></a>Excluir usuários

Quando as organizações incluem e excluem um usuário ou grupo, o usuário ou grupo é excluído da política, uma vez que uma ação de exclusão substitui uma inclusão na política. As exclusões são geralmente usadas para contas de acesso de emergência ou de vidro. Mais informações sobre contas de acesso de emergência e por que elas são importantes podem ser encontradas nos seguintes artigos: 

* [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../users-groups-roles/directory-emergency-access.md)
* [Criar uma estratégia de gerenciamento de controle de acesso resiliente com o Azure Active Directory](../authentication/concept-resilient-controls.md)

As opções a seguir estão disponíveis para exclusão ao criar uma política de acesso condicional.

- Todos os usuários convidados e externos
   - Essa seleção inclui quaisquer convidados B2B e usuários externos, incluindo qualquer usuário com `user type` o atributo definido `guest`como. Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um provedor de soluções de nuvem (CSP). 
- Funções de diretório
   - Permite que os administradores selecionem funções de diretório específicas do Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva em usuários com a função de administrador global atribuída.
- Usuários e grupos
   - Permite direcionar conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contém todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo de nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

### <a name="preventing-administrator-lockout"></a>Impedindo o bloqueio do administrador

Para impedir que um administrador bloqueie seu diretório ao criar uma política aplicada a **todos os usuários** e a **todos os aplicativos**, eles verão o aviso a seguir.

> Não se bloqueie! É recomendável aplicar uma política a um pequeno conjunto de usuários primeiro para verificar se ele se comporta conforme o esperado. Também recomendamos a exclusão de pelo menos um administrador dessa política. Isso garante que você ainda terá acesso e poderá atualizar uma política se uma alteração for necessária. Examine os usuários e aplicativos afetados.

Por padrão, a política fornecerá uma opção para excluir o usuário atual da política, mas esse padrão pode ser substituído pelo administrador, conforme mostrado na imagem a seguir. 

![Aviso, não bloqueie-se!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional: aplicativos de nuvem ou ações](concept-conditional-access-cloud-apps.md)

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
