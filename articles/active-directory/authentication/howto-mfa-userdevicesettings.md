---
title: Gerenciar as configurações do usuário para autenticação multifatorial do Azure - Diretório Ativo do Azure
description: Saiba como configurar as configurações do usuário do Azure Active Directory para autenticação multifatorial do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309765"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gerenciar as configurações do usuário para autenticação multifatorial do Azure

Para ajudar a gerenciar os usuários da Autenticação Multifatorial do Azure, você pode exigir que os usuários remarquem sua senha, registrem-se para OMFA ou revoguem as sessões de MFA existentes. Para usuários que definiram senhas de aplicativos, você também pode optar por excluir essas senhas, fazendo com que a autenticação legada falhe nesses aplicativos. Essas ações podem ser necessárias se você precisar prestar assistência a um usuário ou quiser redefinir seu status de segurança.

## <a name="manage-user-authentication-options"></a>Gerenciar opções de autenticação do usuário

Se você tiver a função *administrador de autenticação,* poderá exigir que os usuários resetem sua senha, registrem-se no MFA ou revoguem as sessões MFA existentes do objeto do usuário. Para gerenciar as configurações do usuário, complete as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Usuários ativos do Azure** > **Users** > **Todos os usuários**.
1. Escolha o usuário que deseja executar uma ação e selecione **métodos de autenticação**. Na parte superior da janela, escolha uma das seguintes opções para o usuário:
   - **Redefinir senha** redefine a senha do usuário e atribui uma senha temporária que deve ser alterada no próximo login.
   - **Exigir o recadastramento MFA** faz com que, quando o usuário assinar na próxima vez, ele seja solicitado a configurar um novo método de autenticação MFA.
   - **Revogar as sessões do MFA** limpa as sessões de MFA lembradas pelo usuário e exige que eles realizem o MFA na próxima vez que for exigido pela política no dispositivo.

   ![Gerenciar métodos de autenticação a partir do portal Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Se necessário, você pode excluir todas as senhas do aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixam de funcionar até que uma nova senha de aplicativo seja criada. *As* permissões de administrador global são necessárias para executar essa ação.

Para excluir as senhas do aplicativo de um usuário, complete as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No lado esquerdo, selecione Usuários ativos do **Azure** > **Users** > Todos**os usuários**.
1. Selecione **Autenticação multifatorial**. Você pode precisar rolar para a direita para ver esta opção de menu. Selecione a captura de tela de exemplo abaixo para ver a janela completa do portal Azure e a localização do menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecione Autenticação multifatorial na janela Usuários no Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de passos rápidos aparece à direita.
1. Selecione **Gerenciar as configurações do usuário**e, em seguida, marque a caixa para excluir todas as **senhas de aplicativos existentes geradas pelos usuários selecionados,** conforme mostrado no exemplo a seguir: ![Exclua todas as senhas existentes do aplicativo](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecione **salvar** **e**fechar .

## <a name="next-steps"></a>Próximas etapas

Este artigo ajudou a configurar configurações individuais do usuário. Para configurar as configurações de serviço de autenticação multifatorial do Azure, consulte [Configure configurações de autenticação multifatorial do Azure](howto-mfa-mfasettings.md)

Se seus usuários precisarem de ajuda, consulte o [guia usuário para autenticação multifatorial do Azure](../user-help/multi-factor-authentication-end-user.md).
