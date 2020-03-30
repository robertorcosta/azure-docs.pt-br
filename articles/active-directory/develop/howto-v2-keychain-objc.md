---
title: Configurar conjunto de chaves
titleSuffix: Microsoft identity platform
description: Aprenda a configurar chaveiro para que seu aplicativo possa armazenar tokens no chaveiro.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085855"
---
# <a name="configure-keychain"></a>Configurar conjunto de chaves

Quando a [Biblioteca de Autenticação da Microsoft para iOS e macOS](msal-overview.md) (MSAL) assina em um usuário ou atualiza um token, ele tenta armazenar tokens no chaveiro. O cache de tokens no chaveiro permite que a MSAL forneça sso de assinatura única silenciosa entre vários aplicativos que são distribuídos pelo mesmo desenvolvedor da Apple. O SSO é alcançado através da funcionalidade de grupos de acesso ao chaveiro. Para obter mais informações, consulte a documentação de itens do [chaveiro](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)da Apple .

Este artigo abrange como configurar os direitos do aplicativo para que o MSAL possa escrever tokens armazenados em cache para o chaveiro iOS e macOS.

## <a name="default-keychain-access-group"></a>Grupo de acesso de chaveiro padrão

### <a name="ios"></a>iOS

O MSAL no `com.microsoft.adalcache` iOS usa o grupo de acesso por padrão. Este é o grupo de acesso compartilhado usado por SDKs Da MSAL e Azure AD Authentication Library (ADAL) e garante a melhor experiência de logon único (SSO) entre vários aplicativos do mesmo editor.

No iOS, `com.microsoft.adalcache` adicione o grupo de chaveiros ao direito do seu aplicativo no XCode em **Configurações** > do Projeto**Recursos de compartilhamento** > **de chaveiros**

### <a name="macos"></a>macOS

O MSAL no `com.microsoft.identity.universalstorage` macOS usa o grupo de acesso por padrão.

Devido às limitações do chaveiro `access group` macOS, o MSAL's não se traduz diretamente no atributo do grupo de acesso ao chaveiro (ver [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)no macOS 10.14 e anterior. No entanto, ele se comporta de forma semelhante a partir de uma perspectiva SSO, garantindo que vários aplicativos distribuídos pelo mesmo desenvolvedor da Apple possam ter SSO silencioso.

No macOS 10.15 em diante (macOS Catalina), o MSAL usa o atributo do grupo de acesso de chaveiro para alcançar o SSO silencioso, da mesma forma que o iOS.

## <a name="custom-keychain-access-group"></a>Grupo de acesso a chaveiros personalizados

Se você quiser usar um grupo de acesso diferente de chaveiro, você pode passar seu grupo personalizado ao criar `MSALPublicClientApplicationConfig` antes de criar `MSALPublicClientApplication`, como este:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Desativar o compartilhamento de chaveiros

Se você não quiser compartilhar o estado de SSO entre vários aplicativos ou usar qualquer grupo de acesso a chaveiros, desabilite o compartilhamento de chaveiros passando o ID do pacote de aplicativos como seu chaveiroGrupo:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Erro do handle -34018 (falha ao definir o item no chaveiro)

Erro -34018 normalmente significa que o chaveiro não foi configurado corretamente. Certifique-se de que o grupo de acesso do chaveiro configurado no MSAL corresponda ao configurado em direitos.

## <a name="ensure-your-application-is-properly-signed"></a>Verifique se seu aplicativo está assinado corretamente

No macOS, os aplicativos podem ser executados sem serem assinados pelo desenvolvedor. Embora a maior parte da funcionalidade do MSAL continue funcionando, o SSO através do acesso ao chaveiro requer que o aplicativo seja assinado. Se você estiver experimentando várias solicitações de chaveiro, certifique-se de que a assinatura do aplicativo seja válida.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre grupos de acesso a chaveiros no [artigo Sharing Access to Keychain Items](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) da Apple entre um artigo sobre a coleção de aplicativos.
