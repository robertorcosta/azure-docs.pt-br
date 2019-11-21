---
title: Use Microsoft identity platform to access secure resources without user interaction | Azure
description: Build web applications by using the Microsoft identity platform implementation of the OAuth 2.0 authentication protocol.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1499e931a81e31494d7ff442c8295ba03f1cf33
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207643"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft identity platform and the OAuth 2.0 client credentials flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Você pode usar a [ concessão de credenciais do cliente OAuth 2.0 ](https://tools.ietf.org/html/rfc6749#section-4.4) especificada na RFC 6749, às vezes chamada de *OAuth de duas etapas*, para acessar recursos hospedados na Web usando a identidade de um aplicativo. Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativo normalmente são referidos como *daemons* ou *contas de serviço*.

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

O fluxo de concessão de credenciais do cliente OAuth 2.0 permite que um serviço Web (cliente confidencial) use suas próprias credenciais, em vez de representar um usuário, para autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> The Microsoft identity platform endpoint doesn't support all Azure AD scenarios and features. To determine whether you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

No tipo mais comum de *OAuth com três pernas*, um aplicativo cliente tem permissão para acessar um recurso em nome de um usuário específico. A permissão é delegada do usuário para o aplicativo, geralmente durante o processo de [consentimento](v2-permissions-and-consent.md). No entanto, no fluxo de credenciais do cliente (*OAuth de dois segmentos*), as permissões são concedidas diretamente ao próprio aplicativo. Quando o aplicativo apresenta um token a um recurso, o recurso impõe que o próprio aplicativo tenha autorização para executar uma ação, e não o usuário.

## <a name="protocol-diagram"></a>Diagrama de protocolo

Todo o fluxo de credenciais do cliente é semelhante ao diagrama a seguir. Descrevemos cada uma das etapas neste artigo.

