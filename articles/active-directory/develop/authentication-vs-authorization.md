---
title: Autenticação versus autorização | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os conceitos básicos de autenticação e autorização na plataforma de identidade da Microsoft (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 095bc5594cf9b6eaf0df7929ea83c25a401793e2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252106"
---
# <a name="authentication-vs-authorization"></a>Autenticação versus autorização

Este artigo define autenticação e autorização e aborda brevemente como você pode usar a plataforma de identidade da Microsoft para autenticar e autorizar usuários em seus aplicativos Web, APIs Web ou aplicativos que chamam APIs Web seguras. Se você vir um termo com o qual não está familiarizado, confira nosso [glossário](developer-glossary.md) ou nossos [vídeos da plataforma de identidade](identity-videos.md) da Microsoft que abrangem conceitos básicos.

## <a name="authentication"></a>Autenticação

**Autenticação** é o processo de provar que você é quem diz ser. Às vezes, a autenticação é abreviada para AuthN. A plataforma de identidade da Microsoft implementa o protocolo [OpenID Connect](https://openid.net/connect/) para lidar com a autenticação.

## <a name="authorization"></a>Autorização

**Autorização** é o ato de conceder a uma parte autenticada a permissão para fazer algo. Ele especifica quais dados você tem permissão para acessar e o que pode fazer com esses dados. Às vezes, a autorização é abreviada para AuthZ. A plataforma de identidade da Microsoft implementa o protocolo [OAuth 2.0](https://oauth.net/2/) para lidar com a autorização.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Autenticação e autorização usando a plataforma de identidade da Microsoft

Em vez de criar aplicativos que mantêm as próprias informações de nome de usuário e senha, o que gera uma carga administrativa alta quando você precisa adicionar ou remover usuários em vários aplicativos, os aplicativos podem delegar essa responsabilidade a um provedor de identidade centralizado.

O Azure AD (Azure Active Directory) é um provedor de identidade centralizado na nuvem. Delegar autenticação e autorização a ele permite cenários como políticas de acesso condicional que exigem que um usuário esteja em um local específico, o uso de [autenticação multifatorial](../authentication/concept-mfa-howitworks.md) (às vezes referida como autenticação de dois fatores ou 2FA), além de habilitar um usuário para entrar uma vez e, em seguida, entrar automaticamente em todos os aplicativos da web que compartilham o mesmo diretório centralizado. Essa funcionalidade é conhecida como **SSO (logon único)** .

A plataforma de identidade da Microsoft simplifica a autorização e a autenticação para os desenvolvedores de aplicativos, fornecendo a identidade como serviço, com suporte para protocolos padrão do setor, como OAuth 2.0 e OpenID Connect, bem como bibliotecas de software livre para diferentes plataformas para ajudar você a começar a codificação rapidamente. Ela permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft e obtenham tokens para chamar o [Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores criaram.

A seguir, é apresentada uma breve comparação dos vários protocolos usados pela plataforma de identidade da Microsoft:

* **OAuth versus OpenID Connect**: O OAuth é usado para autorização, e o OIDC (OpenID Connect) é usado para autenticação. O OpenID Connect é desenvolvido com base no OAuth 2.0, portanto, a terminologia e o fluxo são semelhantes entre os dois. Você pode até mesmo autenticar um usuário (usando o OpenID Connect) e obter autorização para acessar um recurso protegido que o usuário possui (usando o OAuth 2.0) em uma solicitação. Para saber mais, confira [Protocolos OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) e [Protocolo OpenID Connect](v2-protocols-oidc.md).
* **OAuth versus SAML**: O OAuth é usado para autorização, e o SAML é usado para autenticação. Confira [Plataforma de identidade da Microsoft e Fluxo de declaração de portador SAML do OAuth 2.0](v2-saml-bearer-assertion.md) para obter mais informações sobre como os dois protocolos podem ser usados juntos para autenticar um usuário (usando SAML) e obter autorização para acessar um recurso protegido (usando OAuth 2.0).
* **OpenID Connect versus SAML**: O OpenID Connect e o SAML são usados para autenticar um usuário e para ativar o logon único. A autenticação SAML é comumente usada com provedores de identidade, como os Serviços de Federação do Active Directory (AD FS), federados ao Azure AD e, portanto, é usada com frequência em aplicativos corporativos. O OpenID Connect é comumente usado para aplicativos puramente na nuvem, como aplicativos móveis, sites e APIs Web.

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos sobre autenticação e autorização:

* Confira [Tokens de segurança](security-tokens.md) para saber como os tokens de acesso, tokens de atualização e tokens de ID são usados na autorização e autenticação.
* Confira [Modelo de aplicativo](application-model.md) para saber mais sobre o processo de registro de seu aplicativo para que ele possa se integrar à plataforma de identidade da Microsoft.
* Confira [Fluxo de entrada de aplicativos](app-sign-in-flow.md) para saber mais sobre o fluxo de entrada de aplicativos da Web, de desktop e móveis na plataforma de identidade da Microsoft.

* Para saber mais sobre os protocolos implementados pela plataforma de identidade da Microsoft, confira [Protocolos OAuth 2.0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md).
* Confira [Protocolo SAML de Logon Único](single-sign-on-saml-protocol.md) para obter mais informações sobre como a plataforma de identidade da Microsoft oferece suporte ao logon único.
* Confira [Logon único para aplicativos no Azure Active Directory](../manage-apps/what-is-single-sign-on.md) para obter mais informações sobre as diferentes maneiras de implementar o logon único no seu aplicativo.
