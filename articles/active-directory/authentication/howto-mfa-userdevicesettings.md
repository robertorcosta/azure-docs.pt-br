---
title: Gerenciar usuários e dispositivos Azure MFA - Azure Active Directory
description: Como os administradores podem alterar as configurações do usuário, como forçar os usuários a fazer o processo de revisão novamente.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d94fa761980151c420984eb7e8c3254a3509ef
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653489"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciar configurações de usuário com a Autenticação Multifator do Azure na nuvem

Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo:

* Exigir que os usuários forneçam métodos de contato novamente
* Excluir senhas de aplicativo
* Exigir a MFA em todos os dispositivos confiáveis

## <a name="manage-authentication-methods"></a>Gerenciar métodos de autenticação

Como administrador atribuído à função Administrador de Autenticação, você pode exigir que os usuários resete sua senha, registre-se para MFA ou revogue as sessões MFA existentes de seu objeto de usuário.

![Gerenciar métodos de autenticação a partir do portal Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Usuários ativos do Azure** > **Users** > **Todos os usuários**.
1. Escolha o usuário que deseja executar uma ação e selecione **métodos de autenticação**.
   - **Redefinir senha** redefinirá a senha do usuário e atribuirá uma senha temporária que deve ser alterada no próximo login.
   - **Exigir o recadastramento MFA** fará com que, quando o usuário assinar na próxima vez, ele será solicitado a configurar um novo método de autenticação MFA.
   - **Revogar as sessões do MFA** limpa as sessões de MFA lembradas pelo usuário e exige que ele execute o MFA na próxima vez que for exigido pela política no dispositivo.

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Essa configuração exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixam de funcionar até que uma nova senha de aplicativo seja criada. As permissões de administrador global são necessárias para executar essa ação.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir senhas de aplicativo de usuários existentes

1. Entre no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Usuários ativos do Azure** > **Users** > **Todos os usuários**.
3. À direita, selecione **Autenticação Multifator** na barra de ferramentas. Abre a página de autenticação multifator.
4. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de passos rápidos aparece à direita.
5. Selecione **Gerenciar configurações de usuário**.
6. Marque a caixa **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.
   ![Exclua todas as senhas de aplicativos existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Clique **em salvar**.
8. Clique **em fechar**.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como [Definir as configurações da Autenticação Multifator do Azure](howto-mfa-mfasettings.md)
- Se os usuários precisarem de ajuda, direcione-os para [Guia do usuário para verificação em duas etapas](../user-help/multi-factor-authentication-end-user.md)
