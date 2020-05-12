---
title: Autenticação vs. autorização | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os conceitos básicos de autenticação e autorização na plataforma Microsoft Identity (v 2.0).
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
ms.openlocfilehash: fca794d51e06c72f157dc063445d1cab09d92d28
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115856"
---
# <a name="authentication-vs-authorization"></a>Autenticação vs. autorização

Este artigo define a autenticação e a autorização e aborda brevemente como você pode usar a plataforma de identidade da Microsoft para autenticar e autorizar usuários em seus aplicativos Web, APIs Web ou aplicativos que chamam APIs da Web protegidas. Se você vir um termo com o qual não está familiarizado, experimente nosso [Glossário](developer-glossary.md) ou nossos [vídeos da plataforma de identidade da Microsoft](identity-videos.md) que abordam os conceitos básicos.

## <a name="authentication"></a>Autenticação

A **autenticação** é o processo de provar que você é quem diz que está. Às vezes, a autenticação é abreviada para AuthN. A plataforma de identidade da Microsoft implementa o protocolo [OpenID Connect](https://openid.net/connect/) para lidar com a autenticação.

## <a name="authorization"></a>Autorização

A **autorização** é o ato de conceder uma permissão de parte autenticada para fazer algo. Ele especifica quais dados você tem permissão para acessar e o que você pode fazer com esses dados. Às vezes, a autorização é abreviada para AuthZ. A plataforma de identidade da Microsoft implementa o protocolo [OAuth 2,0](https://oauth.net/2/) para lidar com a autorização.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Autenticação e autorização usando a plataforma de identidade da Microsoft

Em vez de criar aplicativos que mantêm suas próprias informações de nome de usuário e senha, o que provoca uma alta carga administrativa quando você precisa adicionar ou remover usuários em vários aplicativos, os aplicativos podem delegar essa responsabilidade a um provedor de identidade centralizado.

O Azure Active Directory (AD do Azure) é um provedor de identidade centralizado na nuvem. A delegação de autenticação e autorização para ela permite cenários como políticas de acesso condicional que exigem que um usuário esteja em um local específico, o uso da autenticação multifator, bem como a habilitação de um usuário a entrar uma vez e, em seguida, ser automaticamente conectado a todos os aplicativos Web que compartilham o mesmo diretório centralizado. Esse recurso é conhecido como **SSO (logon único)**.

A plataforma de identidade da Microsoft simplifica a autenticação e a autorização para desenvolvedores de aplicativos, fornecendo identidade como um serviço, com suporte para protocolos padrão do setor, como OAuth 2,0 e OpenID Connect, bem como bibliotecas de software livre para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Ele permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft, obtenham tokens para chamar [Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores criaram. Para obter mais informações, consulte [evolução da plataforma de identidade da Microsoft](about-microsoft-identity-platform.md).

Veja a seguir uma breve comparação dos vários protocolos usados pela plataforma de identidade da Microsoft:

* **OAuth vs. OpenID Connect**: o OAuth é usado para autorização e o OpenID Connect (OIDC) é usado para autenticação. O OpenID Connect é criado sobre o OAuth 2,0, portanto, a terminologia e o fluxo são semelhantes entre os dois. Você pode até mesmo autenticar um usuário (usando o OpenID Connect) e obter autorização para acessar um recurso protegido que o usuário possui (usando OAuth 2,0) em uma solicitação. Para obter mais informações, consulte [protocolos OAuth 2,0 e OpenID Connect](active-directory-v2-protocols.md) e [protocolo OpenID Connect](v2-protocols-oidc.md).
* O **OAuth vs. SAML**: OAuth é usado para autorização e SAML é usado para autenticação. Consulte [plataforma de identidade da Microsoft e fluxo de asserção do OAuth 2,0 SAML portador](v2-saml-bearer-assertion.md) para obter mais informações sobre como os dois protocolos podem ser usados juntos para autenticar um usuário (usando SAML) e obter autorização para acessar um recurso protegido (usando OAuth 2,0).
* **OpenID Connect vs. SAML**: o OpenID Connect e o SAML são usados para autenticar um usuário e são usados para habilitar o logon único. A autenticação SAML é comumente usada com provedores de identidade, como o Serviços de Federação do Active Directory (AD FS) (ADFS) federado para o Azure AD e, portanto, é usada frequentemente em aplicativos empresariais. O OpenID Connect é normalmente usado para aplicativos que são puramente na nuvem, como aplicativos móveis, sites e APIs da Web.

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos que abrangem noções básicas de autenticação e autorização:

* Consulte [tokens de segurança](security-tokens.md) para saber como tokens de acesso, tokens de atualização e tokens de ID são usados em autenticação e autorização.
* Consulte [modelo de aplicativo](application-model.md) para saber mais sobre o processo de registro de seu aplicativo para que ele possa ser integrado à plataforma de identidade da Microsoft.
* Consulte [fluxo de entrada do aplicativo](app-sign-in-flow.md) para saber mais sobre o fluxo de entrada de aplicativos Web, de área de trabalho e móveis na plataforma Microsoft Identity.

Para saber mais sobre os protocolos que a plataforma de identidade da Microsoft implementa:

* Consulte [protocolos oauth 2,0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md) para obter mais informações sobre os padrões do OpenID Connect e do OAuth 2,0.
* Consulte [protocolo SAML de logon único](single-sign-on-saml-protocol.md) para obter mais informações sobre como a plataforma de identidade da Microsoft dá suporte ao logon único.
