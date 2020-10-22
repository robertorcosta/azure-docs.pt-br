---
title: Habilitar configurações de Colaboração B2B externa – Azure AD
description: Saiba como habilitar a Colaboração B2B externa do Active Directory e gerenciar quem pode enviar convites para usuários convidados. Use a função Emissor de convites independente para delegar convites.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a983931bd372931eacff2f7b21f3358f536046
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92362919"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Habilitar a Colaboração B2B externa e gerenciar quem pode enviar convites

Este artigo descreve como habilitar a colaboração B2B do Azure Active Directory (Azure AD), designar quem pode convidar convidados e determinar as permissões que os usuários convidados têm no Azure AD. 

Por padrão, todos os usuários e convidados em seu diretório podem enviar convites, mesmo que não sejam administradores. As configurações de colaboração externa permitem ativar ou desativar o envio de convites de diversos tipos de usuários em sua organização. Você também pode delegar convites a usuários individuais, atribuindo funções que lhes permitam enviar convites.

O Azure AD permite que você restrinja o que os usuários convidados externos podem ver em seu diretório do AD do Azure. Por padrão, os usuários convidados são definidos com um nível de permissão limitado que os impede de enumerar usuários, grupos ou outros recursos de diretório, mas permite que eles vejam a associação de grupos não ocultos. Uma nova configuração de visualização permite restringir ainda mais o acesso de convidado, para que os convidados possam exibir apenas suas próprias informações de perfil. Para obter detalhes, consulte [restringir permissões de acesso de convidado (versão prévia)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>Definir configurações de Colaboração B2B externa

Com a colaboração B2B do Azure AD, um administrador de locatário pode definir as seguintes políticas de convite:

- Desativar convites
- Somente administradores e usuários na função Emissor de convite para convidado podem convidar
- Administradores, a função Emissor de convite para convidado e membros podem convidar
- Todos os usuários, incluindo convidados, podem convidar

Por padrão, todos os usuários, incluindo convidados, podem enviar convites.

### <a name="to-configure-external-collaboration-settings"></a>Para definir configurações de colaboração externa:

1. Entre no [portal do Azure](https://portal.azure.com) como administrador de locatários.
2. Selecione **Azure Active Directory**.
3. Selecione **Identidades Externas** > **Configurações de colaboração externa**.

4. Em **restrições de acesso do usuário convidado (versão prévia)**, escolha o nível de acesso que você deseja que os usuários convidados tenham:

   - **Os usuários convidados têm o mesmo acesso que os membros (mais inclusivo)**: essa opção fornece aos convidados o mesmo acesso aos recursos do AD do Azure e aos dados de diretório como usuários membros.

   - **Os usuários convidados têm acesso limitado a propriedades e associações de objetos de diretório**: (padrão) essa configuração bloqueia convidados de determinadas tarefas de diretório, como a enumeração de usuários, grupos ou outros recursos de diretório. Os convidados podem ver a associação de todos os grupos não ocultos.

   - **O acesso do usuário convidado é restrito a propriedades e associações de seus próprios objetos de diretório (mais restritivos)**: com essa configuração, os convidados podem acessar somente seus próprios perfis. Os convidados não têm permissão para ver os perfis, grupos ou associações de grupo dos outros usuários.
  
    ![Configurações de restrições de acesso do usuário convidado](./media/delegate-invitations/guest-user-access.png)

5. Em **configurações de convite de convidado**, escolha as configurações apropriadas:

   - **Administradores e usuários na função de emissor de convites independente podem convidar**: Para permitir que administradores e usuários na função "Emissor de convites independente" enviem convites, defina essa política como **Sim**.

   - **Membros podem convidar**: Para permitir que membros não administradores do seu diretório enviem convites, defina essa política como **Sim**.

   - **Convidados podem convidar**: Para permitir que convidados convidem outros convidados, defina essa política como **Sim**.

   - **Habilitar email One-Time senha para convidados (visualização)**: para obter mais informações sobre o recurso de senha de uso único, consulte [autenticação de senha de uso único de email (versão prévia)](one-time-passcode.md).

   - **Habilitar inscrição de autoatendimento de convidado por meio de fluxos de usuário (visualização)**: para obter mais informações sobre essa configuração, consulte [Adicionar um fluxo de usuário de inscrição de autoatendimento a um aplicativo (versão prévia)](self-service-sign-up-user-flow.md).

   > [!NOTE]
   > Se **os membros puderem convidar** for definido como **não** e **Administradores e os usuários na função de convite do convidado puderem convidar** estiver definido como **Sim**, os usuários na função emissor de **convite do convidado** ainda poderão convidar convidados.

    ![Configurações de convite de convidado](./media/delegate-invitations/guest-invite-settings.png)

6. Em **restrições de colaboração**, escolha se deseja permitir ou negar convites para os domínios que você especificar. Para obter mais informações, consulte [Permitir ou bloquear convites para usuários B2B de organizações específicas](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Atribuir a função Emissor de convites independente a um usuário

Com a função Emissor de convites independente, você pode dar aos usuários individuais a capacidade de enviar convites sem atribuir a eles um administrador global ou outra função de administrador. Atribua a Função Emissor de convites independente às pessoas. Não se esqueça de definir **Administradores e usuários na função de emissor de convites independente podem convidar** como **Sim**.

Veja um exemplo que mostra como usar o PowerShell para adicionar um usuário à função Emissor de convite para convidado:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Adicionar usuários convidados da colaboração B2B sem um convite](add-user-without-invite.md)
- [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)

