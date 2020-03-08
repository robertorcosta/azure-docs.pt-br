---
title: Gerenciar usuários e dispositivos Azure MFA-Azure Active Directory
description: Como os administradores podem alterar as configurações do usuário, como forçar os usuários a fazer o processo de verificação novamente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378032"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciar configurações de usuário com a Autenticação Multifator do Azure na nuvem

Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo:

* Exigir que os usuários forneçam métodos de contato novamente
* Excluir senhas de aplicativo
* Exigir a MFA em todos os dispositivos confiáveis

## <a name="manage-authentication-methods"></a>Gerenciar métodos de autenticação

Como um administrador atribuiu a função de administrador de autenticação, você pode exigir que os usuários redefinam sua senha, registrem-se novamente para MFA ou revogar sessões de MFA existentes de seu objeto de usuário.

![Gerenciar métodos de autenticação do portal do Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory** > **Usuários** > **Todos os usuário**.
1. Escolha o usuário no qual você deseja executar uma ação e selecione **métodos de autenticação**.
   - **Redefinir senha** redefinirá a senha do usuário e atribuirá uma senha temporária que deve ser alterada na próxima entrada.
   - **Exigir o novo registro da MFA** fará com que, quando o usuário entrar na próxima vez, será solicitado a configurar um novo método de autenticação de MFA.
   - **Revogar sessões de MFA** limpa as sessões do MFA lembradas do usuário e exige que elas executem o MFA na próxima vez que ela for exigida pela política no dispositivo.

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Essa configuração exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixam de funcionar até que uma nova senha de aplicativo seja criada. São necessárias permissões de administrador global para executar esta ação.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir senhas de aplicativo de usuários existentes

1. Entre no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Usuários** > **Todos os usuário**.
3. À direita, selecione **Autenticação Multifator** na barra de ferramentas. Abre a página de autenticação multifator.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
5. Selecione **Gerenciar configurações de usuário**.
6. Marque a caixa **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.
   ![excluir todas as senhas de aplicativo existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique em **Salvar**.
8. Clique em **Fechar**.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Para saber mais sobre como [Definir as configurações da Autenticação Multifator do Azure](howto-mfa-mfasettings.md)
- Se os usuários precisarem de ajuda, direcione-os para [Guia do usuário para verificação em duas etapas](../user-help/multi-factor-authentication-end-user.md)
