---
title: Habilitar configurações de colaboração externa B2B - Azure AD
description: Saiba como ativar a colaboração externa Do Active Directory B2B e gerenciar quem pode convidar os usuários convidados. Use a função de Convidado convidado para delegar convites.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272892"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Habilite a colaboração externa B2B e gerencie quem pode convidar convidados

Este artigo descreve como ativar a colaboração B2B do Azure Active Directory (Azure AD) e determinar quem pode convidar os convidados. Por padrão, todos os usuários e convidados em seu diretório podem convidar os hóspedes mesmo que eles não estejam designados para uma função de admin. As configurações de colaboração externa permitem ativar ou desativar convites de convidados para diferentes tipos de usuários em sua organização. Você também pode delegar convites para usuários individuais, atribuindo funções que lhes permitem convidar convidados.

## <a name="configure-b2b-external-collaboration-settings"></a>Configure as configurações de colaboração externa B2B

Com a colaboração B2B do Azure AD, um administrador de locatário pode definir as seguintes políticas de convite:

- Desativar convites
- Somente administradores e usuários na função Emissor de convite para convidado podem convidar
- Administradores, a função Emissor de convite para convidado e membros podem convidar
- Todos os usuários, incluindo convidados, podem convidar

Por padrão, todos os usuários, incluindo convidados, podem convidar usuários convidados.

### <a name="to-configure-external-collaboration-settings"></a>Para configurar configurações de colaboração externa:

1. Faça login no [portal Azure](https://portal.azure.com) como administrador de inquilinos.
2. Selecione as configurações de **usuário do azure active** > **Users** > **directory**.
3. Em **usuários externos,** **selecione Gerenciar configurações de colaboração externa**.
   > [!NOTE]
   > As **configurações de colaboração externa** também estão disponíveis na página **Relações organizacionais**. No Azure Active Directory, em **Gerenciar**, acesse **Relações organizacionais** > **Configurações**.
4. Na página **Configurações de colaboração externa,** escolha as políticas que deseja habilitar.

   ![Configuração de colaboração externa](./media/delegate-invitations/control-who-to-invite.png)

  - **As permissões dos usuários convidados são limitadas**: Esta política determina permissões para hóspedes em seu diretório. Selecione **Sim** para bloquear os hóspedes de certas tarefas de diretório, como enumerar usuários, grupos ou outros recursos de diretório. Selecione **Não** para dar aos hóspedes o mesmo acesso aos dados do diretório que os usuários regulares em seu diretório.
   - **Os admins e os usuários na função de convidado convidado podem convidar**: Para permitir que os admins e usuários na função "Convidado convidado" convidem convidados, defina esta política como **Sim**.
   - **Os membros podem convidar**: Para permitir que membros não-admin de seu diretório convidem convidados, defina esta política como **Sim**.
   - **Os hóspedes podem convidar**: Para permitir que os hóspedes convidem outros convidados, defina esta política como **Sim**.
   - **Habilite a senha única de e-mail para convidados (Visualização)**: Para obter mais informações sobre o recurso de senha única, consulte [A autenticação de senha única do e-mail (visualização)](one-time-passcode.md).
   - **Restrições de colaboração**: Para obter mais informações sobre como permitir ou bloquear convites para domínios específicos, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Atribuir a função Convidado convidado a um usuário

Com a função De convite convidado, você pode dar aos usuários individuais a capacidade de convidar os convidados sem atribuir a eles um administrador global ou outra função de administrador. Atribuir o papel de convidado convidado a indivíduos. Em seguida, certifique-se de definir **Admins e usuários na função de convidado convidado pode convidar** para **Sim**.

Veja um exemplo que mostra como usar o PowerShell para adicionar um usuário à função Emissor de convite para convidado:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração Azure AD B2B?](what-is-b2b.md)
- [Adicionar usuários convidados da colaboração B2B sem um convite](add-user-without-invite.md)
- [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)


