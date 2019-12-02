---
title: Adicionar autenticação no Android
description: Saiba como usar o serviço de Azure App para autenticar usuários de seu aplicativo Android com provedores de identidade, como Google, Facebook, Twitter e Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f68b4f8477d5b21a7107270370af387a7e88756e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668944"
---
# <a name="add-authentication-to-your-android-app"></a>Adicionar autenticação ao aplicativo do Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar os serviços de **Build**, **Teste** e **Distribuição** para configurar o pipeline de integração e entrega contínuas. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso dos respectivos aplicativos usando os serviços de **Análise** e **Diagnóstico** e se envolver com usuários usando o serviço de **Push**. Os desenvolvedores também podem aproveitar o serviço de **Autenticação** para autenticar os respectivos usuários e o serviço de **Dados** para persistir e sincronizar dados de aplicativo na nuvem.
>
> Se desejar integrar os serviços de nuvem em seu aplicativo móvel, inscreva-se no [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="summary"></a>Resumo
Neste tutorial, você adiciona autenticação ao projeto de início rápido da lista de tarefas pendentes no Android usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [Introdução aos Aplicativos Móveis] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo do Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos sempre o esquema de URL _appname_. No entanto, você pode usar o esquema de URL que quiser. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [Azure portal], selecione sua conta.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Em **URLs de Redirecionamento Externo Permitidas**, insira `appname://easyauth.callback`.  O _appname_ na cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

4. Clique em **OK**.

5. Clique em **Save** (Salvar).

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Android Studio, abra o projeto concluído com o tutorial [Introdução aos Aplicativos Móveis]. No menu **Executar**, clique em **Executar aplicativo** e verifique se uma exceção sem tratamento com um código de status 401 (Não autorizado) é acionada depois que o aplicativo é iniciado.

     Essa exceção ocorre porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualiza o aplicativo para autenticar os usuários antes de solicitar recursos do back-end dos Aplicativos Móveis.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Armazenar em cache tokens de autenticação no cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximos passos
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicionar notificações por push ao aplicativo Android](app-service-mobile-android-get-started-push.md).
  Saiba como configurar o back-end dos Aplicativos Móveis para usar os hubs de notificação do Azure para enviar notificações por push.
* [Habilitar a sincronização offline para o aplicativo móvel Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline ao aplicativo usando um back-end dos Aplicativos Móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel &mdash; exibindo, adicionando ou modificando dados &mdash; mesmo quando não há nenhuma conexão de rede.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introdução aos Aplicativos Móveis]: app-service-mobile-android-get-started.md
[Azure portal]: https://portal.azure.com/
