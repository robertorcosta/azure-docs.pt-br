---
title: Tokens de segurança | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os conceitos básicos dos tokens de segurança na plataforma Microsoft Identity (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 88dc4bb86459cd0390c4c01deb871aa93e39c6d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84266739"
---
# <a name="security-tokens"></a>Tokens de segurança

Um provedor de identidade centralizado é especialmente útil para aplicativos que têm usuários localizados em todo o mundo que não necessariamente se conectam da rede da empresa. A plataforma de identidade da Microsoft autentica os usuários e fornece tokens de segurança, como [token de acesso](developer-glossary.md#access-token), [token de atualização](developer-glossary.md#refresh-token)e [token de ID](developer-glossary.md#id-token), que permitem que um [aplicativo cliente](developer-glossary.md#client-application) acesse recursos protegidos em um [servidor de recursos](developer-glossary.md#resource-server).

Um **token de acesso** é um token de segurança que é emitido por um [servidor de autorização](developer-glossary.md#authorization-server) como parte de um fluxo do [OAuth 2,0](active-directory-v2-protocols.md) . Ele contém informações sobre o usuário e o aplicativo para o qual o token se destina; que pode ser usado para acessar APIs da Web e outros recursos protegidos. Para saber mais sobre como a plataforma de identidade da Microsoft emite tokens de acesso, consulte [tokens de acesso](access-tokens.md).

Os tokens de acesso só são válidos por um curto período de tempo, portanto, os servidores de autorização, às vezes, emitirão um **token de atualização** ao mesmo tempo em que o token de acesso é emitido. O aplicativo cliente pode então trocar esse token de atualização para um novo token de acesso quando necessário. Para saber mais sobre como a plataforma de identidade da Microsoft usa tokens de atualização para revogar permissões, consulte [revogação de token](access-tokens.md#token-revocation).

Os **tokens de ID** são enviados ao aplicativo cliente como parte de um fluxo do [OpenID Connect](v2-protocols-oidc.md) . Eles podem ser enviados com um token de acesso ou em vez de um, e são usados pelo cliente para autenticar o usuário. Para saber mais sobre como a plataforma de identidade da Microsoft emite tokens de ID, consulte [tokens de ID](id-tokens.md).

> [!NOTE]
> Este artigo discute os tokens de segurança usados pelos protocolos OAuth2 e OpenID Connect. Muitos aplicativos empresariais usam SAML para autenticar usuários. Consulte [referência de token SAML do Azure ad](reference-saml-tokens.md) para obter informações sobre asserções SAML.

## <a name="validating-security-tokens"></a>Validando tokens de segurança

Cabe ao aplicativo para o qual o token foi gerado, o aplicativo Web que conectou o usuário ou a API Web que está sendo chamada, para validar o token. O token é assinado pelo **servidor de token de segurança (STS)** com uma chave privada. O STS publica a chave pública correspondente. Para validar um token, o aplicativo verifica a assinatura usando a chave pública do STS para validar que a assinatura foi criada usando a chave privada.

Os tokens são válidos somente por uma quantidade limitada de tempo. Normalmente, o STS fornece um par de tokens:

* Um token de acesso para acessar o aplicativo ou recurso protegido e
* Um token de atualização usado para atualizar o token de acesso quando o token de acesso está perto de expirar.

Tokens de acesso são passados para uma API da Web como o token de portador no `Authorization` cabeçalho. Um aplicativo pode fornecer um token de atualização para o STS e, se o acesso do usuário ao aplicativo não tiver sido revogado, ele obterá um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém que sai da empresa é tratado. Quando o STS receber o token de atualização, ele não emitirá outro token de acesso válido se o usuário não estiver mais autorizado.

## <a name="json-web-tokens-jwts-and-claims"></a>JWTs (tokens Web JSON) e declarações

A plataforma de identidade da Microsoft implementa tokens de segurança como **JWTs (tokens Web JSON)** que contêm **declarações**. Como JWTs são usados como tokens de segurança, essa forma de autenticação, às vezes, é chamada de **autenticação JWT**.

Uma [declaração](developer-glossary.md#claim) fornece asserções sobre uma entidade, como um aplicativo cliente ou [proprietário do recurso](developer-glossary.md#resource-owner), para outra entidade, como um servidor de recursos. Uma declaração também pode ser referida como uma declaração JWT ou uma declaração de token Web JSON.

As declarações são pares de nome/valor que retransmitem fatos sobre o assunto do token. Por exemplo, uma declaração pode conter fatos sobre a entidade de segurança que foi autenticada pelo servidor de autorização. As declarações presentes em um determinado token dependem de muitas coisas, incluindo o tipo de token, o tipo de credencial usado para autenticar o assunto, a configuração do aplicativo e assim por diante.

Os aplicativos podem usar declarações para várias tarefas, como:

* Validando o token
* Identificando o [locatário](developer-glossary.md#tenant) da entidade do token
* Exibindo informações do usuário
* Determinando a autorização da entidade

Uma declaração consiste em pares chave-valor que fornecem informações como:

* Servidor de token de segurança que gerou o token
* Data em que o token foi gerado
* Assunto (como o usuário – exceto para daemons)
* Público, que é o aplicativo para o qual o token foi gerado
* Aplicativo (o cliente) que solicitou o token. No caso de aplicativos Web, isso pode ser o mesmo que o público-alvo

Para saber mais sobre como a plataforma de identidade da Microsoft implementa tokens e informações de declaração, consulte [tokens de acesso](access-tokens.md) e [tokens de ID](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite tokens e códigos

Dependendo de como o cliente é criado, ele pode usar um (ou vários) dos fluxos de autenticação com suporte pela plataforma de identidade da Microsoft. Esses fluxos podem produzir uma variedade de tokens (tokens de ID, tokens de atualização, tokens de acesso), bem como códigos de autorização, e exigem tokens diferentes para fazê-los funcionar. Veja uma visão geral neste gráfico:

|Flow | Exige | token de ID | o token de acesso | token de atualização | código de autorização |
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo do código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo de OIDC híbrido](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Resgate de token de atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token de atualização | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | o token de acesso| x| x| x| |
|[Credenciais do cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (somente de aplicativo)| | |

Tokens emitidos no modo implícito têm uma limitação de comprimento, porque são passados de volta para o navegador por meio da URL (em que `response_mode` é `query` ou `fragment`).  Alguns navegadores têm um limite de tamanho da URL que pode ser digitada na barra do navegador e falham quando ela é muito longa.  Portanto, esses tokens não têm declarações de `groups` ou `wids`.

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos de noções básicas sobre autenticação e autorização:

* Confira [Autenticação versus autorização](authentication-vs-authorization.md) para saber mais sobre os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft.
* Confira [Modelo de aplicativo](application-model.md) para saber mais sobre o processo de registro de seu aplicativo para que ele possa se integrar à plataforma de identidade da Microsoft.
* Confira [Fluxo de entrada de aplicativos](app-sign-in-flow.md) para saber mais sobre o fluxo de entrada de aplicativos da Web, de desktop e móveis na plataforma de identidade da Microsoft.
