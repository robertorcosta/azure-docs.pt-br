---
title: Fluxo de código de autorização-Azure Active Directory B2C | Microsoft Docs
description: Saiba como criar aplicativos Web usando o Azure AD B2C e o protocolo de Autenticação OpenID Connect.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 1ae3f739d5104ea9a98889f7fbce938b835e84aa
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385935"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Fluxo de código de autorização do OAuth 2,0 em Azure Active Directory B2C

Você pode usar a concessão de código de autorização OAuth 2.0 em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Usando a implementação do Azure AD B2C (Azure Active Directory B2C) do OAuth 2.0, você pode adicionar tarefas de inscrição, entrada e outras de gerenciamento de identidade aos seus aplicativos móveis e da área de trabalho. Este artigo é independente de linguagem. No artigo, descreveremos como enviar e receber mensagens HTTP sem usar nenhuma biblioteca de software livre.

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749). Você pode usá-lo para autenticação e autorização na maioria dos [tipos de aplicativos](active-directory-b2c-apps.md), incluindo aplicativos Web e aplicativos instalados nativamente. Você pode usar o fluxo de código de autorização do OAuth 2,0 para adquirir tokens de acesso e atualizar tokens com segurança para seus aplicativos, que podem ser usados para acessar recursos que são protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md).  O token de atualização permite que o cliente adquira novos tokens de acesso (e atualização) quando o token de acesso expirar, normalmente após uma hora.

Este artigo concentra-se no fluxo de código de autorização do OAuth 2.0 de **clientes públicos**. Um cliente público é qualquer aplicativo cliente que não é confiável para manter a integridade de uma senha secreta com segurança. Isso inclui aplicativos móveis, aplicativos de área de trabalho e, essencialmente, qualquer aplicativo executado em um dispositivo e precisa obter tokens de acesso.

> [!NOTE]
> Para adicionar o gerenciamento de identidade em um aplicativo Web usando o Azure AD B2C, use o [OpenID Connect](active-directory-b2c-reference-oidc.md) em vez do OAuth 2.0.

O Azure AD B2C estende os fluxos padrão do OAuth 2.0 para fazer mais do que simples ações de autenticação e autorização. Ele apresenta o [fluxo do usuário](active-directory-b2c-reference-policies.md). Com fluxos de usuário, você pode usar o OAuth 2,0 para adicionar experiências de usuário ao seu aplicativo, como inscrição, entrada e gerenciamento de perfil. Os provedores de identidade que usam o protocolo OAuth 2,0 incluem [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md)e [LinkedIn](active-directory-b2c-setup-li-app.md).

Para experimentar as solicitações HTTP neste artigo:

