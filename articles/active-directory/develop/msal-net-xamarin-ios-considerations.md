---
title: Considerações do IOS Xamarin (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para usar o IOS Xamarin com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262705"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerações para usar xamarin iOS com MSAL.NET
Quando você usa a Biblioteca de Autenticação microsoft para .NET (MSAL.NET) no IOS Xamarin, você deve: 

- Anular e implementar `OpenUrl` a `AppDelegate`função em .
- Habilite grupos de chaveiros.
- Habilite o compartilhamento de cache de token.
- Habilite o acesso ao chaveiro.
- Entenda os problemas conhecidos com o iOS 12 e autenticação.

## <a name="implement-openurl"></a>Implementar OpenUrl

Anular o `OpenUrl` método `FormsApplicationDelegate` da classe derivada e chamar `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Aqui está um exemplo:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Também faça as seguintes tarefas: 
* Defina um esquema de URL.
* Exija permissões para que seu aplicativo ligue para outro aplicativo.
* Tenha um formulário específico para a URL de redirecionamento.
* Registre a URL de redirecionamento no [portal Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Habilite o acesso a chaveiros

Para habilitar o acesso ao chaveiro, certifique-se de que seu aplicativo tenha um grupo de acesso a chaveiros. Você pode definir o grupo de acesso ao `WithIosKeychainSecurityGroup()` chaveiro quando criar seu aplicativo usando a API.

Para se beneficiar do cache e do soder (SSO), defina o grupo de acesso ao chaveiro com o mesmo valor em todos os seus aplicativos.

Este exemplo da configuração usa MSAL 4.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Também habilite o `Entitlements.plist` acesso ao chaveiro no arquivo. Use o seguinte grupo de acesso ou seu próprio grupo de acesso.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando você `WithIosKeychainSecurityGroup()` usa a API, o MSAL anexa automaticamente seu grupo de segurança`AppIdentifierPrefix`ao final do *ID* da equipe do aplicativo ( ). A MSAL adiciona seu grupo de segurança porque quando você constrói seu aplicativo no Xcode, ele fará o mesmo. É por isso que os `Entitlements.plist` direitos no `$(AppIdentifierPrefix)` arquivo precisam ser insionados antes do grupo de acesso ao chaveiro.

Para obter mais informações, consulte a [documentação de direitos](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)do iOS . 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Habilite o compartilhamento de cache de token em aplicativos iOS

A partir do MSAL 2.x, você pode especificar um grupo de acesso a chaveiros para persistir o cache de token em vários aplicativos. Essa configuração permite compartilhar o cache de token entre vários aplicativos que possuem o mesmo grupo de acesso a chaveiros. Você pode compartilhar o dinheiro do token entre [ADAL.NET](https://aka.ms/adal-net) aplicativos, MSAL.NET aplicativos Xamarin.iOS e aplicativos nativos para iOS que foram desenvolvidos em [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Ao compartilhar o cache de token, você permite o sso de logon único entre todos os aplicativos que usam o mesmo grupo de acesso a chaveiros.

Para habilitar esse compartilhamento `WithIosKeychainSecurityGroup()` de cache, use o método para definir o grupo de acesso ao chaveiro com o mesmo valor em todos os aplicativos que compartilham o mesmo cache. O primeiro exemplo de código neste artigo mostra como usar o método.

No início deste artigo, você `$(AppIdentifierPrefix)` aprendeu que `WithIosKeychainSecurityGroup()` o MSAL adiciona sempre que você usa a API. A MSAL adiciona esse elemento `AppIdentifierPrefix` porque o ID da equipe garante que apenas aplicativos feitos pelo mesmo editor possam compartilhar o acesso ao chaveiro.

> [!NOTE]
> A `KeychainSecurityGroup` propriedade está preterida.
> 
> A partir do MSAL 2.x, os `TeamId` desenvolvedores foram `KeychainSecurityGroup` forçados a incluir o prefixo quando usaram a propriedade. Mas a partir de MSAL 2.7.x, quando você usa a nova `iOSKeychainSecurityGroup` propriedade, o MSAL resolve o prefixo durante o `TeamId` tempo de execução. Quando você usar esta propriedade, `TeamId` não inclua o prefixo no valor. O prefixo não é necessário.
>
> Como `KeychainSecurityGroup` a propriedade é `iOSKeychainSecurityGroup` obsoleta, use a propriedade.

### <a name="use-microsoft-authenticator"></a>Use Microsoft Authenticator

Seu aplicativo pode usar o Microsoft Authenticator como um intermediário para habilitar:

- **SSO**: Quando você habilita o SSO, seus usuários não precisam fazer login em cada aplicativo.
- **Identificação do dispositivo**: Use a identificação do dispositivo para autenticar acessando o certificado do dispositivo. Este certificado é criado no dispositivo quando é juntado ao local de trabalho. Sua aplicação estará pronta se os administradores inquilinos habilitarem acesso condicional relacionado aos dispositivos.
- **Verificação de identificação do aplicativo**: Quando um aplicativo chama o corretor, ele passa sua URL de redirecionamento. O corretor verifica a URL de redirecionamento.

Para obter detalhes sobre como ativar um corretor, consulte [Use Microsoft Authenticator ou Microsoft Intune Company Portal em aplicativos Xamarin iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com iOS 12 e autenticação
A Microsoft lançou um [aviso de segurança](https://github.com/aspnet/AspNetCore/issues/4647) sobre uma incompatibilidade entre o iOS 12 e alguns tipos de autenticação. A incompatibilidade quebra os logins sociais, WSFed e OIDC. O aviso de segurança ajuda os desenvolvedores a entender como remover ASP.NET restrições de segurança de seus aplicativos para torná-los compatíveis com o iOS 12.  

Quando você desenvolve MSAL.NET aplicativos no IOS Xamarin, você pode ver um loop infinito quando você tenta fazer login em sites do iOS 12. Esse comportamento é semelhante a esta [questão ADAL.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329) 

Você também pode ver uma quebra na autenticação ASP.NET Core OIDC com o iOS 12 Safari. Para obter mais informações, consulte esta [edição do WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre propriedades para xamarin iOS, consulte o parágrafo [de considerações específicas](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) do iOS do arquivo de README.md da amostra a seguir:

Amostra | Plataforma | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | IOS Xamarin, Android, Universal Windows Platform (UWP) | Um simples aplicativo Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e a Azure AD através do ponto final do Azure AD 2.0. O aplicativo também mostra como usar o token resultante para acessar o Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->