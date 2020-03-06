---
title: Fluxo de concessão implícita do OAuth 2,0 – plataforma de identidade da Microsoft | Azure
description: Proteger aplicativos de página única usando o fluxo implícito da plataforma de identidade da Microsoft.
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
ms.openlocfilehash: 4f35162be513873637f9a87260410d891fdace10
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300461"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de concessão implícita

Com o ponto de extremidade da plataforma Microsoft Identity, você pode conectar os usuários em seus aplicativos de página única com contas pessoais e corporativas ou de estudante da Microsoft. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não são compatíveis com solicitações CORS.
* Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para esses aplicativos (AngularJS, Ember. js, reajam. js e assim por diante), a plataforma de identidade da Microsoft dá suporte ao fluxo de concessão implícita do OAuth 2,0. O fluxo está descrito na [Especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Seu principal benefício é que ele permite que o aplicativo obtenha tokens da plataforma de identidade da Microsoft sem executar uma troca de credenciais de servidor de back-end. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente. Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](https://tools.ietf.org/html/rfc6749#section-10.3).

Este artigo descreve como programar diretamente em relação ao protocolo em seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [!NOTE]
> Nem todos os cenários e recursos do Azure Active Directory (AD do Azure) são suportados pelo ponto de extremidade da plataforma de identidade da Microsoft. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícita OAuth2

A especificação OAuth2 declara que a concessão implícita foi concebida para habilitar aplicativos de agente do usuário, ou seja, aplicativos JavaScript em execução em um navegador. A característica que define esses aplicativos é que o código JavaScript é usado para acessar recursos de servidor (normalmente, uma API Web) e para atualizar a experiência do usuário do aplicativo de maneira adequada. Pense em aplicativos como Gmail ou Outlook Web Access: quando você seleciona uma mensagem da caixa de entrada, apenas o painel de visualização da mensagem muda para exibir a nova seleção, enquanto o restante da página permanece inalterado. Essa característica difere de aplicativos Web tradicionais baseados em redirecionamento, em que cada interação do usuário resulta em um postback de página inteira e em uma renderização de página inteira da nova resposta do servidor.

Os aplicativos que levam a abordagem baseada em JavaScript ao extremo são chamados de SPAs ou aplicativos de página única. A ideia é que esses aplicativos servem apenas uma página HTML inicial e o JavaScript associado, com todas as interações posteriores sendo conduzidas por chamadas à API Web realizadas por JavaScript. No entanto, as abordagens híbridas, em que o aplicativo é principalmente orientado por postback, mas executa chamadas JS ocasionais, não são incomuns. A discussão sobre o uso de fluxo implícitos também é relevante para eles.

Aplicativos baseados em redirecionamento normalmente protegem suas solicitações por meio de cookies. No entanto, essa abordagem não funciona bem para aplicativos JavaScript. Cookies só funcionam em relação ao domínio em que foram gerados, enquanto chamadas JavaScript podem ser direcionadas para outros domínios. De fato, frequentemente esse será o caso: pense em aplicativos que invocam a API do Microsoft Graph, a API do Office e a API do Azure. Todos eles residem fora do domínio de onde o aplicativo é fornecido. Uma tendência crescente para aplicativos JavaScript é não ter um back-end, recorrendo totalmente a APIs Web de terceiros para implementar sua função de negócios.

Atualmente, o método preferencial para proteger chamadas para uma API Web é usar a abordagem de token de portador de OAuth2, em que cada chamada é acompanhada por um token de acesso OAuth2. A API Web examina o token de acesso de entrada e, se encontrar nele os escopos necessários, concederá acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para que aplicativos JavaScript obtenham tokens de acesso para uma API Web, com diversas vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma confiável sem a necessidade de chamadas entre origens; o registro obrigatório do URI de redirecionamento ao qual os tokens são retornados garante que os tokens não sejam substituídos
* Aplicativos JavaScript podem obter tantos tokens de acesso quantos forem necessários, para todas as APIs Web que direcionam, sem restrição quanto aos domínios
* Recursos de HTML5, como armazenamento local ou por sessão, concedem controle total sobre o cache de tokens e o gerenciamento de tempo de vida, enquanto o gerenciamento de cookies é opaco para o aplicativo
* Tokens de acesso não são suscetíveis a ataques de CSRF (solicitação intersite forjada)

O fluxo de concessão implícita não emite tokens de atualização, principalmente por motivos de segurança. Um token de atualização não tem um escopo restrito como tokens de acesso, concedendo muito mais energia, provocando muito mais danos caso ele seja vazado. No fluxo implícito, os tokens são entregues na URL, portanto, o risco de interceptação é maior do que na concessão de código de autorização.

No entanto, um aplicativo JavaScript tem outro mecanismo à sua disposição para renovar tokens de acesso sem solicitar repetidamente as credenciais ao usuário. O aplicativo pode usar um iframe oculto para executar novas solicitações de token em relação ao ponto de extremidade de autorização do Azure AD: enquanto o navegador ainda tiver uma sessão ativa (ou seja, tiver um cookie de sessão) em relação ao domínio do Azure AD, a solicitação de autenticação poderá ocorrer com êxito sem necessidade de interação do usuário.

Esse modelo concede ao aplicativo JavaScript a capacidade de renovar independentemente os tokens de acesso e até mesmo adquirir novos tokens para uma nova API (desde que o usuário os tenha consentido anteriormente). Isso evita a sobrecarga adicional de aquisição, manutenção e proteção de um artefato de alto valor, como um token de atualização. O artefato que possibilita a renovação silenciosa, o cookie de sessão do Azure AD, é gerenciado fora do aplicativo. Outra vantagem dessa abordagem é que um usuário pode sair do Azure AD, usando qualquer um dos aplicativos conectados ao Azure AD, em execução em qualquer uma das guias do navegador. Isso resulta na exclusão do cookie de sessão do Azure AD, e o aplicativo JavaScript automaticamente perde a capacidade de renovar tokens para o usuário desconectado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A concessão implícita é adequada para meu aplicativo?

A concessão implícita apresenta mais riscos do que outras concessões, e as áreas que você precisa prestar atenção são bem documentadas (por exemplo, [uso indevido do token de acesso para representar o proprietário do recurso no fluxo implícito] [OAuth2-spec-implícito-incerto] e [modelo de ameaça OAuth 2,0 e considerações sobre segurança] [OAuth2-Threat-Model-and-Security-implicações]). No entanto, o perfil de risco mais alto é amplamente devido ao fato de que seu objetivo é habilitar aplicativos que executam código ativo, fornecido por um recurso remoto a um navegador. Se você estiver planejando uma arquitetura SPA, não tem um componente de back-end ou pretende invocar uma API Web por meio de JavaScript, é recomendável usar o fluxo implícito para aquisição de token.

Se o aplicativo é um cliente nativo, o fluxo implícito não é a melhor opção. A ausência do cookie de sessão do Azure AD no contexto de um cliente nativo priva o aplicativo dos meios de manter uma sessão com vida útil longa. Isso significa que o aplicativo se dirigirá repetidamente ao usuário ao obter tokens de acesso para novos recursos.

Se você está desenvolvendo um aplicativo Web que inclui um back-end e que consome uma API de seu código de back-end, o fluxo implícito também não é uma boa opção. Outras concessões oferecem capacidade muito maior. Por exemplo, a concessão de credenciais de cliente OAuth2 fornece a capacidade de obter tokens que refletem as permissões atribuídas ao próprio aplicativo, em vez de delegações de usuário. Isso significa que o cliente tem a capacidade de manter o acesso programático a recursos mesmo quando um usuário não está ativamente envolvido em uma sessão e assim por diante. Não apenas isso, mas essas concessões dão garantias de segurança mais alta. Por exemplo, tokens de acesso nunca transitam pelo navegador do usuário, não correm o risco de ser salvos no histórico do navegador e assim por diante. O aplicativo cliente também pode executar a autenticação forte ao solicitar um token.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra a aparência de todo fluxo de entrada implícita e as seções a seguir descrevem cada etapa em mais detalhes.

![Diagrama mostrando o fluxo de entrada implícito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

Para conectar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autenticação do [OpenID Connect](v2-protocols-oidc.md) e obter um `id_token` do ponto de extremidade da plataforma Microsoft Identity.

> [!IMPORTANT]
> Para solicitar um token de ID e/ou um token de acesso com êxito, o registro do aplicativo na página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) deve ter o fluxo de concessão implícito correspondente habilitado, selecionando **tokens de ID** e. ou **tokens de acesso** na seção **concessão implícita** . Se não estiver habilitado, um erro de `unsupported_response` será retornado: **o valor fornecido para o parâmetro de entrada ' response_type ' não é permitido para este cliente. O valor esperado é ' code '**

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
> Para testar a entrada usando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Depois de entrar, seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
>

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` | obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obrigatório | A ID do aplicativo (cliente) que a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo. |
| `response_type` | obrigatório |Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização. Se você usar o response_type de `token`, o parâmetro `scope` deverá conter um escopo indicando para qual recurso emitir o token (por exemplo, User. Read on Microsoft Graph).  |
| `redirect_uri` | recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| `scope` | obrigatório |Uma lista de [escopos](v2-permissions-and-consent.md) separados por espaços. Para o OpenID Connect (id_tokens), ele deve incluir o escopo `openid`, que se traduz com a permissão "entrar" na interface do usuário de consentimento. Opcionalmente, talvez você também queira incluir os escopos `email` e `profile` para obter acesso a dados de usuário adicionais. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento para vários recursos, se um token de acesso for solicitado. |
| `response_mode` | opcionais |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. O padrão é consultar apenas um token de acesso, mas fragmentar se a solicitação incluir uma id_token. |
| `state` | recomendável |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `nonce` | obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Necessário somente quando um id_token é solicitado. |
| `prompt` | opcionais |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'select_account' e 'consent'. `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único. `prompt=none` é o oposto, ele garantirá que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade da plataforma de identidade da Microsoft retornará um erro. `prompt=select_account` envia o usuário para um seletor de conta em que todas as contas lembradas na sessão serão exibidas. `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| `login_hint`  |opcionais |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`.|
| `domain_hint` | opcionais |Se for incluído, ele ignorará o processo de descoberta baseado em email que o usuário passa na página de entrada, levando a uma experiência de usuário um pouco mais simplificada. Isso é normalmente usado para aplicativos de linha de negócios que operam em um único locatário, no qual eles fornecerão um nome de domínio dentro de um determinado locatário.  Isso encaminhará o usuário para o provedor de Federação para esse locatário.  Observe que isso impedirá convidados de entrar neste aplicativo.  |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade da plataforma Microsoft Identity também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não consentir a **nenhuma** dessas permissões, ele será solicitado a consentir às permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos multilocatários](v2-permissions-and-consent.md).

