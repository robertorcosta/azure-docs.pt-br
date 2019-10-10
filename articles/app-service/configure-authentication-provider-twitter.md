---
title: Configurar a autenticação do Twitter – Serviço de Aplicativo do Azure
description: Saiba como configurar a autenticação do Twitter para seu aplicativo do serviço de aplicativo.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 02008b7dc1609a5f28ac6ba2a582933a96428198
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176951"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Configurar seu aplicativo do serviço de aplicativo para usar o logon do Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar Azure App serviço para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento neste artigo, você precisa de uma conta do Twitter que tenha um endereço de email e número de telefone verificados. Para criar uma nova conta do Twitter, vá para [twitter.com].

## <a name="register"> </a>Registre seu aplicativo com o Twitter

1. Entre no [portal do Azure] e vá para seu aplicativo. Copie a **URL**. Você o usará para configurar seu aplicativo do Twitter.
1. Vá para o site de [Desenvolvedores do Twitter,] , entre com suas credenciais de conta do Twitter e selecione **criar novo aplicativo**.
1. Insira um **nome** e uma **Descrição** para o novo aplicativo. Cole a **URL** do aplicativo no campo **site** . No campo **URL de retorno de chamada** , insira a URL do seu aplicativo do serviço de aplicativo e acrescente o caminho `/.auth/login/aad/callback`. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de usar o esquema HTTPS.
1. Na parte inferior da página, leia e aceite os termos. Selecione **criar seu aplicativo do Twitter**. Os detalhes do aplicativo são exibidos.
1. Selecione a guia **configurações** , marque **permitir que este aplicativo seja usado para entrar com o Twitter**e, em seguida, selecione **Atualizar configurações**.
1. Selecione a guia **Chaves e Tokens de Acesso** .

   Anote esses valores:
   - Chave do consumidor (chave de API)
   - Segredo do consumidor (segredo da API)

   > [!NOTE]
   > O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

## <a name="secrets"> </a>Adicione informações do Twitter ao seu aplicativo

1. Vá para seu aplicativo no [portal do Azure].
1. Selecione **configurações** > **autenticação/autorização**e verifique se a **autenticação do serviço de aplicativo** está **ativada**.
1. Selecione **Twitter**.
1. Cole os valores `API Key` e `API Secret` que você obteve anteriormente.
1. Selecione **OK**.

   ![Captura de tela das configurações do Twitter do aplicativo móvel][1]

   Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.

1. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Twitter, defina **Ação a ser executada quando a solicitação não for autenticada** como **Twitter**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para o Twitter para autenticação.

   > [!CAUTION]
   > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Clique em **Salvar**.

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Próximas etapas

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter,]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
