---
title: Login da Web com OpenID Connect - Azure Active Directory B2C
description: Crie aplicativos web usando o protocolo de autenticação OpenID Connect no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264382"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Entrada na Web com o OpenID Connect no Azure Active Directory B2C

O OpenID Connect é um protocolo de autenticação criado com base em OAuth 2.0 que pode ser usado para conectar com segurança os usuários em aplicativos Web. Ao usar a implementação do Azure AD B2C (Azure Active Directory B2C) do OpenID Connect, você pode terceirizar a inscrição, a entrada e outras experiências de gerenciamento de identidade em seus aplicativos Web para o Azure AD (Azure Active Directory). Este guia mostra como fazer isso de maneira independente da linguagem. Ele descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

O [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 a ser usado como um protocolo de *autenticação*. Este protocolo de autenticação permite que você realize o único login. Ele introduz o conceito de um token de *Identificação,* que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário.

Como estende o OAuth 2.0, ele também permite que os aplicativos adquiram com segurança *tokens de acesso*. Você pode usar tokens de acesso para acessar os recursos protegidos por um [servidor de autorização](protocols-overview.md). O OpenID Connect é recomendado se você estiver construindo um aplicativo web hospedado em um servidor e acessado através de um navegador. Para obter mais informações sobre tokens, consulte a [visão geral dos tokens no Azure Active Directory B2C](tokens-overview.md)

O Azure AD B2C estende o protocolo padrão OpenID Connect para fazer mais do que uma simples ação de autenticação e autorização. Ele introduz o [parâmetro de fluxo](user-flow-overview.md)do usuário , que permite que você use o OpenID Connect para adicionar experiências de usuário ao seu aplicativo, como inscrição, login e gerenciamento de perfil.

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação

Quando seu aplicativo web precisa autenticar o usuário e executar um fluxo `/authorize` de usuário, ele pode direcionar o usuário para o ponto final. O usuário toma medidas dependendo do fluxo do usuário.

Nesta solicitação, o cliente indica as permissões que precisa `scope` adquirir do usuário no parâmetro e especifica o fluxo de usuário para executar. Para ter uma noção de como a solicitação funciona, tente colar a solicitação em um navegador e executá-la. Substitua `{tenant}` pelo nome do seu locatário. Substitua pelo `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` ID do aplicativo que você já registrou anteriormente em seu inquilino. Também mude o`{policy}`nome da política ( ) para o `b2c_1_sign_in`nome da política que você tem em seu inquilino, por exemplo .

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou no seu inquilino Azure AD B2C. Por `b2c_1_sign_in`exemplo: `b2c_1_sign_up`, `b2c_1_edit_profile`, ou . |
| client_id | Sim | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu ao seu aplicativo. |
| nonce | Sim | Um valor incluído na solicitação (gerada pelo aplicativo) que está incluído no token de ID resultante como uma reclamação. O aplicativo pode então verificar esse valor para mitigar ataques de repetição de tokens. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| response_type | Sim | Deve incluir um token de ID para OpenID Connect. Se o seu aplicativo web também precisar de tokens `code+id_token`para chamar uma API web, você pode usar . |
| scope | Sim | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para entrar no usuário e obter dados sobre ele na forma de tokens de ID. O `offline_access` escopo é opcional para aplicações web. Isso indica que seu aplicativo precisará de um *token de atualização para* acesso estendido aos recursos. |
| prompt | Não | O tipo de interação do usuário que é necessária. O único valor válido no momento é `login`, que força o usuário a inserir suas credenciais nessa solicitação. |
| redirect_uri | Não | O `redirect_uri` parâmetro do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente `redirect_uri` a um dos parâmetros que você registrou no portal Azure, exceto que ele deve ser codificado por URL. |
| response_mode | Não | O método que é usado para enviar o código de autorização resultante de volta ao seu aplicativo. Ele pode ser `query`, `form_post` ou `fragment`.  O modo de resposta `form_post` é recomendado para maior segurança. |
| state | Não | Um valor incluído na solicitação que também é devolvido na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrer, como a página em que eles estavam. |

Neste ponto, o usuário é solicitado a completar o fluxo de trabalho. O usuário pode ter que digitar seu nome de usuário e senha, entrar com uma identidade social ou se inscrever no diretório. Pode haver qualquer outro número de etapas, dependendo de como o fluxo de usuário é definido.

Depois que o usuário completa o fluxo do usuário, uma `redirect_uri` resposta é retornada ao seu aplicativo no `response_mode` parâmetro indicado, usando o método especificado no parâmetro. A resposta é a mesma para cada um dos casos anteriores, independente do fluxo de usuário.

Uma resposta bem-sucedida usando `response_mode=fragment` se parece com esta:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| id_token | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| código | O código de autorização que o `response_type=code+id_token`aplicativo solicitou, se você usou . O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Os códigos de autorização normalmente expiram após cerca de 10 minutos. |
| state | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve `state` verificar se os valores na solicitação e resposta são idênticos. |

As respostas de erro também `redirect_uri` podem ser enviadas ao parâmetro para que o aplicativo possa manuseá-las adequadamente:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudar a identificar a causa raiz de um erro de autenticação. |
| state | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve `state` verificar se os valores na solicitação e resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Apenas o recebimento de um tokend de ID não é suficiente para autenticar o usuário. Valide a assinatura do token de ID e verifique as reclamações no token de acordo com os requisitos do aplicativo. O Azure AD B2C usa [JWTs (Tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos. Há muitas bibliotecas de software livre para validar JWTs dependendo do idioma de preferência. Recomendamos que você explore essas opções em vez de implementar a sua própria lógica de validação.

O Azure AD B2C possui um ponto final de metadados do OpenID Connect, que permite que um aplicativo obtenha informações sobre o Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada fluxo de usuário no locatário B2C. Por exemplo, o documento de metadados para o fluxo de usuário `b2c_1_sign_in` em `fabrikamb2c.onmicrosoft.com` está localizado em:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades desse documento de configuração é a `jwks_uri`, cujo valor para o mesmo fluxo de usuário seria:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual fluxo de usuário foi usado na assinatura de um token de ID (e de onde obter os metadados), você tem duas opções. Primeiro, o nome do fluxo de usuário é incluído na declaração `acr` no token de ID. A outra opção é codificar o fluxo de usuário no valor do parâmetro `state` quando você emitir a solicitação e, em seguida, decodificá-lo para determinar qual fluxo de usuário foi usado. Ambos os métodos são válidos.

Depois de adquirir o documento de metadados do ponto final de metadados do OpenID Connect, você pode usar as chaves públicas RSA 256 para validar a assinatura do token ID. Pode haver várias chaves listadas neste ponto `kid` final, cada uma identificada por uma reivindicação. O cabeçalho do token de ID também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o token de ID.

Para verificar os tokens do Azure AD B2C, você precisa gerar a chave pública usando o expoente(e) e o módulo(n). Você precisa determinar como fazer isso em sua respectiva linguagem de programação de acordo. A documentação oficial sobre a geração de Chaves Públicas com o protocolo RSA pode ser encontrada aqui:https://tools.ietf.org/html/rfc3447#section-3.1

Depois de validar a assinatura do token de ID, há várias declarações que você precisa verificar. Por exemplo:

- Valide a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
- Valide `aud` a alegação para garantir que o token de ID foi emitido para sua aplicação. Seu valor deve ser o ID de aplicação da sua aplicação.
- Valide `iat` `exp` o e afirma ter certeza de que o token de ID não expirou.

Também há várias outras validações que devem ser realizadas. As validações são descritas detalhadamente na [especificação do núcleo de conexão OpenID](https://openid.net/specs/openid-connect-core-1_0.html). Você também pode querer validar reivindicações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

- Garantindo que o usuário/organização tenha se inscrito para o aplicativo.
- A garantia de que o usuário tenha a autorização/os privilégios adequados.
- A garantia de que uma determinada intensidade de autenticação tenha ocorrido, como a Autenticação Multifator do Azure.

Depois de validar o token id, você pode iniciar uma sessão com o usuário. Você pode usar as reivindicações no token ID para obter informações sobre o usuário em seu aplicativo. Os usos para essas informações incluem exibição, registros e autorização.

## <a name="get-a-token"></a>Obter um token

Se você precisar do seu aplicativo web para executar apenas fluxos de usuário, você pode pular as próximas seções. Essas seções são aplicáveis apenas a aplicativos web que precisam fazer chamadas autenticadas para uma API web e também são protegidas pelo Azure AD B2C.

Você pode resgatar o código de autorização adquirido (usando `response_type=code+id_token`) para um token do recurso desejado enviando uma solicitação `POST` ao ponto de extremidade `/token`. No Azure AD B2C, você pode [solicitar tokens de acesso para outras APIs,](access-tokens.md#request-a-token) como de costume, especificando seu escopo na solicitação.

Você também pode solicitar um token de acesso para a API web back-end do seu aplicativo por convenção de usar o ID do cliente do aplicativo como o escopo solicitado (o que resultará em um token de acesso com esse ID do cliente como "público"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de usuário que foi usado para adquirir o código de autorização. Você não pode usar um fluxo de usuário diferente nesta solicitação. Adicione este parâmetro à seqüência de consulta, não ao corpo POST. |
| client_id | Sim | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu ao seu aplicativo. |
| client_secret | Sim, em Web Apps | O segredo do aplicativo que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários do Web App, onde o cliente pode armazenar com segurança um segredo do cliente. Para cenários nativos do App (cliente público), os segredos do cliente não podem ser armazenados com segurança, portanto, não são usados neste fluxo. Se usar um segredo de cliente, por favor, mude-o em uma base periódica. |
| código | Sim | O código de autorização que você adquiriu no início do fluxo de usuário. |
| grant_type | Sim | O tipo de concessão, que deve ser `authorization_code` para o fluxo do código de autorização. |
| redirect_uri | Sim | O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| scope | Não | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre ele na forma de parâmetros de id_token. Ele pode ser usado para obter tokens para a API web back-end do seu aplicativo, que é representada pelo mesmo ID de aplicativo que o cliente. O `offline_access` escopo indica que seu aplicativo precisa de um token de atualização para acesso estendido aos recursos. |

Uma resposta de token bem-sucedida tem a seguinte aparência:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| not_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type | O valor do tipo de token. `Bearer`é o único tipo que é suportado. |
| access_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token é válido. |
| expires_in | O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens adicionais após o vencimento do token atual. Os tokens de atualização podem ser usados para reter acesso aos recursos por longos períodos de tempo. O `offline_access` escopo deve ter sido usado tanto nas solicitações de autorização quanto de token para receber um token de atualização. |

As respostas de erro se parecem com:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="use-the-token"></a>Usar o token

Agora que adquiriu um token de acesso com êxito, você poderá usá-lo em solicitações às suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token

Os tokens de ID expiram em um curto período de tempo. Atualize os tokens depois que eles expirarem para continuar a ter acesso aos recursos. Você pode atualizar um token `POST` enviando `/token` outra solicitação para o ponto final. Desta vez, forneça o parâmetro `refresh_token` em vez do parâmetro `code`:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de usuário que foi usado para adquirir o token de atualização original. Você não pode usar um fluxo de usuário diferente nesta solicitação. Adicione este parâmetro à seqüência de consulta, não ao corpo POST. |
| client_id | Sim | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu ao seu aplicativo. |
| client_secret | Sim, em Web Apps | O segredo do aplicativo que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários do Web App, onde o cliente pode armazenar com segurança um segredo do cliente. Para cenários nativos do Aplicativo (cliente público), os segredos do cliente não podem ser armazenados com segurança, portanto, não são usados nesta chamada. Se usar um segredo de cliente, por favor, mude-o em uma base periódica. |
| grant_type | Sim | O tipo de subvenção, que deve ser um token de atualização para esta parte do fluxo do código de autorização. |
| refresh_token | Sim | O token de atualização original que foi adquirido na segunda parte do fluxo. O `offline_access` escopo deve ser usado tanto nas solicitações de autorização quanto de token para receber um token de atualização. |
| redirect_uri | Não | O parâmetro `redirect_uri` do aplicativo em que você recebeu o código de autorização. |
| scope | Não | Uma lista de escopos separados por espaços. O escopo `openid` indica uma permissão para entrar no usuário e obter dados sobre ele na forma de tokens de ID. Ele pode ser usado para enviar tokens para a API web back-end do seu aplicativo, que é representada pelo mesmo ID de aplicativo que o cliente. O `offline_access` escopo indica que seu aplicativo precisa de um token de atualização para acesso estendido aos recursos. |

Uma resposta de token bem-sucedida tem a seguinte aparência:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| not_before | A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type | O valor do tipo de token. `Bearer`é o único tipo que é suportado. |
| access_token | O token JWT assinado que foi solicitado. |
| scope | O escopo para o qual o token é válido. |
| expires_in | O período de tempo pelo qual o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens adicionais após o vencimento do token atual. Os tokens de atualização podem ser usados para reter acesso aos recursos por longos períodos de tempo. |

As respostas de erro se parecem com:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| error | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja assinar com o usuário fora do aplicativo, não é suficiente para limpar os cookies do aplicativo ou, de outra forma, terminar a sessão com o usuário. Redirecione o usuário para o Azure AD B2C para sair. Se você não fizer isso, o usuário pode ser capaz de reautenticar seu aplicativo sem inserir suas credenciais novamente.

Para fazer logon do usuário, `end_session` redirecione o usuário para o ponto final listado no documento de metadados OpenID Connect descrito anteriormente:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de usuário que você quer usar para desconectar o usuário do aplicativo. |
| id_token_hint| Não | Um token de ID emitido anteriormente para passar para o ponto final de logout como uma dica sobre a sessão autenticada atual do usuário final com o cliente. O `id_token_hint` garante que `post_logout_redirect_uri` a URL de resposta registrada é uma URL de resposta registrada nas configurações do aplicativo Azure AD B2C. |
| client_id | Não* | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu ao seu aplicativo.<br><br>\**Isso é necessário `Application` ao usar a configuração de Isolamento SSO `No`e exigir que o _ID Token_ na solicitação de logout seja definido como .* |
| post_logout_redirect_uri | Não | A URL para a a nossa ção que o usuário deve ser redirecionado após a saída bem sucedida. Se não estiver incluído, o Azure AD B2C mostra ao usuário uma mensagem genérica. A menos `id_token_hint`que você forneça um , você não deve registrar esta URL como uma URL de resposta nas configurações do aplicativo Azure AD B2C. |
| state | Não | Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve `state` verificar se os valores na solicitação e resposta são idênticos. |

### <a name="secure-your-logout-redirect"></a>Proteja seu redirecionamento de logout

Após o logout, o usuário é redirecionado `post_logout_redirect_uri` para o URI especificado no parâmetro, independentemente das URLs de resposta especificadas para o aplicativo. No entanto, `id_token_hint` se um valid for aprovado, o Azure `post_logout_redirect_uri` AD B2C verifica se o valor de corresponde a uma das URIs de redirecionamento configuradas do aplicativo antes de realizar o redirecionamento. Se nenhuma URL de resposta correspondente foi configurada para o aplicativo, uma mensagem de erro será exibida e o usuário não será redirecionado.

### <a name="external-identity-provider-sign-out"></a>Saída do provedor de identidade externa

Direcionar o usuário `end_session` para o ponto final limpa parte do estado de tipo único do usuário com o Azure AD B2C, mas ele não assina a sessão de iDP do provedor de identidade social (IDP). Se o usuário selecionar o mesmo IDP durante um login subseqüente, ele será reautenticado sem inserir suas credenciais. Se um usuário quiser sair do aplicativo, isso não significa necessariamente que ele quer sair de sua conta do Facebook. No entanto, se as contas locais forem usadas, a sessão do usuário termina corretamente.
