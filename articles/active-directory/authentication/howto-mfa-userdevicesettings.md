---
title: Manage users and devices Azure MFA - Azure Active Directory
description: How can administrators change user settings such as forcing the users to do the proof-up process again.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fd03ea807e48f6f0e287bb4497e4d20268995db
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404177"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciar configurações de usuário com a Autenticação Multifator do Azure na nuvem

Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo:

* Exigir que os usuários forneçam métodos de contato novamente
* Excluir senhas de aplicativo
* Exigir a MFA em todos os dispositivos confiáveis

## <a name="manage-authentication-methods"></a>Manage authentication methods

As an administrator assigned the Authentication Administrator role you can require users to reset their password, re-register for MFA, or revoke existing MFA sessions from their user object.

![Manage authentication methods from the Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory** > **Usuários** > **Todos os usuário**.
1. Choose the user you wish to perform an action on and select **Authentication methods**.
   - **Reset Password** will reset the user's password and assign a temporary password that must be changed on the next sign in.
   - **Require Re-register MFA** will make it so that when the user signs in next time, they will be requested to setup a new MFA authentication method.
   - **Revoke MFA Sessions** clears the user's remembered MFA sessions and requires them to perform MFA the next time it is required by the policy on the device.

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Essa configuração exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixam de funcionar até que uma nova senha de aplicativo seja criada. Global administrator permissions are required to perform this action.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir senhas de aplicativo de usuários existentes

1. Entre no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários** > **Todos os usuário**.
3. À direita, selecione **Autenticação Multifator** na barra de ferramentas. Abre a página de autenticação multifator.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. A list of quick step options appears on the right.
5. Selecione **Gerenciar configurações de usuário**.
6. Marque a caixa **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.
   ![Delete all existing app passwords](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique em **Salvar**.
8. Clique em **Fechar**.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre como [Definir as configurações da Autenticação Multifator do Azure](howto-mfa-mfasettings.md)
- Se os usuários precisarem de ajuda, direcione-os para [Guia do usuário para verificação em duas etapas](../user-help/multi-factor-authentication-end-user.md)
