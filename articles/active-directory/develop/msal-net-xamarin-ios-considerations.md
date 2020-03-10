---
title: Considerações sobre o Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as considerações para usar o Xamarin iOS com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377451"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerações sobre o uso do Xamarin iOS com MSAL.NET
Ao usar a MSAL.NET (biblioteca de autenticação da Microsoft para .NET) no Xamarin iOS, você deve: 

- Substitua e implemente a função `OpenUrl` no `AppDelegate`.
- Habilite grupos de conjunto de chaves.
- Habilitar o compartilhamento de cache de token.
- Habilite o acesso ao conjunto de chaves.
- Entenda os problemas conhecidos com o iOS 12 e a autenticação.

## <a name="implement-openurl"></a>Implementar OpenUrl

Substitua o método `OpenUrl` da classe derivada `FormsApplicationDelegate` e chame `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`. Aqui está um exemplo:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Além disso, execute as seguintes tarefas: 
* Defina um esquema de URL.
* Exija permissões para que seu aplicativo chame outro aplicativo.
* Ter um formulário específico para a URL de redirecionamento.
* Registre a URL de redirecionamento no [portal do Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Habilitar o acesso ao conjunto de chaves

Para habilitar o acesso ao conjunto de chaves, certifique-se de que seu aplicativo tenha um grupo de acesso da cadeia de chaves. Você pode definir o grupo de acesso do conjunto de chaves ao criar seu aplicativo usando a API `WithIosKeychainSecurityGroup()`.

Para se beneficiar do cache e do SSO (logon único), defina o grupo de acesso do conjunto de chaves com o mesmo valor em todos os seus aplicativos.

Este exemplo da instalação usa o MSAL 4. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Habilite também o acesso ao conjunto de chaves no arquivo de `Entitlements.plist`. Use o grupo de acesso a seguir ou seu próprio grupo de acesso.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando você usa a API `WithIosKeychainSecurityGroup()`, o MSAL anexa automaticamente o grupo de segurança ao final da *ID de equipe* do aplicativo (`AppIdentifierPrefix`). O MSAL adiciona seu grupo de segurança porque, ao compilar seu aplicativo no Xcode, ele fará o mesmo. É por isso que os direitos no arquivo de `Entitlements.plist` precisam incluir `$(AppIdentifierPrefix)` antes do grupo de acesso do conjunto de chaves.

Para obter mais informações, consulte a [documentação de direitos do IOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Habilitar o compartilhamento de cache de token em aplicativos iOS

A partir do MSAL 2. x, você pode especificar um grupo de acesso do conjunto de chaves para manter o cache do token entre vários aplicativos. Essa configuração permite que você compartilhe o cache do token entre vários aplicativos que têm o mesmo grupo de acesso do conjunto de chaves. Você pode compartilhar o sinal de dinheiro entre aplicativos [Adal.net](https://aka.ms/adal-net) , aplicativos MSAL.net Xamarin. Ios e aplicativos Ios nativos que foram desenvolvidos em [Adal. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Ao compartilhar o cache de token, você permite o SSO (logon único) entre todos os aplicativos que usam o mesmo grupo de acesso do conjunto de chaves.

Para habilitar esse compartilhamento de cache, use o método `WithIosKeychainSecurityGroup()` para definir o grupo de acesso do conjunto de chaves com o mesmo valor em todos os aplicativos que compartilham o mesmo cache. O primeiro exemplo de código neste artigo mostra como usar o método.

Anteriormente neste artigo, você aprendeu que o MSAL adiciona `$(AppIdentifierPrefix)` sempre que você usa a API `WithIosKeychainSecurityGroup()`. O MSAL adiciona esse elemento porque a ID da equipe `AppIdentifierPrefix` garante que somente os aplicativos que são feitos pelo mesmo publicador podem compartilhar o acesso ao conjunto de chaves.

> [!NOTE]
> A propriedade `KeychainSecurityGroup` é preterida.
> 
> A partir do MSAL 2. x, os desenvolvedores foram forçados a incluir o prefixo `TeamId` quando usaram a propriedade `KeychainSecurityGroup`. Mas, a partir do MSAL 2.7. x, quando você usa a nova propriedade `iOSKeychainSecurityGroup`, o MSAL resolve o prefixo de `TeamId` durante o tempo de execução. Quando você usa essa propriedade, não inclua o prefixo de `TeamId` no valor. O prefixo não é necessário.
>
> Como a propriedade `KeychainSecurityGroup` é obsoleta, use a propriedade `iOSKeychainSecurityGroup`.

### <a name="use-microsoft-authenticator"></a>Usar Microsoft Authenticator

Seu aplicativo pode usar Microsoft Authenticator como um agente para habilitar:

- **SSO**: quando você HABILITA o SSO, os usuários não precisam entrar em cada aplicativo.
- **Identificação do dispositivo**: Use a identificação do dispositivo para autenticar acessando o certificado do dispositivo. Esse certificado é criado no dispositivo quando ele é ingressado no local de trabalho. Seu aplicativo estará pronto se os administradores de locatários habilitarem o acesso condicional relacionado aos dispositivos.
- **Verificação da identificação do aplicativo**: quando um aplicativo chama o agente, ele passa sua URL de redirecionamento. O agente verifica a URL de redirecionamento.

Para obter detalhes sobre como habilitar um agente, consulte [usar Microsoft Authenticator ou Microsoft Intune portal da empresa em aplicativos Xamarin Ios e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com o iOS 12 e a autenticação
A Microsoft lançou um [comunicado de segurança](https://github.com/aspnet/AspNetCore/issues/4647) sobre uma incompatibilidade entre o IOS 12 e alguns tipos de autenticação. A incompatibilidade interrompe as entradas sociais, WSFed e OIDC. A consultoria de segurança ajuda os desenvolvedores a entender como remover restrições de segurança ASP.NET de seus aplicativos para torná-las compatíveis com o iOS 12.  

Ao desenvolver aplicativos MSAL.NET no Xamarin iOS, você poderá ver um loop infinito ao tentar entrar em sites do iOS 12. Esse comportamento é semelhante a esse [problema de Adal](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Você também pode ver uma interrupção em ASP.NET Core autenticação OIDC com o iOS 12 Safari. Para obter mais informações, consulte este [problema de WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para obter informações sobre as propriedades do Xamarin iOS, consulte o parágrafo [Considerações específicas do IOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) do arquivo readme.MD do exemplo a seguir:

Amostra | Platform | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Plataforma Universal do Windows (UWP) | Um aplicativo Xamarin Forms simples que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e o Azure AD por meio do ponto de extremidade 2,0 do Azure AD. O aplicativo também mostra como usar o token resultante para acessar Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->