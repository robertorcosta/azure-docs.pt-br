---
title: OAuth 2.0 fluxo implícito de subsídios - plataforma de identidade Microsoft | Azure
description: Proteja aplicativos de página única usando o fluxo implícito da plataforma de identidade da Microsoft.
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
ms.openlocfilehash: 53d498f4aed8ec86cc57c35824a9fb8aa471dc1d
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419674"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade Microsoft e fluxo de subvenção implícita

Com o ponto final da plataforma de identidade da Microsoft, você pode inscrever usuários em seus aplicativos de página única com contas pessoais e de trabalho ou escola da Microsoft. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

* As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não são compatíveis com solicitações CORS.
* Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para esses aplicativos (Angular, Ember.js, React.js e assim por diante), a plataforma de identidade da Microsoft suporta o fluxo de subvenção implícita OAuth 2.0. O fluxo está descrito na [Especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Seu principal benefício é que ele permite que o aplicativo obtenha tokens da plataforma de identidade da Microsoft sem realizar uma troca de credenciais de servidor backend. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente. Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](https://tools.ietf.org/html/rfc6749#section-10.3).

Este artigo descreve como programar diretamente contra o protocolo em sua aplicação.  Quando possível, recomendamos que você use as Bibliotecas de Autenticação Microsoft (MSAL) suportadas em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Também dê uma olhada nos [aplicativos de exemplo que usam msal](sample-v2-code.md).

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [!NOTE]
> Nem todos os cenários e recursos do Azure Active Directory (Azure AD) são suportados pelo ponto final da plataforma de identidade da Microsoft. Para determinar se você deve usar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a concessão implícita OAuth2

A especificação OAuth2 declara que a concessão implícita foi concebida para habilitar aplicativos de agente do usuário, ou seja, aplicativos JavaScript em execução em um navegador. A característica que define esses aplicativos é que o código JavaScript é usado para acessar recursos de servidor (normalmente, uma API Web) e para atualizar a experiência do usuário do aplicativo de maneira adequada. Pense em aplicativos como Gmail ou Outlook Web Access: quando você seleciona uma mensagem da caixa de entrada, apenas o painel de visualização da mensagem muda para exibir a nova seleção, enquanto o restante da página permanece inalterado. Essa característica difere de aplicativos Web tradicionais baseados em redirecionamento, em que cada interação do usuário resulta em um postback de página inteira e em uma renderização de página inteira da nova resposta do servidor.

Os aplicativos que levam a abordagem baseada em JavaScript ao extremo são chamados de SPAs ou aplicativos de página única. A ideia é que esses aplicativos servem apenas uma página HTML inicial e o JavaScript associado, com todas as interações posteriores sendo conduzidas por chamadas à API Web realizadas por JavaScript. No entanto, as abordagens híbridas, em que o aplicativo é principalmente orientado por postback, mas executa chamadas JS ocasionais, não são incomuns. A discussão sobre o uso de fluxo implícitos também é relevante para eles.

Aplicativos baseados em redirecionamento normalmente protegem suas solicitações por meio de cookies. No entanto, essa abordagem não funciona bem para aplicativos JavaScript. Cookies só funcionam em relação ao domínio em que foram gerados, enquanto chamadas JavaScript podem ser direcionadas para outros domínios. De fato, frequentemente esse será o caso: pense em aplicativos que invocam a API do Microsoft Graph, a API do Office e a API do Azure. Todos eles residem fora do domínio de onde o aplicativo é fornecido. Uma tendência crescente para aplicativos JavaScript é não ter um back-end, recorrendo totalmente a APIs Web de terceiros para implementar sua função de negócios.

Atualmente, o método preferencial para proteger chamadas para uma API Web é usar a abordagem de token de portador de OAuth2, em que cada chamada é acompanhada por um token de acesso OAuth2. A API Web examina o token de acesso de entrada e, se encontrar nele os escopos necessários, concederá acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para que aplicativos JavaScript obtenham tokens de acesso para uma API Web, com diversas vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma confiável sem a necessidade de chamadas entre origens; o registro obrigatório do URI de redirecionamento ao qual os tokens são retornados garante que os tokens não sejam substituídos
* Aplicativos JavaScript podem obter tantos tokens de acesso quantos forem necessários, para todas as APIs Web que direcionam, sem restrição quanto aos domínios
* Recursos de HTML5, como armazenamento local ou por sessão, concedem controle total sobre o cache de tokens e o gerenciamento de tempo de vida, enquanto o gerenciamento de cookies é opaco para o aplicativo
* Os tokens de acesso não são suscetíveis a ataques de falsificação de solicitação entre sites (CSRF)

O fluxo de concessão implícita não emite tokens de atualização, principalmente por motivos de segurança. Um token de atualização não é tão restrito quanto os tokens de acesso, concedendo muito mais energia, infligindo muito mais dano no caso de vazar. No fluxo implícito, os tokens são entregues na URL, portanto, o risco de interceptação é maior do que na concessão do código de autorização.

No entanto, um aplicativo JavaScript tem outro mecanismo à sua disposição para renovar tokens de acesso sem solicitar repetidamente as credenciais ao usuário. O aplicativo pode usar um iframe oculto para executar novas solicitações de token em relação ao ponto de extremidade de autorização do Azure AD: enquanto o navegador ainda tiver uma sessão ativa (ou seja, tiver um cookie de sessão) em relação ao domínio do Azure AD, a solicitação de autenticação poderá ocorrer com êxito sem necessidade de interação do usuário.

Esse modelo concede ao aplicativo JavaScript a capacidade de renovar independentemente os tokens de acesso e até mesmo adquirir novos tokens para uma nova API (desde que o usuário os tenha consentido anteriormente). Isso evita a sobrecarga adicional de aquisição, manutenção e proteção de um artefato de alto valor, como um token de atualização. O artefato que possibilita a renovação silenciosa, o cookie de sessão do Azure AD, é gerenciado fora do aplicativo. Outra vantagem dessa abordagem é que um usuário pode sair do Azure AD, usando qualquer um dos aplicativos conectados ao Azure AD, em execução em qualquer uma das guias do navegador. Isso resulta na exclusão do cookie de sessão do Azure AD, e o aplicativo JavaScript automaticamente perde a capacidade de renovar tokens para o usuário desconectado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A concessão implícita é adequada para meu aplicativo?

A concessão implícita apresenta mais riscos do que outras concessões e as áreas que você precisa prestar atenção estão bem documentadas (por exemplo, [Token de Acesso para Representar o Proprietário do Recurso em Fluxo Implícito][OAuth2-Spec-Implicit-Misuse]e [Modelo de Ameaça do OAuth 2.0 e Considerações de Segurança][OAuth2-Threat-Model-And-Security-Implications]). No entanto, o perfil de risco mais alto é amplamente devido ao fato de que seu objetivo é habilitar aplicativos que executam código ativo, fornecido por um recurso remoto a um navegador. Se você estiver planejando uma arquitetura SPA, não tem um componente de back-end ou pretende invocar uma API Web por meio de JavaScript, é recomendável usar o fluxo implícito para aquisição de token.

Se sua aplicação é um cliente nativo, o fluxo implícito não é um grande ajuste. A ausência do cookie de sessão do Azure AD no contexto de um cliente nativo priva o aplicativo dos meios de manter uma sessão com vida útil longa. Isso significa que o aplicativo se dirigirá repetidamente ao usuário ao obter tokens de acesso para novos recursos.

Se você está desenvolvendo um aplicativo Web que inclui um back-end e que consome uma API de seu código de back-end, o fluxo implícito também não é uma boa opção. Outras concessões oferecem capacidade muito maior. Por exemplo, a concessão de credenciais de cliente OAuth2 fornece a capacidade de obter tokens que refletem as permissões atribuídas ao próprio aplicativo, em vez de delegações de usuário. Isso significa que o cliente tem a capacidade de manter o acesso programático a recursos mesmo quando um usuário não está ativamente envolvido em uma sessão e assim por diante. Não apenas isso, mas essas concessões dão garantias de segurança mais alta. Por exemplo, os tokens de acesso nunca transitam pelo navegador do usuário, eles não correm o risco de serem salvos no histórico do navegador, e assim por diante. O aplicativo cliente também pode executar a autenticação forte ao solicitar um token.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra a aparência de todo fluxo de entrada implícita e as seções a seguir descrevem cada etapa em mais detalhes.

![Diagrama mostrando o fluxo implícito de entrada](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão

Para inicialmente inscrever o usuário em seu aplicativo, você pode enviar `id_token` uma solicitação de autenticação [do OpenID Connect](v2-protocols-oidc.md) e obter um ponto final da plataforma de identidade da Microsoft.

> [!IMPORTANT]
> Para solicitar com sucesso um token de ID e/ou um token de acesso, o registro do aplicativo no [portal Azure - Página de registros de aplicativos](https://go.microsoft.com/fwlink/?linkid=2083908) deve ter o fluxo de concessão implícito correspondente ativado, selecionando **tokens de ID** e.ou **tokens de acesso** sob a seção de **subvenção implícita.** Se não estiver habilitado, `unsupported_response` um erro será devolvido: **O valor fornecido para o parâmetro de entrada 'response_type' não é permitido para este cliente. Valor esperado é 'código'**

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
> Para testar a login usando <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a> o fluxo implícito, clique em . Após fazer login, seu navegador `https://localhost/myapp/` deve `id_token` ser redirecionado para um na barra de endereços.
>

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` | obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obrigatório | O ID do aplicativo (cliente) que o [portal Azure - Página de registros do aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído ao seu aplicativo. |
| `response_type` | obrigatório |Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir o response_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização. Se você `token` usar o `scope` response_type, o parâmetro deve conter um escopo indicando qual recurso emitir o token para (por exemplo, user.read no Microsoft Graph).  |
| `redirect_uri` | recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| `scope` | obrigatório |Uma lista de [escopos separados](v2-permissions-and-consent.md)pelo espaço. Para o OpenID Connect (id_tokens), `openid`ele deve incluir o escopo , que se traduz na permissão "Assinar você" na ui de consentimento. Opcionalmente, você também pode `email` `profile` querer incluir os e escopos para obter acesso a dados adicionais do usuário. Você também pode incluir outros escopos nesta solicitação para solicitar consentimento a vários recursos, se um token de acesso for solicitado. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Padrão para consultar apenas um token de acesso, mas fragmente se a solicitação incluir um id_token. |
| `state` | recomendável |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor único gerado aleatoriamente é normalmente usado para [evitar ataques de falsificação de solicitação entre sites](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| `nonce` | obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Necessário somente quando um id_token é solicitado. |
| `prompt` | opcional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'select_account' e 'consent'. `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único. `prompt=none`é o oposto - ele garantirá que o usuário não seja apresentado com qualquer solicitação interativa. Se a solicitação não puder ser concluída silenciosamente por meio de um único sinal, o ponto final da plataforma de identidade da Microsoft retornará um erro. `prompt=select_account` envia o usuário para um seletor de conta em que todas as contas lembradas na sessão serão exibidas. `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| `login_hint`  |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`.|
| `domain_hint` | opcional |Se incluído, ele pulará o processo de detecção baseado em e-mail pelo que o usuário passa na página de login, levando a uma experiência de usuário um pouco mais simplificada. Isso é comumente usado para aplicativos da Linha de Negócios que operam em um único inquilino, onde eles fornecerão um nome de domínio dentro de um determinado inquilino.  Isso encaminhará o usuário ao provedor da federação para aquele inquilino.  Observe que isso impedirá que os hóspedes entrem neste aplicativo.  |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto final da plataforma de identidade da Microsoft também garantirá `scope` que o usuário tenha consentido com as permissões indicadas no parâmetro de consulta. Se o usuário não consentir a **nenhuma** dessas permissões, ele será solicitado a consentir às permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos multilocatários](v2-permissions-and-consent.md).

Uma vez que o usuário autentica e concede consentimento, o ponto final da `redirect_uri`plataforma de identidade da `response_mode` Microsoft retornará uma resposta ao seu aplicativo no indicado, usando o método especificado no parâmetro.

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
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso que o aplicativo solicitou. O token de acesso não deve ser decodificado ou inspecionado de outra forma, deve ser tratado como uma seqüência opaca. |
| `token_type` |Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` incluir `token`. Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` |Incluído se `response_type` incluir `token`. Indica os escopos para os quais access_token será válido. Não pode incluir todos os escopos solicitados, se não forem aplicáveis ao usuário (no caso de escopos somente Azure AD sendo solicitados quando uma conta pessoal é usada para fazer login). |
| `id_token` | Um JWT (Token Web JSON) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações [`id_token reference`](id-tokens.md)sobre id_tokens, consulte o . <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
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

Agora que você inscreveu o usuário em seu aplicativo de página única, você pode silenciosamente obter tokens de acesso para chamar APIs da Web protegidas pela plataforma de identidade da Microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo se já tiver recebido um token usando o response_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal openid connect/oauth, você faria isso fazendo uma `/token` solicitação ao ponto final da plataforma de identidade da Microsoft. No entanto, o ponto final da plataforma de identidade da Microsoft não suporta solicitações cors, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora de questão. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web:

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
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, deve ser tratado como uma seqüência opaca. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| `scope` | Indica os escopos para os quais access_token será válido. Não pode incluir todos os escopos solicitados, se não forem aplicáveis ao usuário (no caso de escopos somente Azure AD sendo solicitados quando uma conta pessoal é usada para fazer login). |
| `id_token` | Um JWT (Token Web JSON) assinado. Incluído se `response_type` incluir `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que fez login. O aplicativo pode armazenar os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações [ `id_token` ](id-tokens.md)sobre id_tokens, consulte a referência . <br> **Observação:** somente fornecido se o escopo `openid` for solicitado. |
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

A concessão implícita não fornece tokens de atualização. `id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. Para atualizar qualquer tipo de token, você pode executar a `prompt=none` mesma solicitação oculta de iframe de cima usando o parâmetro para controlar o comportamento da plataforma de identidade. Se você quiser receber `id_token`um novo `id_token` , `response_type` certifique-se de usar no e `scope=openid`, bem como um `nonce` parâmetro.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de desconexão

O OpenID `end_session_endpoint` Connect permite que seu aplicativo envie uma solicitação para o ponto final da plataforma de identidade da Microsoft para encerrar a sessão do usuário e limpar cookies definidos pelo ponto final da plataforma de identidade da Microsoft. Para desconectar por completo um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente, limpando o cache de token ou removendo cookies) e, depois, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recomendável | A URL para a qual o usuário deve retornar após a conclusão do logoff. Esse valor deve corresponder a um dos URIs de redirecionamento registrados no aplicativo. Se não estiver incluído, o usuário será mostrado uma mensagem genérica pelo ponto final da plataforma de identidade da Microsoft. |

## <a name="next-steps"></a>Próximas etapas

* Percorra os [exemplos de MSAL JS](sample-v2-code.md) para começar a codificação.
