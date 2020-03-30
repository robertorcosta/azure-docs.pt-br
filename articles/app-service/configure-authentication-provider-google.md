---
title: Configurar a autenticação do Google
description: Saiba como configurar a autenticação do Google como provedor de identidade para o seu aplicativo App Service.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74670821"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Configure seu aplicativo App Service para usar o login do Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o Serviço de Aplicativo do Azure para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registre seu aplicativo com o Google

1. Siga a documentação do Google no [Google Sign-In para aplicativos do lado do servidor](https://developers.google.com/identity/sign-in/web/server-side-flow) para criar um ID do cliente e um segredo do cliente. Não há necessidade de fazer nenhuma mudança de código. Basta usar as seguintes informações:
    - Para **JavaScript Origins autorizado,** use `https://<app-name>.azurewebsites.net` com o nome do seu aplicativo no * \<nome do aplicativo>*.
    - Para **uri de redirecionamento autorizado,** use `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Copie o ID do aplicativo e os valores secretos do App.

    > [!IMPORTANT]
    > O segredo do App é uma importante credencial de segurança. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Adicionar informações do Google ao seu aplicativo

1. No [portal Azure,]acesse o aplicativo App Service.
1. Selecione **Configurações** > **Autenticação / Autorização**e certifique-se de que a **autenticação do serviço** do aplicativo está em **ação**.
1. Selecione **o Google,** cole os valores do App ID e app Secret que você obteve anteriormente. Habilite todos os escopos necessários pelo seu aplicativo.
1. Selecione **OK**.

   O App Service fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e às APIs. Para obter mais informações, consulte [Autorizar ou negar usuários](app-service-authentication-how-to.md#authorize-or-deny-users).

1. (Opcional) Para restringir o acesso ao site apenas aos usuários autenticados pelo Google, defina **action to take when request is not authenticated** to **Google**. Quando você define essa funcionalidade, seu aplicativo exige que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas ao Google para autenticação.

    > [!CAUTION]
    > Restringir o acesso desta forma se aplica a todas as chamadas para o seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial disponível publicamente, como em muitos aplicativos de página única. Para tais aplicativos, **permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow).

1. Selecione **Salvar**.

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal Azure]: https://portal.azure.com/

