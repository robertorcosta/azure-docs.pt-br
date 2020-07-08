---
title: Gerenciar configurações de usuário para a autenticação multifator do Azure-Azure Active Directory
description: Saiba como você pode definir Azure Active Directory configurações de usuário para a autenticação multifator do Azure
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
ms.openlocfilehash: 295738ee5943a6cf54bc7e1e3ce4bba621dbe29f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84658690"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Gerenciar configurações de usuário para a autenticação multifator do Azure

Para ajudar a gerenciar os usuários da autenticação multifator do Azure, você pode exigir que os usuários redefinam sua senha, registrem-se novamente para MFA ou revogar as sessões existentes do MFA. Para usuários que definiram senhas de aplicativo, você também pode optar por excluir essas senhas, fazendo com que a autenticação herdada falhe nesses aplicativos. Essas ações poderão ser necessárias se você precisar fornecer assistência a um usuário ou desejar redefinir seu status de segurança.

## <a name="manage-user-authentication-options"></a>Gerenciar opções de autenticação de usuário

Se você tiver atribuído a função de *administrador de autenticação* , poderá exigir que os usuários redefinam sua senha, registrem-se novamente para MFA ou revokem as sessões de MFA existentes de seu objeto de usuário. Para gerenciar as configurações do usuário, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, selecione **Azure Active Directory**  >  **usuários**  >  **todos os usuários**.
1. Escolha o usuário no qual você deseja executar uma ação e selecione **métodos de autenticação**. Na parte superior da janela, escolha uma das seguintes opções para o usuário:
   - **Redefinir senha** redefine a senha do usuário e atribui uma senha temporária que deve ser alterada na próxima entrada.
   - **Exigir o novo registro de MFA** faz isso para que, quando o usuário entrar na próxima vez, ele seja solicitado a configurar um novo método de autenticação de MFA.
   
      > [!NOTE]
      > Os métodos de autenticação atualmente registrados do usuário não são excluídos quando um administrador requer um novo registro para MFA. Depois que um usuário se registra novamente para MFA, recomendamos que eles examinem suas informações de segurança e excluam os métodos de autenticação registrados anteriormente que não podem mais ser usados.
   
   - **Revogar sessões de MFA** limpa as sessões do MFA lembradas do usuário e exige que elas executem o MFA da próxima vez que for exigida pela política no dispositivo.

   ![Gerenciar métodos de autenticação do portal do Azure](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes

Se necessário, você pode excluir todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixam de funcionar até que uma nova senha de aplicativo seja criada. São necessárias permissões de *administrador global* para executar esta ação.

Para excluir as senhas de aplicativo de um usuário, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No lado esquerdo, selecione **Azure Active Directory**  >  **usuários**  >  **todos os usuários**.
1. Selecione **Autenticação Multifator**. Role para a direita para ver esta opção de menu. Selecione a captura de tela de exemplo abaixo para ver toda a janela do portal do Azure e a localização do menu: [![](media/howto-mfa-userstates/selectmfa-cropped.png "Selecionar a Autenticação Multifator na janela Usuários no Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Marque a caixa ao lado do usuário ou usuários que você deseja gerenciar. Uma lista de opções de etapa rápida é exibida à direita.
1. Selecione **gerenciar configurações de usuário**e marque a caixa **excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**, conforme mostrado no exemplo a seguir: ![ excluir todas as senhas de aplicativo existentes](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecione **salvar**e **fechar**.

## <a name="next-steps"></a>Próximas etapas

Este artigo ajudou a definir configurações de usuário individuais. Para definir as configurações do serviço de autenticação multifator do Azure, consulte [definir as configurações da autenticação multifator do Azure](howto-mfa-mfasettings.md)

Se os usuários precisarem de ajuda, consulte o [Guia do usuário para autenticação multifator do Azure](../user-help/multi-factor-authentication-end-user.md).
