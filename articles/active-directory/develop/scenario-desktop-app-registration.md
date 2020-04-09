---
title: Registre aplicativos de desktop que chamam APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de desktop que chama APIs web (registro de aplicativos)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885150"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplicativo de desktop que chama APIs web: Registro de aplicativos

Este artigo abrange as especificidades de registro do aplicativo para um aplicativo de desktop.

## <a name="supported-account-types"></a>Tipos de conta com suporte

Os tipos de conta suportados em um aplicativo de desktop dependem da experiência que você deseja iluminar. Por causa dessa relação, os tipos de conta suportadas dependem dos fluxos que você deseja usar.

### <a name="audience-for-interactive-token-acquisition"></a>Audiência para aquisição interativa de tokens

Se o aplicativo de desktop usar autenticação interativa, você pode fazer login com usuários de qualquer tipo de [conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Audiência para fluxos silenciosos de aplicativos de desktop

- Para usar a Autenticação Integrada do Windows ou um nome de usuário e uma senha, seu aplicativo precisa fazer login em usuários em seu próprio inquilino, por exemplo, se você é um desenvolvedor de linha de negócios (LOB). Ou, nas organizações do Azure Active Directory, seu aplicativo precisa fazer login com usuários em seu próprio inquilino se for um cenário ISV. Esses fluxos de autenticação não são suportados para contas pessoais da Microsoft.
- Se você quiser usar o fluxo de código do dispositivo, você não pode fazer login com usuários com suas contas pessoais da Microsoft ainda.
- Se você fizer login em usuários com identidades sociais que passam por uma autoridade e política de business-to-commerce (B2C), você só poderá usar a autenticação interativa e de senha de nome de usuário.

## <a name="redirect-uris"></a>URIs de redirecionamento

Os URIs de redirecionamento para usar em um aplicativo de desktop dependem do fluxo que você deseja usar.

- Se você usar autenticação interativa ou `https://login.microsoftonline.com/common/oauth2/nativeclient`fluxo de código do dispositivo, use . Para obter essa configuração, selecione a URL correspondente na seção **Autenticação** para o seu aplicativo.
  
  > [!IMPORTANT]
  > Hoje, MSAL.NET usa outro URI redirecionado por padrão`urn:ietf:wg:oauth:2.0:oob`em aplicativos de desktop que são executados no Windows ( ). No futuro, vamos querer mudar esse padrão, por isso `https://login.microsoftonline.com/common/oauth2/nativeclient`recomendamos que você use .

- Se você criar um aplicativo nativo Objective-C ou Swift para macOS, registre o URI redirecionado com base no identificador de pacote do aplicativo no seguinte formato: msauth.<your.app.bundle.id>://auth. Substitua <your.app.bundle.id> pelo identificador de pacote do aplicativo.
- Se o aplicativo usa apenas autenticação integrada do Windows ou um nome de usuário e uma senha, você não precisa registrar um URI redirecionado para o seu aplicativo. Esses fluxos fazem uma ida e volta para a plataforma de identidade microsoft v2.0 endpoint. Sua aplicação não será chamada de volta em nenhum URI específico.
- Para distinguir o fluxo de código do dispositivo, a autenticação integrada do Windows e um nome de usuário e uma senha de um fluxo de aplicativo cliente confidencial que também não tenha URIs redirecionados (o fluxo de credencial do cliente usado em aplicativos daemon), você precisa expressar que seu aplicativo é um aplicativo cliente público. Para obter essa configuração, vá até a seção **Autenticação** para o seu aplicativo. Na subseção **Configurações Avançadas,** no parágrafo padrão do **tipo cliente,** selecione **'Sim** para **tratar' como cliente público**.

  ![Permitir cliente público](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Permissões de API

Os aplicativos de desktop chamam APIs para o usuário inscrito. Eles precisam pedir permissões delegadas. Eles não podem solicitar permissões de solicitação, que são tratadas apenas em [aplicativos daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo para desktop: configuração do aplicativo](scenario-desktop-app-configuration.md)
