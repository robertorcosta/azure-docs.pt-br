---
title: SSO entre aplicativos ADAL & MSAL (iOS/macOS) - plataforma de identidade da Microsoft | Azure
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880743"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Como: SSO entre aplicativos ADAL e MSAL no macOS e iOS

A Microsoft Authentication Library (MSAL) para iOS pode compartilhar o estado SSO com [o ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) entre aplicativos. Você pode migrar seus aplicativos para o MSAL no seu próprio ritmo, garantindo que seus usuários ainda se beneficiem do SSO entre aplicativos — mesmo com uma mistura de aplicativos baseados em ADAL e MSAL.

Se você está procurando orientação para configurar o SSO entre aplicativos usando o MSAL SDK, consulte [Silent SSO entre vários aplicativos](single-sign-on-macos-ios.md#silent-sso-between-apps). Este artigo enfoca o SSO entre a ADAL e a MSAL.

As especificidades que implementam o SSO dependem da versão ADAL que você está usando.

## <a name="adal-27x"></a>ADAL 2.7.x

Esta seção abrange as diferenças de SSO entre MSAL e ADAL 2.7.x

### <a name="cache-format"></a>Formato cache

O ADAL 2.7.x pode ler o formato de cache MSAL. Você não precisa fazer nada especial para o SSO cross-app com a versão ADAL 2.7.x. No entanto, você precisa estar ciente das diferenças nos identificadores de conta que essas duas bibliotecas suportam.

### <a name="account-identifier-differences"></a>Diferenças de identificador de conta

MSAL e ADAL usam diferentes identificadores de conta. O ADAL usa o UPN como seu principal identificador de conta. A MSAL usa um identificador de conta não exibido que é baseado em um ID `sub` de objeto e um ID de inquilino para contas AAD, e uma reivindicação para outros tipos de contas.

Quando você `MSALAccount` recebe um objeto no resultado MSAL, ele `identifier` contém um identificador de conta na propriedade. O aplicativo deve usar este identificador para solicitações silenciosas subseqüentes.

Além de `identifier` `MSALAccount` , objeto contém um `username`identificador exibivel chamado . Isso se `userId` traduz em ADAL. `username`não é considerado um identificador único e pode mudar a qualquer momento, por isso só deve ser usado para cenários de retrocompatibilidade com ADAL. O MSAL suporta consultas `username` de `identifier`cache usando ou, quando a consulta é `identifier` recomendada.

A tabela a seguir resume as diferenças de identificadores de conta entre ADAL e MSAL:

| Identificador de conta                | MSAL                                                         | ADAL 2.7.x      | ADAL mais antigo (antes de ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| identificador exibivel            | `username`                                                   | `userId`        | `userId`                       |
| identificador único não exibivel | `identifier`                                                 | `homeAccountId` | N/D                            |
| Nenhum id de conta conhecido               | Consultar todas as `allAccounts:` contas através de API em`MSALPublicClientApplication` | N/D             | N/D                            |

Esta é `MSALAccount` a interface que fornece esses identificadores:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO de MSAL para ADAL

Se você tiver um aplicativo MSAL e um aplicativo ADAL, e o usuário fizer o primeiro sinal no aplicativo `username` baseado `MSALAccount` em MSAL, você poderá obter `userId`SSO no aplicativo ADAL salvando o objeto e passando-o para o seu aplicativo baseado em ADAL como . O ADAL pode então encontrar as `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` informações da conta silenciosamente com a API.

### <a name="sso-from-adal-to-msal"></a>SSO de ADAL para MSAL

Se você tiver um aplicativo MSAL e um aplicativo ADAL, e o usuário fizer o primeiro sinal no aplicativo baseado em ADAL, você poderá usar identificadores de usuário ADAL para procurar conta no MSAL. Isso também se aplica ao migrar de ADAL para MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId da ADAL

ADAL 2.7.x `homeAccountId` retorna `ADUserInformation` o objeto no resultado através desta propriedade:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`em ADAL é equivalente `identifier` em MSAL. Você pode salvar este identificador para usar no MSAL para procurar conta com a `accountForIdentifier:error:` API.

#### <a name="adals-userid"></a>ADAL`userId`

Se `homeAccountId` não estiver disponível, ou você tiver apenas o identificador `userId` exibivel, você pode usar ADAL's para procurar a conta no MSAL.

No MSAL, primeiro procure `username` uma `identifier`conta por ou . Use `identifier` sempre para consultar se você tiver, `username` e use apenas como um recuo. Se a conta for encontrada, `acquireTokenSilent` use a conta nas chamadas.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



APIs de análise de conta suportadas pela MSAL:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Esta seção abrange as diferenças de SSO entre MSAL e ADAL 2.x-2.6.6.

As versões ADAL mais antigas não suportam nativamente o formato de cache MSAL. No entanto, para garantir uma migração suave do ADAL para o MSAL, o MSAL pode ler o formato de cache ADAL mais antigo sem solicitar credenciais do usuário novamente.

Como `homeAccountId` não está disponível em versões ADAL mais antigas, você precisa procurar contas usando o: `username`

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Por exemplo:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Alternativamente, você pode ler todas as contas, que também lerão as informações da conta da ADAL:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
