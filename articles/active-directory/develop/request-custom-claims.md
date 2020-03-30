---
title: Solicitar reclamações personalizadas (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como solicitar reclamações personalizadas.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085595"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>Como: Solicitar reclamações personalizadas usando MSAL para iOS e macOS

O OpenID Connect permite solicitar opcionalmente o retorno de reclamações individuais do Ponto Final do UserInfo e/ou no ID Token. Uma solicitação de sinistro é representada como um objeto JSON que contém uma lista de reclamações solicitadas. Consulte [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) para obter mais detalhes.

A Microsoft Authentication Library (MSAL) para iOS e macOS permite solicitar reivindicações específicas em cenários de aquisição de tokens interativos e silenciosos. Ele faz isso `claimsRequest` através do parâmetro.

Há vários cenários em que isso é necessário. Por exemplo: 

- Solicitando reclamações fora do padrão definido para sua aplicação.
- Solicitando combinações específicas das reivindicações padrão que não podem ser especificadas usando escopos para sua aplicação. Por exemplo, se um token de acesso for rejeitado por causa de reclamações perdidas, o aplicativo pode solicitar as reclamações perdidas usando o MSAL.

> [!NOTE]
> O MSAL ignora o cache do token de acesso sempre que uma solicitação de reclamações é especificada. É importante apenas fornecer `claimsRequest` parâmetro quando forem necessárias reivindicações `claimsRequest` adicionais (em vez de sempre fornecer o mesmo parâmetro em cada chamada de API do MSAL).

`claimsRequest`pode ser especificado em `MSALSilentTokenParameters` e: `MSALInteractiveTokenParameters`

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`pode ser construído a partir de uma representação NSString de solicitação de Reclamações JSON. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Ele também pode ser modificado solicitando reivindicações específicas adicionais:

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`devem então ser definidos nos parâmetros do token e fornecidos a uma das APIs de aquisições de tokens da MSAL:

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
