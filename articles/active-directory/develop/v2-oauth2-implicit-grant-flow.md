---
title: Secure single-page applications using the Microsoft identity platform implicit flow | Azure
description: Building web applications using Microsoft identity platform implementation of the implicit flow for single-page apps.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1775106d7f8de9f6bbc2d9a36114e5bfda2625cb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207618"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft identity platform and Implicit grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

With the Microsoft identity platform endpoint, you can sign users into your single-page apps with both personal and work or school accounts from Microsoft. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não são compatíveis com solicitações CORS.
* Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

For these applications (AngularJS, Ember.js, React.js, and so on), Microsoft identity platform supports the OAuth 2.0 Implicit Grant flow. O fluxo está descrito na [Especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Its primary benefit is that it allows the app to get tokens from Microsoft identity platform without performing a backend server credential exchange. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente. Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](https://tools.ietf.org/html/rfc6749#section-10.3).

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [!NOTE]
> Not all Azure Active Directory (Azure AD) scenarios and features are supported by the Microsoft identity platform endpoint. To determine if you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra a aparência de todo fluxo de entrada implícita e as seções a seguir descrevem cada etapa em mais detalhes.

![Diagram showing the implicit sign-in flow](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

To initially sign the user into your app, you can send an [OpenID Connect](v2-protocols-oidc.md) authentication request and get an `id_token` from the Microsoft identity platform endpoint.

> [!IMPORTANT]
> To successfully request an ID token and/or an access token, the app registration in the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page must have the corresponding implicit grant flow enabled, by selecting **ID tokens** and.or **access tokens** under the **Implicit grant** section. If it's not enabled, an `unsupported_response` error will be returned: **The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> To test signing in using the implicit flow, click <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> After signing in, your browser should be redirected to `https://localhost/myapp/` with an `id_token` in the address bar.
>

| . |  | Descrição |
| --- | --- | --- |
| `tenant` | obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obrigatório | The Application (client) ID that the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. |
| `response_type` | obrigatório |Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização. If you use the `token` response_type, the `scope` parameter must contain a scope indicating which resource to issue the token for (for example, user.read on Microsoft Graph).  |
| `redirect_uri` | recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| `scope` | obrigatório |Uma lista de [escopos](v2-permissions-and-consent.md) separados por espaços. For OpenID Connect (id_tokens), it must include the scope `openid`, which translates to the "Sign you in" permission in the consent UI. Optionally you may also want to include the `email` and `profile` scopes for gaining access to additional user data. You may also include other scopes in this request for requesting consent to various resources, if an access token is requested. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Defaults to query for just an access token, but fragment if the request includes an id_token. |
| `state` | recomendável |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `nonce` | obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Necessário somente quando um id_token é solicitado. |
| `prompt` | opcional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'select_account' e 'consent'. `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único. `prompt=none` is the opposite - it will ensure that the user isn't presented with any interactive prompt whatsoever. If the request can't be completed silently via single-sign on, the Microsoft identity platform endpoint will return an error. `prompt=select_account` envia o usuário para um seletor de conta em que todas as contas lembradas na sessão serão exibidas. `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| `login_hint`  |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`.|
| `domain_hint` | opcional |If included, it will skip the email-based discovery process that user goes through on the sign in page, leading to a slightly more streamlined user experience. This is commonly used for Line of Business apps that operate in a single tenant, where they will provide a domain name within a given tenant.  This will forward the user to the federation provider for that tenant.  Note that this will prevent guests from signing into this application.  |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. The Microsoft identity platform endpoint will also ensure that the user has consented to the permissions indicated in the `scope` query parameter. Se o usuário não consentir a **nenhuma** dessas permissões, ele será solicitado a consentir às permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos multilocatários](v2-permissions-and-consent.md).

Once the user authenticates and grants consent, the Microsoft identity platform endpoint will return a response to your app at the indicated `redirect_uri`, using the method specified in the `response_mode` parameter.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é semelhante ao seguinte (com quebras de linha para legibilidade):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| . | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso que o aplicativo solicitou. The access token shouldn't be decoded or otherwise inspected, it should be treated as an opaque string. |
| `token_type` |Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` incluir `token`. Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` |Incluído se `response_type` incluir `token`. Indica os escopos para os quais access_token será válido. May not include all of the scopes requested, if they were not applicable to the user (in the case of Azure AD-only scopes being requested when a personal account is used to log in). |
| `id_token` | Um JWT (Token Web JSON) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. The  app can cache the values and display them, but it shouldn't rely on them for any authorization or security boundaries. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| . | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Getting access tokens silently in the background

Now that you've signed the user into your single-page app, you can silently get access tokens for calling web APIs secured by Microsoft identity platform, such as the [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

In the normal OpenID Connect/OAuth flow, you would do this by making a request to the Microsoft identity platform `/token` endpoint. However, the Microsoft identity platform endpoint does not support CORS requests, so making AJAX calls to get and refresh tokens is out of the question. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Para obter detalhes sobre os parâmetros de consulta na URL, consulte [enviar a solicitação de entrada](#send-the-sign-in-request).

> [!TIP]
> Tente copiar e colar a solicitação abaixo em uma guia do navegador! (Não se esqueça de substituir os valores `login_hint` pelos valores corretos para o seu usuário)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Graças ao parâmetro `prompt=none` , essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo. Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| . | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. The access token shouldn't be decoded or otherwise inspected, it should be treated as an opaque string. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` | Indica os escopos para os quais access_token será válido. May not include all of the scopes requested, if they were not applicable to the user (in the case of Azure AD-only scopes being requested when a personal account is used to log in). |
| `id_token` | Um JWT (Token Web JSON) assinado. Incluído se `response_type` incluir `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. The  app can cache the values and display them, but it shouldn't rely on them for any authorization or security boundaries. Para obter mais informações sobre id_tokens, veja a [referência `id_token`](id-tokens.md). <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente. No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| . | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token. Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## <a name="refreshing-tokens"></a>Atualizando tokens

A concessão implícita não fornece tokens de atualização. `id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. To refresh either type of token, you can perform the same hidden iframe request from above using the `prompt=none` parameter to control the identity platform's behavior. If you want to receive a new `id_token`, be sure to use `id_token` in the `response_type` and `scope=openid`, as well as a `nonce` parameter.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de desconexão

The OpenID Connect `end_session_endpoint` allows your app to send a request to the Microsoft identity platform endpoint to end a user's session and clear cookies set by the Microsoft identity platform endpoint. Para desconectar por completo um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente, limpando o cache de token ou removendo cookies) e, depois, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| . |  | Descrição |
| --- | --- | --- |
| `tenant` |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recomendável | A URL para a qual o usuário deve retornar após a conclusão do logoff. Esse valor deve corresponder a um dos URIs de redirecionamento registrados no aplicativo. If not included, the user will be shown a generic message by the Microsoft identity platform endpoint. |

## <a name="next-steps"></a>Próximos passos

* Percorra os [exemplos de MSAL JS](sample-v2-code.md) para começar a codificação.
