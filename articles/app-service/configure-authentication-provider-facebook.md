---
title: Configurar a autenticação do Facebook
description: Saiba como configurar a autenticação do Facebook como provedor de identidade para o aplicativo App Service ou Azure Functions.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438012"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configure seu aplicativo App Service ou Azure Functions para usar o login do Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar o Azure App Service ou funções do Azure para usar o Facebook como provedor de autenticação.

Para concluir o procedimento neste artigo, você precisa de uma conta no Facebook que tenha um endereço de e-mail verificado e um número de telefone celular. Para criar uma nova conta do Facebook, vá para [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrar seu aplicativo com o Facebook

1. Acesse o site do [Facebook Developers] e faça login com as credenciais da sua conta do Facebook.

   Se você não tiver uma conta no Facebook para Desenvolvedores, **selecione 'Iniciar'e** siga as etapas de registro.
1. Selecione **meus aplicativos** > **adicionar novo aplicativo**.
1. No campo **Nome de exibição:**
   1. Digite um nome exclusivo para o seu aplicativo.
   1. Forneça seu **e-mail de contato**.
   1. Selecione **Criar ID de aplicativo**.
   1. Complete a verificação de segurança.

   O painel de desenvolvedores do seu novo aplicativo do Facebook é aberto.
1. Selecione **O login** > **Facebook Login** > do Facebook do Painel**De si mesmo** > **configuração da Web**.
1. Na navegação à esquerda em **Login do Facebook,** selecione **Configurações**.
1. No campo **Valid OAuth redirecionar URIs,** digite `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Lembre-se `<app-name>` de substituir pelo nome do seu aplicativo Azure App Service.
1. Selecione ** Salvar alterações **.
1. No painel esquerdo, selecione **Configurações** > **Básicas**. 
1. No campo **App Secret,** selecione **Mostrar**. Copie os valores de **App ID** e **App Secret**. Você os usa mais tarde para configurar seu aplicativo App Service no Azure.

   > [!IMPORTANT]
   > O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe essa senha com ninguém nem distribua-a em um aplicativo cliente.
   >

1. A conta do Facebook que você usou para registrar o aplicativo é uma administradora do aplicativo. Neste ponto, apenas os administradores podem fazer login neste aplicativo.

   Para autenticar outras contas do Facebook, selecione **O App Review** e habilite o Make ** \<your app-name> público** para permitir que o público em geral acesse o aplicativo usando a autenticação do Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Adicionar informações do Facebook ao seu aplicativo

1. Faça login no [portal Azure] e navegue até o aplicativo App Service.
1. Selecione **Configurações** > **Autenticação / Autorização**e certifique-se de que a **autenticação do serviço** do aplicativo está em **ação**.
1. Selecione **facebook**e cole nos valores do App ID e App Secret que você obteve anteriormente. Habilite todos os escopos necessários pelo seu aplicativo.
1. Selecione **OK**.

   ![Captura de tela das configurações do Facebook do aplicativo móvel][0]

    Por padrão, o App Service fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e APIs. Você precisa autorizar os usuários em seu código de aplicativo.
1. (Opcional) Para restringir o acesso apenas aos usuários autenticados pelo Facebook, defina **ação a ser tomada quando a solicitação não for autenticada** no **Facebook**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para o Facebook para autenticação.

   > [!CAUTION]
   > Restringir o acesso desta forma se aplica a todas as chamadas para o seu aplicativo, o que pode não ser desejável para aplicativos que têm uma página inicial disponível publicamente, como em muitos aplicativos de página única. Para tais aplicativos, **permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [Fluxo de Autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar o Facebook para autenticação em seu aplicativo.

## <a name="next-steps"></a><a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal do Azure]: https://portal.azure.com/