1. Substitua `{tenant}` pelo nome do seu locatário Azure AD B2C.
1. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` pela ID do aplicativo de um aplicativo que você registrou anteriormente em seu locatário Azure AD B2C.
1. Substitua `{policy}` pelo nome de uma política que você criou em seu locatário, por exemplo `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. obter um código de autorização
O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Essa é a parte interativa do fluxo, na qual o usuário entra em ação. Nessa solicitação, o cliente indica no parâmetro `scope` as permissões que ele precisa adquirir do usuário. Os três exemplos a seguir (com quebras de linha para facilitar a leitura) usam um fluxo de usuário diferente.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| . | Obrigatório? | Descrição |
| --- | --- | --- |
|vários| Obrigatório | Nome do seu locatário de Azure AD B2C|
| regras | Obrigatório | O fluxo do usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou em seu locatário Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up` ou `b2c_1_edit_profile`. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| response_type |Obrigatório |O tipo de resposta, que deve incluir `code` para o fluxo do código de autorização. |
| redirect_uri |Obrigatório |O URI de redirecionamento do seu aplicativo, no qual as respostas de autenticação são enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento registrados no portal, exceto que ele deve ser codificado como URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD (Azure Active Directory) que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente.  O escopo `offline_access` indica que seu aplicativo precisa de um token de atualização para acesso de longa duração a recursos. Você também pode usar o escopo `openid` para solicitar um token de ID do Azure AD B2C. |
| response_mode |Recomendado |O método que você usa para enviar o código de autorização resultante para seu aplicativo. Pode ser `query`, `form_post` ou `fragment`. |
| Status |Recomendado |Um valor incluído na solicitação que pode ser uma cadeia de caracteres de qualquer conteúdo que você deseja usar. Geralmente, um valor exclusivo gerado aleatoriamente é usado para evitar ataques de solicitação intersite forjada. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo, antes que a solicitação de autenticação tenha ocorrido. Por exemplo, a página em que o usuário estava ou o fluxo do usuário que estava sendo executado. |
| prompt |Opcional |O tipo de interação do usuário que é necessário. Atualmente, o único valor válido é `login`, que força o usuário a inserir suas credenciais nessa solicitação. O logon único não terá efeito. |

Neste ponto, o usuário será solicitado a concluir o fluxo de trabalho do fluxo do usuário. Isso pode exigir que o usuário insira seu nome de usuário e senha, entre com uma identidade social, inscreva-se no diretório ou realize outras etapas. As ações do usuário dependem de como o fluxo do usuário é definido.

Depois que o usuário concluir o fluxo do usuário, o Azure AD retornará uma resposta ao seu aplicativo no valor usado para `redirect_uri`. Ele usa o método especificado no parâmetro `response_mode`. A resposta é exatamente a mesma para cada um dos cenários de ação do usuário, independentemente do fluxo do usuário que foi executado.

Uma resposta bem-sucedida que usa `response_mode=query` tem esta aparência:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| . | Descrição |
| --- | --- |
| Auto-completar |O código de autorização solicitado pelo aplicativo. O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Os códigos de autorização têm uma duração muito curta. Normalmente, eles expiram depois de cerca de 10 minutos. |
| Status |Consulte a descrição completa na tabela na seção anterior. Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas ao URI de redirecionamento, de modo que o aplicativo possa tratá-las adequadamente:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |
| Status |Consulte a descrição completa na tabela anterior. Se um parâmetro `state` estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |

## <a name="2-get-a-token"></a>2. obter um token
Agora que você adquiriu um código de autorização, será possível resgatar o `code` de um token para o recurso desejado enviando uma solicitação POST para o ponto de extremidade `/token`. No Azure AD B2C, você pode [solicitar tokens de acesso para outras APIs](active-directory-b2c-access-tokens.md#request-a-token) como de costume, especificando seus escopos na solicitação.

Você também pode solicitar um token de acesso para a própria API Web de back-end do seu aplicativo por convenção de usar a ID do cliente do aplicativo como o escopo solicitado (o que resultará em um token de acesso com essa ID de cliente como o "público"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| . | Obrigatório? | Descrição |
| --- | --- | --- |
|vários| Obrigatório | Nome do seu locatário de Azure AD B2C|
|regras| Obrigatório| O fluxo do usuário que foi usado para adquirir o código de autorização. Você não pode usar um fluxo de usuário diferente nesta solicitação. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com).|
| grant_type |Obrigatório |O tipo de concessão. Para o fluxo de código de autorização, o tipo de concessão deve ser `authorization_code`. |
| scope |Recomendado |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente.  O escopo `offline_access` indica que seu aplicativo precisa de um token de atualização para acesso de longa duração a recursos.  Você também pode usar o escopo `openid` para solicitar um token de ID do Azure AD B2C. |
| Auto-completar |Obrigatório |O código de autorização que você adquiriu no primeiro segmento do fluxo. |
| redirect_uri |Obrigatório |O URI de redirecionamento do aplicativo em que você recebeu o código de autorização. |

Uma resposta de token bem-sucedido tem esta aparência:

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
| . | Descrição |
| --- | --- |
| not_before |A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| access_token |O JWT (Token Web JSON) assinado que você solicitou. |
| scope |Os escopos para os quais o token é válido. Você também pode usar os escopos para armazenar tokens em cache para uso posterior. |
| expires_in |O período de tempo pelo qual o token é válido (em segundos). |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os tokens de atualização têm uma vida longa. Você pode usá-los para manter o acesso aos recursos por longos períodos de tempo. Para obter mais informações, consulte a [Referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro tem esta aparência:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

## <a name="3-use-the-token"></a>3. usar o token
Agora que adquiriu um token de acesso com êxito, você poderá usá-lo em solicitações às suas APIs Web de back-end incluindo-o no cabeçalho `Authorization`:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. atualizar o token
Os tokens de acesso e os tokens de ID tem curta duração. Depois que eles expirarem, você deverá atualizá-los para continuar a acessar recursos. Para fazer isso, envie outra solicitação POST para o ponto de extremidade `/token`. Dessa vez, forneça o `refresh_token` em vez do `code`:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| . | Obrigatório? | Descrição |
| --- | --- | --- |
|vários| Obrigatório | Nome do seu locatário de Azure AD B2C|
|regras |Obrigatório |O fluxo do usuário que foi usado para adquirir o token de atualização original. Você não pode usar um fluxo de usuário diferente nesta solicitação. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| client_secret |Obrigatório |O client_secret associado ao seu client_id no [portal do Azure](https://portal.azure.com). |
| grant_type |Obrigatório |O tipo de concessão. Para este segmento do fluxo de código de autorização, o tipo de concessão deve ser `refresh_token`. |
| scope |Recomendado |Uma lista de escopos separados por espaços. Um valor de escopo único indica ao Azure AD que ambas as permissões estão sendo solicitadas. O uso da ID do cliente como o escopo indica que seu aplicativo precisa de um token de acesso que possa ser usado em relação a seu próprio serviço ou API Web, representado pela mesma ID de cliente.  O escopo `offline_access` indica que o aplicativo precisará de um token de atualização para acessar os recursos de longa duração.  Você também pode usar o escopo `openid` para solicitar um token de ID do Azure AD B2C. |
| redirect_uri |Opcional |O URI de redirecionamento do aplicativo em que você recebeu o código de autorização. |
| refresh_token |Obrigatório |O token de atualização original que você adquiriu na segunda ramificação do fluxo. |

Uma resposta de token bem-sucedido tem esta aparência:

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
| . | Descrição |
| --- | --- |
| not_before |A hora em que o token é considerado válido, nos valores de hora da época. |
| token_type |O valor do tipo de token. O único tipo com suporte do Azure AD é Portador |
| access_token |O JWT assinado que você solicitou. |
| scope |Os escopos para os quais o token é válido. Você também pode usar os escopos para armazenar tokens em cache para uso posterior. |
| expires_in |O período de tempo pelo qual o token é válido (em segundos). |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o atual expirar. Os tokens de atualização têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais informações, consulte a [Referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |

As respostas de erro tem esta aparência:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de código de erro que você pode usar para classificar os tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Use seu próprio diretório do Azure AD B2C
Para testar essas solicitações, conclua as etapas a seguir. Substitua os valores de exemplo que usamos neste artigo pelos seus próprios valores.

1. [Criar um diretório do Azure AD B2C](active-directory-b2c-get-started.md). Use o nome do seu diretório nas solicitações.
2. [Criar um aplicativo](active-directory-b2c-app-registration.md) para obter uma ID de aplicativo e um URI de redirecionamento. Inclua um cliente nativo em seu aplicativo.
3. [Crie seus fluxos de usuário](active-directory-b2c-reference-policies.md) para obter seus nomes de fluxo de usuário.
