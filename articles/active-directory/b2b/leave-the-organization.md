---
title: Deixe uma organização como usuário convidado - Azure Active Directory
description: Mostra como um usuário convidado do Azure AD B2B pode deixar uma organização usando o Painel de Acesso.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272490"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Deixar uma organização como um usuário convidado

Um usuário convidado do Azure Active Directory B2B (Azure AD) pode optar por deixar uma organização a qualquer momento se ele não precisar mais usar aplicativos da organização ou manter qualquer associação. Um usuário pode deixar uma organização por conta própria, sem a necessidade de contatar um administrador.

> [!NOTE]
> Um usuário convidado não pode deixar uma organização se sua conta estiver desativada no inquilino da casa ou no inquilino do recurso. Se sua conta estiver desativada, o usuário convidado precisará entrar em contato com o administração do inquilino, que pode excluir a conta do hóspede ou ativar a conta do hóspede para que o usuário possa deixar a organização.

## <a name="leave-an-organization"></a>Sair da organização

Para sair de uma organização, siga esses passos.

1. Acesse a página do perfil do painel de acesso fazendo uma das seguintes etapas:
   
   - No [portal Azure,](https://portal.azure.com)clique em seu nome no canto superior direito e **selecione Exibir conta**.
   - Abra o [painel de acesso,](https://myapps.microsoft.com)clique em seu nome no canto superior direito e, ao lado **de Organizações,** selecione o ícone de configurações (engrenagem).
 
   ![Captura de tela mostrando as configurações do usuário no Painel de Acesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se você ainda não está conectado à organização que deseja deixar, em **Organizações,** clique **no Login para deixar** o link da organização ao lado do nome da organização. Depois de fazer o fizer o fizer, clique novamente em seu nome no canto superior direito e ao lado **de Organizações,** selecione o ícone de configurações (engrenagem).

3. Em **Organizações**, localizar a organização que deseja sair e selecione **Deixar a organização**.

   ![Captura de tela mostrando a opção Deixar organização na interface do usuário](media/leave-the-organization/LeaveOrg.png)

4. Quando solicitado a confirmar, selecione **Deixar**. 

## <a name="account-removal"></a>Remoção de conta

Quando um usuário deixar uma organização, a conta de usuário é "excluída de forma reversível" no diretório. Por padrão, o objeto do usuário se move para a área **Deleted users** no Azure AD, mas não é excluído permanentemente por 30 dias. Essa exclusão reversível permite ao administrador restaurar a conta de usuário (incluindo grupos e permissões), se o usuário faz uma solicitação para restaurar a conta dentro do período de 30 dias.

Se desejar, um administrador de locatários pode excluir permanentemente a conta a qualquer momento durante o período de 30 dias. Para fazer isso:

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Em **Gerenciar**, selecione **Usuários**.
3. Selecione **Usuários Excluídos**.
4. Selecione a caixa de seleção próxima a um usuário deletado e então selecione **Deletar permanentemente**.

Se você permanentemente deletar um usuário, esta ação será irrevogável.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do Azure AD B2B, consulte [O que é a colaboração do Azure AD B2B?](what-is-b2b.md)