![Diagram showing the client credentials flow](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Obter autorização direta

Um aplicativo geralmente recebe autorização direta para acessar um recurso de duas maneiras:

* [Por meio de uma ACL (lista de controle de acesso) no recurso](#access-control-lists)
* [Por meio da atribuição de permissão de aplicativo no Azure AD](#application-permissions)

Esses dois métodos são os mais comuns no Azure AD e são recomendados para clientes e recursos que executam o fluxo de credenciais de cliente. Um recurso também pode optar por autorizar seus clientes de outras maneiras. Cada servidor de recursos pode escolher o método que faz mais sentido para seu aplicativo.

### <a name="access-control-lists"></a>Listas de controle de acesso

Um provedor de recursos pode impor uma verificação de autorização com base em uma lista de IDs de aplicativo (cliente) que ele conhece e concede um nível específico de acesso. When the resource receives a token from the Microsoft identity platform endpoint, it can decode the token and extract the client's application ID from the `appid` and `iss` claims. Em seguida, compara o aplicativo com uma ACL (lista de controle de acesso) que ele mantém. Granularidade e o método a ACL podem variar significativamente entre os recursos.

Um caso de uso comum é usar uma ACL para executar testes para um aplicativo Web ou para uma API Web. A API Web pode conceder apenas um subconjunto das permissões completas para um cliente específico. To run end-to-end tests on the API, create a test client that acquires tokens from the Microsoft identity platform endpoint and then sends them to the API. A API então verifica a ACL para a ID do aplicativo do cliente de teste a fim de ter acesso completo à funcionalidade total da API. Se você usar esse tipo de ACL, certifique-se de validar não apenas o valor `appid` do chamador, mas também de validar que o valor `iss` do token é confiável.

Esse tipo de autorização é comum para daemons e contas de serviço que precisam acessar dados pertencentes a usuários consumidores com contas pessoais da Microsoft. Para dados de propriedade de organizações, é recomendável que você obtenha a autorização necessária por meio de permissões de aplicativo.

### <a name="application-permissions"></a>Permissões de aplicativo

Em vez de usar ACLs, você pode usar APIs para expor um conjunto de permissões do aplicativo. Uma permissão de aplicativo é concedida a um aplicativo por um administrador de uma organização e só pode ser usada para acessar os dados pertencentes a essa organização e a seus funcionários. Por exemplo, o Microsoft Graph expõe várias permissões de aplicativo para fazer o seguinte:

* Ler emails em todas as caixas de correio
* Ler e gravar mensagens em todas as caixas de correio
* Enviar emails como qualquer usuário
* Ler dados do diretório

Para saber mais sobre permissões de aplicativo, vá para [Microsoft Graph](https://developer.microsoft.com/graph).

Para usar permissões de aplicativo no seu aplicativo, siga as etapas discutidas nas próximas seções.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar as permissões no portal de registro do aplicativo

1. Register and create an app through the new [App registrations (Preview) experience](quickstart-register-app.md).
2. Go to your application in the App registrations (Preview) experience. Navigate to the **Certificates & secrets** section, and add a **new client secret**, because you'll need at least one client secret to request a token.
3. Localize a seção **Permissões de API** e adicione as **permissões de aplicativo** que seu aplicativo requer.
4. **Salve** o registro do aplicativo.

#### <a name="recommended-sign-the-user-into-your-app"></a>Recommended: Sign the user into your app

Normalmente, quando você cria um aplicativo que usa as permissões de aplicativo, o aplicativo exige uma página ou exibição na qual o administrador aprova as permissões do aplicativo. Esta página pode ser parte do fluxo de entrada do aplicativo, parte das configurações do aplicativo ou pode ser um fluxo dedicado de "conexão". Em muitos casos, faz sentido que o aplicativo somente mostre o modo de exibição "conectar" depois que o usuário entra com uma conta corporativa ou de estudante da Microsoft.

If you sign the user into your app, you can identify the organization to which the user belongs to before you ask the user to approve the application permissions. Embora não seja estritamente necessário, isso pode ajudá-lo a criar uma experiência mais intuitiva para os usuários. To sign the user in, follow our [Microsoft identity platform protocol tutorials](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar permissões de um administrador de diretório

When you're ready to request permissions from the organization's admin, you can redirect the user to the Microsoft identity platform *admin consent endpoint*.

> [!TIP]
> Tente executar a solicitação no Postman! (Use your own app ID for best results - the tutorial application won't request useful permissions.) [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| . | Condição | Descrição |
| --- | --- | --- |
| `tenant` | obrigatórios | O locatário do diretório para o qual você deseja solicitar permissão. Pode estar no formato de nome amigável ou de GUID. Se você não souber a qual locatário o usuário pertence e se quiser deixá-lo entrar com qualquer locatário, use `common`. |
| `client_id` | obrigatórios | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app. |
| `redirect_uri` | obrigatórios | O URI de redirecionamento onde você deseja que a resposta seja enviada para ser tratada pelo aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, com exceção de que ele deve ser codificado por URL e podem ter mais segmentos de caminho. |
| `state` | Recomendado | Um valor incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |

At this point, Azure AD enforces that only a tenant administrator can sign into complete the request. O administrador deverá aprovar todas as permissões diretas do aplicativo que você solicitou para o aplicativo no portal de registro de aplicativos.

##### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para o seu aplicativo, a resposta bem-sucedida terá esta aparência:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| . | Descrição |
| --- | --- |
| `tenant` | O locatário do diretório que concedeu as permissões solicitadas, no formato GUID. |
| `state` | Um valor incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `admin_consent` | Defina como **True**. |

##### <a name="error-response"></a>Resposta de erro

Se o administrador não aprovar as permissões para o seu aplicativo, a resposta de falha terá esta aparência:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| . | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros, e que você pode usar para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar você a identificar a causa raiz de um erro. |

Depois de receber uma resposta bem-sucedida do ponto de extremidade de provisionamento do aplicativo, o aplicativo terá as permissões diretas solicitadas. Agora você pode solicitar um token para o recurso desejado.

## <a name="get-a-token"></a>Obter um token

Após adquirir a autorização necessária para seu aplicativo, continue adquirindo os tokens de acesso para as APIs. To get a token by using the client credentials grant, send a POST request to the `/token` Microsoft identity platform endpoint:

> [!TIP]
> Tente executar a solicitação no Postman! (Use your own app ID for best results - the tutorial application won't request useful permissions.) [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Na primeira ocorrência: solicitação de token de acesso com um segredo compartilhado

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant! 
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| . | Condição | Descrição |
| --- | --- | --- |
| `tenant` | obrigatórios | O aplicativo de locatário do diretório planos operar, no formato de nome de domínio ou GUID. |
| `client_id` | obrigatórios | A ID do aplicativo atribuído ao aplicativo. É possível localizar essas informações no portal onde você registrou o aplicativo. |
| `scope` | obrigatórios | O valor transmitido ao parâmetro `scope` na solicitação deve ser o identificador de recurso (URI de ID do aplicativo) do recurso que você deseja, com o sufixo `.default`. Para o exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. <br/>This value tells the Microsoft identity platform endpoint that of all the direct application permissions you have configured for your app, the endpoint should issue a token for the ones associated with the resource you want to use. Para saber mais sobre o escopo `/.default`, confira [Documentação de consentimento](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | obrigatórios | The client secret that you generated for your app in the app registration portal. O segredo do cliente deve ser codificada como URL antes de serem enviados. |
| `grant_type` | obrigatórios | Deve ser definido como `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| . | Condição | Descrição |
| --- | --- | --- |
| `tenant` | obrigatórios | O aplicativo de locatário do diretório planos operar, no formato de nome de domínio ou GUID. |
| `client_id` | obrigatórios |A ID do aplicativo (cliente) atribuída ao seu aplicativo. |
| `scope` | obrigatórios | O valor transmitido ao parâmetro `scope` na solicitação deve ser o identificador de recurso (URI de ID do aplicativo) do recurso que você deseja, com o sufixo `.default`. Para o exemplo do Microsoft Graph, o valor é `https://graph.microsoft.com/.default`. <br/>This value informs the Microsoft identity platform endpoint that of all the direct application permissions you have configured for your app, it should issue a token for the ones associated with the resource you want to use. Para saber mais sobre o escopo `/.default`, confira [Documentação de consentimento](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | obrigatórios | O valor precisa ser definido como `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | obrigatórios | Uma asserção (um token web JSON) que você precisa para criar e assinar com o certificado registrado como credenciais do seu aplicativo. Leia mais sobre [credenciais de certificado](active-directory-certificate-credentials.md) para saber como registrar seu certificado e saber sobre o formato da asserção.|
| `grant_type` | obrigatórios | Deve ser definido como `client_credentials`. |

Observe que os parâmetros são praticamente os mesmos como no caso da solicitação pelo segredo compartilhado, exceto pelo fato de o parâmetro client_secret ser substituído por dois parâmetros: um client_assertion_type e uma client_assertion.

### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida tem a seguinte aparência:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| . | Descrição |
| --- | --- |
| `access_token` | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| `token_type` | Indica o valor do tipo de token. The only type that Microsoft identity platform supports is `bearer`. |
| `expires_in` | A quantidade de tempo que um token de acesso é válido (em segundos). |

### <a name="error-response"></a>Resposta de erro

Uma resposta de erro tem esta aparência:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| . | Descrição |
| --- | --- |
| `error` | Uma cadeia de caracteres de códigos de erro que você pode usar para classificar tipos de erro que ocorrem e para responder aos erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| `timestamp` | A hora em que o erro ocorreu. |
| `trace_id` | Um identificador exclusivo da solicitação para ajudar com diagnósticos. |
| `correlation_id` | Um identificador exclusivo da solicitação para ajudar com diagnósticos entre componentes. |

## <a name="use-a-token"></a>Usar um token

Agora que você já adquiriu um token, use-o para fazer solicitações ao recurso. Quando o token expirar, repita a solicitação para o ponto de extremidade `/token` a fim de adquirir um novo token de acesso.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Exemplos de código e outras documentações

Leia a [documentação de visão geral de credenciais de cliente](https://aka.ms/msal-net-client-credentials) da biblioteca de autenticação da Microsoft

| Amostra | Plataforma |Descrição |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Console do .NET Core 2.1 | Um aplicativo .NET Core simples que exibe os usuários de um locatário consultando o Microsoft Graph usando a identidade do aplicativo, em vez de uma consulta em nome do usuário. O exemplo também ilustra a variação usando certificados para autenticação. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Um aplicativo Web que sincroniza dados do Microsoft Graph usando a identidade do aplicativo, em vez de fazer em nome do usuário. |
