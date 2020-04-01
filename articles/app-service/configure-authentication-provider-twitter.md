---
title: Configurar a autenticação do Twitter
description: Saiba como configurar a autenticação do Twitter como provedor de identidade para o aplicativo App Service ou Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 3f85f30e0a64b6e39b905fc05503a445aa5876ba
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437990"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Configure seu aplicativo App Service ou Azure Functions para usar o login do Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar o Azure App Service ou funções do Azure para usar o Twitter como provedor de autenticação.

Para concluir o procedimento neste artigo, você precisa de uma conta no Twitter que tenha um endereço de e-mail e número de telefone verificados. Para criar uma nova conta do Twitter, vá para [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registre seu aplicativo com o Twitter

1. Faça login no [portal Azure] e acesse sua inscrição. Copie a **URL**. Você vai usá-lo para configurar seu aplicativo do Twitter.
1. Acesse o site do [Twitter Developers,] faça login com as credenciais da sua conta do Twitter e selecione **Criar um aplicativo**.
1. Digite o **nome do aplicativo** e a descrição do **aplicativo** para o seu novo aplicativo. Cole a **URL** do aplicativo no campo URL do **site.** Na seção **URLs de retorno** de chamada, digite a `/.auth/login/twitter/callback`URL HTTPS do aplicativo App Service e apêndice o caminho . Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Na parte inferior da página, digite pelo menos 100 caracteres em **Diga-nos como este aplicativo será usado,** em seguida, selecione **Criar**. Clique em **Criar** novamente no pop-up. Os detalhes do aplicativo são exibidos.
1. Selecione a guia **Chaves e Tokens de Acesso** .

   Anote esses valores:
   - Chave de API
   - Chave secreta da API

   > [!NOTE]
   > A chave secreta da API é uma importante credencial de segurança. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo

1. Vá para sua aplicação no [portal Azure].
1. Selecione **Configurações** > **Autenticação / Autorização**e certifique-se de que a **autenticação do serviço** do aplicativo está em **ação**.
1. Selecione **Twitter**.
1. Cole os `API key` valores `API secret key` que você obteve anteriormente.
1. Selecione **OK**.

   ![Captura de tela das configurações do Twitter do aplicativo móvel][1]

   Por padrão, o App Service fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e APIs. Você deve autorizar os usuários no código do aplicativo.

1. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Twitter, defina **Ação a ser executada quando a solicitação não for autenticada** como **Twitter**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para o Twitter para autenticação.

   > [!CAUTION]
   > Restringir o acesso desta forma se aplica a todas as chamadas para o seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial disponível publicamente, como em muitos aplicativos de página única. Para tais aplicativos, **permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
