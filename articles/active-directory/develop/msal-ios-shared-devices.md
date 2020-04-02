---
title: Modo de dispositivo compartilhado para dispositivos iOS
titleSuffix: Microsoft identity platform | Azure
description: Saiba como ativar o modo dispositivo compartilhado para permitir que os Trabalhadores de Primeira Linha compartilhem um dispositivo iOS
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550247"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modo de dispositivo compartilhado para dispositivos iOS

> [!NOTE]
> Esse recurso está em uma versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Trabalhadores de primeira linha, como associados de varejo, membros da tripulação de voo e trabalhadores de serviço de campo, muitas vezes usam um dispositivo móvel compartilhado para realizar seu trabalho. Esses dispositivos compartilhados podem apresentar riscos à segurança se seus usuários compartilharem suas senhas ou PINs, intencionalmente ou não, para acessar dados de clientes e negócios no dispositivo compartilhado.

O modo de dispositivo compartilhado permite configurar um dispositivo iOS 13 ou superior para ser compartilhado com mais facilidade e segurança pelos funcionários. Os funcionários podem fazer login e acessar as informações dos clientes rapidamente. Quando terminarem seu turno ou tarefa, eles podem sair do dispositivo e ele está imediatamente pronto para uso pelo próximo funcionário.

O modo dispositivo compartilhado também fornece o gerenciamento de identidade da Microsoft do dispositivo.

Este recurso usa o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para gerenciar os usuários no dispositivo e distribuir o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Crie um aplicativo de modo de dispositivo compartilhado

Para criar um aplicativo de modo de dispositivo compartilhado, desenvolvedores e admins de dispositivos em nuvem trabalham juntos:

1. **Os desenvolvedores** de aplicativos escrevem um aplicativo de conta única (aplicativos de várias contas não são suportados no modo de dispositivo compartilhado) e escrevem código para lidar com coisas como a saída compartilhada do dispositivo.

1. **Os administradores** de dispositivos preparam o dispositivo para ser compartilhado usando um provedor de gerenciamento de dispositivos móveis (MDM) como o Microsoft Intune para gerenciar os dispositivos em sua organização. O MDM empurra o aplicativo Microsoft Authenticator para os dispositivos e ativa o "Modo Compartilhado" para cada dispositivo através de uma atualização de perfil para o dispositivo. Esta configuração de Modo Compartilhado é o que muda o comportamento dos aplicativos suportados no dispositivo. Esta configuração do provedor MDM define o modo de dispositivo compartilhado para o dispositivo e permite o [plug-in Microsoft Enterprise SSO para dispositivos Apple,](apple-sso-plugin.md) o que é necessário para o modo de dispositivo compartilhado.

1. [**Obrigatório apenas durante a visualização pública**] Um usuário com função [de administrador de dispositivos em nuvem](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) deve então iniciar o aplicativo Microsoft [Authenticator](../user-help/user-help-auth-app-overview.md) e juntar seu dispositivo à organização.

    Para configurar a adesão de suas funções organizacionais no portal Azure: **Azure Active Directory** > **Roles and Administrators** > **Cloud Device Administrator**

As seções a seguir ajudam você a atualizar seu aplicativo para suportar o modo de dispositivo compartilhado.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Use o Intune para habilitar o modo de dispositivo compartilhado & extensão SSO

> [!NOTE]
> A etapa a seguir é necessária apenas durante a visualização pública.

Seu dispositivo precisa ser configurado para suportar o modo de dispositivo compartilhado. Ele deve ter o iOS 13+ instalado e ser inscrito em MDM. A configuração de MDM também precisa habilitar o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md). Para saber mais sobre as extensões do SSO, consulte o vídeo da [Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. No Portal de Configuração intune, diga ao dispositivo para habilitar o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) com a seguinte configuração:

    - **Tipo:** Redirecionamento
    - **ID de extensão**: com.microsoft.azureauthenticator.ssoextension
    - **ID da equipe**: SGGM6D27TK
    - **URLs:**https://login.microsoftonline.com
    - Dados adicionais para configurar:
      - Chave: compartilhadoDeviceMode
      - Tipo: booliano
      - Valor: Verdadeiro

    Para obter mais informações sobre a configuração com o Intune, consulte a [documentação de configuração](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)do Intune .

