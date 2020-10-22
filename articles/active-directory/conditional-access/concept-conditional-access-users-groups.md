---
title: Usuários e grupos na política de acesso condicional-Azure Active Directory
description: Quem são usuários e grupos em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d14c9330977296630ee58bc2b508f4304472044c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366353"
---
# <a name="conditional-access-users-and-groups"></a>Acesso condicional: usuários e grupos

Uma política de acesso condicional deve incluir uma atribuição de usuário como um dos sinais no processo de decisão. Os usuários podem ser incluídos ou excluídos das políticas de acesso condicional. Azure Active Directory avalia todas as políticas e garante que todos os requisitos sejam atendidos antes de conceder acesso ao usuário.

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
      - Essa seleção inclui quaisquer convidados B2B e usuários externos, incluindo qualquer usuário com o `user type` atributo definido como `guest` . Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um provedor de soluções de nuvem (CSP). 
   - Funções de diretório
      - Permite que os administradores selecionem funções de diretório específicas do Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva em usuários com a função de administrador global atribuída.
   - Usuários e grupos
      - Permite direcionar conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contém todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo de nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos. A política será aplicada a usuários e grupos aninhados.

> [!WARNING]
> Se os usuários ou grupos forem membros de mais de 2048 grupos, seu acesso poderá ser bloqueado. Esse limite se aplica à associação de grupo direta e aninhada.

> [!WARNING]
> As políticas de acesso condicional não dão suporte a usuários atribuídos a uma função de diretório [com escopo para uma unidade administrativa](../roles/admin-units-assign-roles.md) ou funções de diretório com escopo definido diretamente para um objeto, como por meio de [funções personalizadas](../roles/custom-create.md).

## <a name="exclude-users"></a>Excluir usuários

Quando as organizações incluem e excluem um usuário ou grupo, o usuário ou grupo é excluído da política, uma vez que uma ação de exclusão substitui uma inclusão na política. As exclusões são geralmente usadas para contas de acesso de emergência ou de vidro. Mais informações sobre contas de acesso de emergência e por que elas são importantes podem ser encontradas nos seguintes artigos: 

* [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../roles/security-emergency-access.md)
* [Criar uma estratégia de gerenciamento de controle de acesso resiliente com o Azure Active Directory](../authentication/concept-resilient-controls.md)

As opções a seguir estão disponíveis para exclusão ao criar uma política de acesso condicional.

- Todos os usuários convidados e externos
   - Essa seleção inclui quaisquer convidados B2B e usuários externos, incluindo qualquer usuário com o `user type` atributo definido como `guest` . Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um provedor de soluções de nuvem (CSP). 
- Funções de diretório
   - Permite que os administradores selecionem funções de diretório específicas do Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva em usuários com a função de administrador global atribuída.
- Usuários e grupos
   - Permite direcionar conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contém todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo de nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

### <a name="preventing-administrator-lockout"></a>Impedindo o bloqueio do administrador

Para impedir que um administrador bloqueie seu diretório ao criar uma política aplicada a **todos os usuários** e a **todos os aplicativos**, eles verão o aviso a seguir.

> Não se bloqueie! É recomendável aplicar uma política a um pequeno conjunto de usuários primeiro para verificar se ele se comporta conforme o esperado. Também recomendamos a exclusão de pelo menos um administrador dessa política. Isso garante que você ainda terá acesso e poderá atualizar uma política se uma alteração for necessária. Examine os usuários e aplicativos afetados.

Por padrão, a política fornecerá uma opção para excluir o usuário atual da política, mas esse padrão pode ser substituído pelo administrador, conforme mostrado na imagem a seguir. 

![Aviso, não bloqueie-se!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

[O que fazer se você estiver bloqueado no portal do Azure?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>Próximas etapas

- [Acesso Condicional: Aplicativos na nuvem ou ações](concept-conditional-access-cloud-apps.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)