Depois que o usuário autenticar e conceder consentimento, o ponto de extremidade da plataforma de identidade da Microsoft retornará uma resposta ao seu aplicativo no `redirect_uri`indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é semelhante ao seguinte (com quebras de linha para legibilidade):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso que o aplicativo solicitou. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` |Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` incluir `token`. Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` |Incluído se `response_type` incluir `token`. Indica os escopos para os quais access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (Token Web JSON) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [`id_token reference`](id-tokens.md). <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Obtendo tokens de acesso silenciosamente em segundo plano

Agora que você assinou o usuário em seu aplicativo de página única, você pode obter silenciosamente tokens de acesso para chamar APIs da Web protegidas pela plataforma de identidade da Microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal do OpenID Connect/OAuth, você faria isso fazendo uma solicitação para o ponto de extremidade de `/token` da plataforma de identidade da Microsoft. No entanto, o ponto de extremidade da plataforma Microsoft Identity não oferece suporte a solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora da pergunta. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web: 

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
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
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
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` | Indica os escopos para os quais access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (Token Web JSON) assinado. Incluído se `response_type` incluir `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, veja a [referência `id_token`](id-tokens.md). <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
| `state` |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente. No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token. Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## <a name="refreshing-tokens"></a>Atualizando tokens

A concessão implícita não fornece tokens de atualização. `id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. Para atualizar qualquer tipo de token, você pode executar a mesma solicitação de iframe oculto acima usando o parâmetro `prompt=none` para controlar o comportamento da plataforma de identidade. Se você quiser receber uma nova `id_token`, certifique-se de usar `id_token` no `response_type` e `scope=openid`, bem como um parâmetro de `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de desconexão

O `end_session_endpoint` OpenID Connect permite que seu aplicativo envie uma solicitação ao ponto de extremidade da plataforma Microsoft Identity para encerrar a sessão de um usuário e limpar os cookies definidos pelo ponto de extremidade da plataforma Microsoft Identity. Para desconectar por completo um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente, limpando o cache de token ou removendo cookies) e, depois, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recomendável | A URL para a qual o usuário deve retornar após a conclusão do logoff. Esse valor deve corresponder a um dos URIs de redirecionamento registrados no aplicativo. Se não estiver incluído, o usuário receberá uma mensagem genérica do ponto de extremidade da plataforma Microsoft Identity. |

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Percorra os [exemplos de MSAL JS](sample-v2-code.md) para começar a codificação.