1. Em seguida, configure seu MDM para empurrar o aplicativo Microsoft Authenticator para o seu dispositivo através de um perfil MDM.

    Defina as seguintes opções de configuração para ativar o modo dispositivo compartilhado:

    - Configuração 1:
      - Chave: compartilhadoDeviceMode
      - Tipo: booliano
      - Valor: Verdadeiro

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modifique seu aplicativo iOS para suportar o modo de dispositivo compartilhado

Seus usuários dependem de você para garantir que seus dados não sejam vazados para outro usuário. As seções a seguir fornecem sinais úteis para indicar ao seu aplicativo que uma alteração ocorreu e deve ser tratada.

Você é responsável por verificar o estado do usuário no dispositivo toda vez que seu aplicativo é usado e, em seguida, limpar os dados do usuário anterior. Isso inclui se ele for recarregado do plano de fundo em multitarefa.

Em uma alteração de usuário, você deve garantir que os dados do usuário anterior sejam limpos e que todos os dados armazenados em cache no aplicativo sejam removidos. Recomendamos que você e sua empresa realizem um processo de revisão de segurança após a atualização do seu aplicativo para suportar o modo de dispositivo compartilhado.

### <a name="detect-shared-device-mode"></a>Detectar o modo de dispositivo compartilhado

Detectar o modo de dispositivo compartilhado é importante para o seu aplicativo. Muitos aplicativos exigirão uma mudança na experiência do usuário (UX) quando o aplicativo é usado em um dispositivo compartilhado. Por exemplo, seu aplicativo pode ter um recurso "Cadastre-se", o que não é apropriado para um Firstline Worker porque ele provavelmente já tem uma conta. Você também pode querer adicionar segurança extra ao tratamento de dados do seu aplicativo se ele estiver no modo dispositivo compartilhado.

Use `getDeviceInformationWithParameters:completionBlock:` a API `MSALPublicClientApplication` no para determinar se um aplicativo está sendo executado em um dispositivo no modo dispositivo compartilhado.

Os seguintes trechos de código `getDeviceInformationWithParameters:completionBlock:` mostram exemplos de uso da API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obtenha o usuário de entrada e determine se um usuário mudou no dispositivo

Outra parte importante do suporte ao modo de dispositivo compartilhado é determinar o estado do usuário no dispositivo e limpar os dados do aplicativo se um usuário mudou ou se não há nenhum usuário no dispositivo. Você é responsável por garantir que os dados não sejam vazados para outro usuário.

Você pode `getCurrentAccountWithParameters:completionBlock:` usar a API para consultar a conta de entrada atualmente assinada no dispositivo.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Conectar um usuário globalmente

Quando um dispositivo é configurado como um dispositivo `acquireTokenWithParameters:completionBlock:` compartilhado, seu aplicativo pode chamar a API para fazer login na conta. A conta estará disponível globalmente para todos os aplicativos elegíveis no dispositivo após os primeiros sinais de aplicativo na conta.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Desconectar um usuário globalmente

O código a seguir remove a conta de entrada e limpa tokens armazenados em cache não apenas do aplicativo, mas também do dispositivo que está no modo dispositivo compartilhado. No entanto, não limpa os *dados* do seu aplicativo. Você deve limpar os dados do seu aplicativo, bem como limpar quaisquer dados armazenados em cache que seu aplicativo possa estar exibindo para o usuário.

#### <a name="clear-browser-state"></a>Limpar o estado do navegador

> [!NOTE]
> A etapa a seguir é necessária apenas durante a visualização pública.

Nesta versão de visualização pública, o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) limpa o estado apenas para aplicativos. Ele não limpa o estado no navegador Safari. Recomendamos que você limpe manualmente a sessão do navegador para garantir que nenhum vestígio do estado do usuário seja deixado para trás. Você pode usar `signoutFromBrowser` a propriedade opcional mostrada abaixo para limpar quaisquer cookies. Isso fará com que o navegador seja lançado brevemente no dispositivo.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Próximas etapas

Para ver o modo de dispositivo compartilhado em ação, a seguinte amostra de código no GitHub inclui um exemplo de execução de um aplicativo Firstline Worker em um dispositivo iOS no modo de dispositivo compartilhado:

[MSAL iOS Swift Microsoft Graph API Sample](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
