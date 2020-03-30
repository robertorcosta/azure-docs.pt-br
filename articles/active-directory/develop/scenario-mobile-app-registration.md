---
title: Registre aplicativos móveis que chamam APIs web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chame APIs web (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132431"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registre aplicativos móveis que chamam APIs web

Este artigo contém instruções para ajudá-lo a registrar um aplicativo móvel que você está criando.

## <a name="supported-account-types"></a>Tipos de conta com suporte

Os tipos de conta que seus aplicativos móveis suportam dependem da experiência que você deseja habilitar e dos fluxos que você deseja usar.

### <a name="audience-for-interactive-token-acquisition"></a>Audiência para aquisição interativa de tokens

A maioria dos aplicativos móveis usa autenticação interativa. Se o seu aplicativo usar essa forma de autenticação, você pode fazer login usuários de qualquer tipo de [conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Audiência para autenticação integrada do Windows, senha de nome de usuário e B2C

Se você tiver um aplicativo Universal Windows Platform (UWP), você pode usar a autenticação integrada do Windows para fazer login nos usuários. Para usar autenticação integrada do Windows ou autenticação de senha de nome de usuário, seu aplicativo precisa fazer login nos usuários em seu próprio inquilino de desenvolvedor de linha de negócios (LOB). Em um cenário isv (software independente), seu aplicativo pode fazer login com usuários em organizações do Azure Active Directory. Esses fluxos de autenticação não são suportados para contas pessoais da Microsoft.

Você também pode fazer login com usuários usando identidades sociais que passam por uma autoridade e política B2C. Para usar este método, você pode usar apenas autenticação interativa e autenticação de senha de nome de usuário. A autenticação de senha de nome de usuário é suportada atualmente apenas em Xamarin.iOS, Xamarin.Android e UWP.

Para obter mais informações, consulte [Cenários e fluxos de autenticação suportados](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e [cenários e plataformas e idiomas suportados.](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Configuração da plataforma e redirecionamento de URIs  

### <a name="interactive-authentication"></a>Autenticação interativa

Quando você constrói um aplicativo móvel que usa autenticação interativa, a etapa de registro mais crítica é o URI de redirecionamento. Você pode definir autenticação interativa através da configuração da [plataforma na lâmina **autenticação** ](https://aka.ms/MobileAppReg).

Essa experiência permitirá que seu aplicativo obtenha o sodeirúnico (SSO) através do Microsoft Authenticator (e do Portal da Empresa Intune no Android). Também suportará políticas de gerenciamento de dispositivos.

O portal de registro de aplicativos oferece uma experiência de pré-visualização para ajudá-lo a calcular o URI de resposta intermediado para aplicativos iOS e Android:

1. No portal de registro do aplicativo, selecione **Autenticação** > **Experimente a nova experiência.**

   ![A lâmina de autenticação, onde você escolhe uma nova experiência](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecione **Adicionar uma plataforma**.

   ![Adicionar uma plataforma](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando a lista de plataformas for suportada, selecione **o iOS**.

   ![Escolha um aplicativo móvel](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Digite seu ID de pacote e, em seguida, selecione **Registrar**.

   ![Digite seu ID de pacote](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando você completa as etapas, o URI de redirecionamento é computado para você, como na imagem a seguir.

![O URI de redirecionamento resultante](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se você preferir configurar manualmente o URI de redirecionamento, você pode fazê-lo através do manifesto do aplicativo. Aqui está o formato recomendado para o manifesto:

- **iOS**:`msauth.<BUNDLE_ID>://auth` 
  - Por exemplo, insira: `msauth.com.yourcompany.appName://auth`
- **Android**:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Você pode gerar o hash de assinatura do Android usando a tecla de liberação ou depuração através do comando KeyTool.

### <a name="username-password-authentication"></a>Autenticação de senha de nome de usuário

Se o aplicativo usa apenas autenticação de senha de nome de usuário, você não precisa registrar um URI redirecionado para o seu aplicativo. Esse fluxo faz uma ida e volta para a plataforma de identidade da Microsoft versão 2.0 endpoint. Sua aplicação não será chamada de volta em nenhum URI específico. 

No entanto, você precisa identificar sua aplicação como um aplicativo de cliente público. Para isso, inicie na seção **Autenticação** do seu aplicativo. Na subseção **Configurações Avançadas,** no parágrafo padrão do **tipo cliente,** para o aplicativo tratar a pergunta **como cliente público,** selecione **Sim**.

## <a name="api-permissions"></a>Permissões de API

Os aplicativos móveis chamam APIs em nome do usuário inscrito. Seu aplicativo precisa solicitar permissões delegadas. Essas permissões também são chamadas de escopos. Dependendo da experiência que você deseja, você pode solicitar permissões delegadas estáticamente através do portal Azure. Ou você pode solicitá-los dinamicamente em tempo de execução. 

Ao registrar as permissões estáticas, você permite que os administradores aprovem facilmente seu aplicativo. Recomenda-se o registro estático.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração do código](scenario-mobile-app-configuration.md)
