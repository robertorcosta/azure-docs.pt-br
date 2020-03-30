---
title: Grupos dinâmicos e colaboração B2B - Azure Active Directory | Microsoft Docs
description: Mostra como usar os grupos dinâmicos do Azure AD com a colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226920"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e Colaboração do Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
A configuração dinâmica da associação de grupo de segurança para o Azure AD (Azure Active Directory) está disponível [no portal do Azure](https://portal.azure.com). Os administradores podem definir regras para preencher grupos criados no Azure AD com base em atributos do usuário (como userType, departamento ou país/região). Os membros podem ser adicionados ou removidos automaticamente de um grupo de segurança com base nas alterações de seus atributos. Esses grupos podem fornecer acesso a aplicativos ou a recursos de nuvem (como sites e documentos do SharePoint) e para atribuir licenças a membros. Leia mais sobre grupos dinâmicos em [Grupos dedicados no Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

O [licenciamento do Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) apropriado é necessário para criar e usar grupos dinâmicos. Saiba mais no artigo [Criar regras baseadas em atributo para associação dinâmica de grupo no Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Criando um grupo dinâmico "todos os usuários"
Você pode criar um grupo contendo todos os usuários dentro de um locatário usando uma regra de associação. Quando os usuários são adicionados ou removidos do locatário no futuro, a associação do grupo é ajustada automaticamente.

1. Faça login no [portal Dozure](https://portal.azure.com) com uma conta que é atribuída ao administrador Global ou função de administrador do usuário no inquilino.
1. Selecione **O Diretório Ativo do Azure**.
2. Em **Gerenciar,** selecione **Grupos**e selecione **Novo grupo**.
1. Na página **Novo Grupo,** em **Tipo de Grupo,** selecione **Segurança**. Insira o **Nome do grupo** e a **Descrição do grupo** para o novo grupo. 
2. No **tipo De associação,** selecione **O Usuário Dinâmico**e selecione Adicionar **consulta dinâmica**. 
4. Acima da caixa de texto **de sintaxe de regra,** selecione **Editar**. Na página **Editar regra de sintaxe,** digite a seguinte expressão na caixa de texto:

   ```
   user.objectId -ne null
   ```
1. Selecione **OK**. A regra aparece na caixa de sintaxe Regra:

   ![Sintaxe de regras para todos os usuários grupo dinâmico](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Selecione **Salvar**. O novo grupo dinâmico agora incluirá usuários convidados B2B, bem como usuários membros.


1. Selecione **Criar** na página **do grupo Novo** para criar o grupo.

## <a name="creating-a-group-of-members-only"></a>Criando apenas um grupo de membros

Se você quiser que seu grupo exclua usuários convidados e inclua apenas membros do seu inquilino, crie um grupo dinâmico conforme descrito acima, mas na caixa **de sintaxe Regra,** digite a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

A imagem a seguir mostra a sintaxe de regras para um grupo dinâmico modificado para incluir apenas membros e excluir convidados.

![Mostra regra onde tipo de usuário é igual a membro](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Criando apenas um grupo de convidados

Também pode ser útil criar um novo grupo dinâmico que contenha apenas os usuários convidados, para que você possa aplicar políticas (como as políticas de acesso condicional do Azure AD) a eles. Crie um grupo dinâmico conforme descrito acima, mas na caixa **de sintaxe Regra,** digite a seguinte expressão:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

A imagem a seguir mostra a sintaxe de regras para um grupo dinâmico modificado para incluir apenas convidados e excluir usuários membros.

![Mostra a regra onde o tipo de usuário é igual a convidado](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Próximas etapas

- [Propriedades de usuário de colaboração B2B](user-properties.md)
- [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)
- [Acesso Condicional para usuários de colaboração B2B](conditional-access.md)

