---
title: Habilitar configurações de Colaboração B2B externa – Azure AD
description: Saiba como habilitar a Colaboração B2B externa do Active Directory e gerenciar quem pode enviar convites para usuários convidados. Use a função Emissor de convites independente para delegar convites.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf872991adbf1361a482f6618e57304e33ec1261
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591277"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Habilitar a Colaboração B2B externa e gerenciar quem pode enviar convites

Este artigo descreve como habilitar a Colaboração B2B do Azure Active Directory (Azure AD) e determinar quem pode enviar convites. Por padrão, todos os usuários e convidados em seu diretório podem enviar convites, mesmo que não sejam administradores. As configurações de colaboração externa permitem ativar ou desativar o envio de convites de diversos tipos de usuários em sua organização. Você também pode delegar convites a usuários individuais, atribuindo funções que lhes permitam enviar convites.

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
6. Na página **Configurações de colaboração externa**, escolha as políticas que você deseja habilitar.

   ![Configuração de colaboração externa](./media/delegate-invitations/control-who-to-invite.png)

  - **Permissões de usuários convidados são limitadas**: Essa política determina as permissões dos convidados no seu diretório. Selecione **Sim** para bloquear o acesso dos convidados a determinadas tarefas de diretório, como enumerar usuários, grupos ou outros recursos de diretório. Selecione **Não** para dar aos convidados o mesmo acesso aos dados do diretório que os usuários comuns possuem.
   - **Administradores e usuários na função de emissor de convites independente podem convidar**: Para permitir que administradores e usuários na função "Emissor de convites independente" enviem convites, defina essa política como **Sim**.
   - **Membros podem convidar**: Para permitir que membros não administradores do seu diretório enviem convites, defina essa política como **Sim**.
   - **Convidados podem convidar**: Para permitir que convidados convidem outros convidados, defina essa política como **Sim**.
   - **Habilitar senha avulsa por email para convidados (versão prévia)** : Para obter mais informações sobre o recurso de senha avulsa, confira [Autenticação por senha avulsa por email (versão prévia)](one-time-passcode.md).
   - **Restrições de colaboração**: Para obter mais informações sobre como permitir ou bloquear convites em domínios específicos, confira [Permitir ou bloquear convites para usuários B2B de organizações específicas](allow-deny-list.md).

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


