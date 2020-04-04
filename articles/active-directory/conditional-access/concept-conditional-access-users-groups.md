---
title: Usuários e grupos na política de Acesso Condicional - Diretório Ativo do Azure
description: Quem são usuários e grupos em uma política de acesso condicional do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ae866959dd2112bacbb6b56e5683e7b3b851a0
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631909"
---
# <a name="conditional-access-users-and-groups"></a>Acesso Condicional: Usuários e grupos

Uma política de acesso condicional deve incluir uma atribuição de usuário como um dos sinais no processo de decisão. Os usuários podem ser incluídos ou excluídos das políticas de Acesso Condicional. 

![Usuário como sinal nas decisões tomadas pelo Acesso Condicional](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Incluir usuários

Essa lista de usuários normalmente inclui todos os usuários que uma organização está direcionando em uma política de Acesso Condicional. 

As seguintes opções estão disponíveis para incluir ao criar uma política de acesso condicional.

- Nenhum
   - Nenhum usuário selecionado
- todos os usuários
   - Todos os usuários que existem no diretório, incluindo convidados B2B.
- Selecionar Usuários e grupos
   - Todos os usuários convidados e externos (visualização)
      - Esta seleção inclui quaisquer convidados B2B `user type` e usuários `guest`externos, incluindo qualquer usuário com o atributo definido para . Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um CSP (Cloud Solution Provider, provedor de soluções em nuvem). 
   - Funções de diretório (pré-visualização)
      - Permite que os administradores selecionem funções específicas de diretório Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva sobre os usuários atribuídos à função de administrador global.
   - Usuários e grupos
      - Permite a segmentação de conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo em nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

## <a name="exclude-users"></a>Excluir usuários

Quando as organizações incluem e excluem um usuário ou grupo, o usuário ou grupo é excluído da política, uma vez que uma ação de exclusão substitui uma inclusão na política. Exclusões são comumente usadas para acesso de emergência ou contas de vidro de quebra. Mais informações sobre contas de acesso a emergências e por que elas são importantes podem ser encontradas nos seguintes artigos: 

* [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../users-groups-roles/directory-emergency-access.md)
* [Criar uma estratégia de gerenciamento de controle de acesso resiliente com o Azure Active Directory](../authentication/concept-resilient-controls.md)

As seguintes opções estão disponíveis para excluir ao criar uma política de acesso condicional.

- Todos os usuários convidados e externos (visualização)
   - Esta seleção inclui quaisquer convidados B2B `user type` e usuários `guest`externos, incluindo qualquer usuário com o atributo definido para . Essa seleção também se aplica a qualquer usuário externo conectado de uma organização diferente, como um CSP (Cloud Solution Provider, provedor de soluções em nuvem). 
- Funções de diretório (pré-visualização)
   - Permite que os administradores selecionem funções específicas de diretório Azure AD usadas para determinar a atribuição. Por exemplo, as organizações podem criar uma política mais restritiva sobre os usuários atribuídos à função de administrador global.
- Usuários e grupos
   - Permite a segmentação de conjuntos específicos de usuários. Por exemplo, as organizações podem selecionar um grupo que contenha todos os membros do departamento de RH quando um aplicativo de RH é selecionado como o aplicativo em nuvem. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo grupos de segurança e distribuição dinâmicos ou atribuídos.

### <a name="preventing-administrator-lockout"></a>Impedindo o bloqueio do administrador

Para evitar que um administrador se bloqueie do diretório ao criar uma política aplicada a **Todos os usuários** e todos os **aplicativos,** eles verão o seguinte aviso.

> Não se tranque! Recomendamos a aplicação de uma política a um pequeno conjunto de usuários primeiro para verificar se comporta como esperado. Também recomendamos excluir pelo menos um administrador desta política. Isso garante que você ainda tenha acesso e possa atualizar uma diretiva se uma alteração for necessária. Por favor, revise os usuários e aplicativos afetados.

Por padrão, a diretiva fornecerá uma opção para excluir o usuário atual da diretiva, mas esse padrão pode ser substituído pelo administrador, conforme mostrado na imagem a seguir. 

![Atenção, não se tranque!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Próximas etapas

- [Acesso Condicional: Aplicativos ou ações em nuvem](concept-conditional-access-cloud-apps.md)

- [Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)
