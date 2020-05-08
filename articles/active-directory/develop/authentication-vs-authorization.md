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
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584292"
---
# <a name="authentication-vs-authorization"></a>Autenticação vs. autorização

Este artigo define a autenticação e a autorização e aborda brevemente como você pode usar a plataforma de identidade da Microsoft para autenticar e autorizar usuários em seus aplicativos Web, APIs Web ou aplicativos que chamam APIs da Web protegidas. Se você vir um termo com o qual não está familiarizado, experimente nosso [Glossário](developer-glossary.md) ou nossos [vídeos da plataforma de identidade da Microsoft](identity-videos.md) que abordam os conceitos básicos.

## <a name="authentication"></a>Autenticação

A **autenticação** é o processo de provar que você é quem diz que está. Às vezes, a autenticação é abreviada para AuthN. A plataforma de identidade da Microsoft implementa o protocolo [OpenID Connect](https://openid.net/connect/) para lidar com a autenticação.

## <a name="authorization"></a>Autorização

A **autorização** é o ato de conceder uma permissão de parte autenticada para fazer algo. Ele especifica quais dados você tem permissão para acessar e o que você pode fazer com esses dados. Às vezes, a autorização é abreviada para AuthZ. A plataforma de identidade da Microsoft implementa o protocolo [OAuth 2,0](https://oauth.net/2/) para lidar com a autorização.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Autenticação e autorização usando a plataforma de identidade da Microsoft

Em vez de criar aplicativos que mantêm suas próprias informações de nome de usuário e senha, o que provoca uma alta carga administrativa quando você precisa adicionar ou remover usuários em vários aplicativos, os aplicativos podem delegar essa responsabilidade a um provedor de identidade centralizado.

O Azure Active Directory (AD do Azure) é um provedor de identidade centralizado na nuvem. A delegação de autenticação e autorização para ela permite cenários como políticas de acesso condicional que exigem que um usuário esteja em um local específico, o uso da autenticação multifator, bem como a habilitação de um usuário a entrar uma vez e, em seguida, ser automaticamente conectado a todos os aplicativos Web que compartilham o mesmo diretório centralizado. Esse recurso é conhecido como **SSO (logon único)**.

A plataforma de identidade da Microsoft simplifica a autenticação e a autorização para desenvolvedores de aplicativos, fornecendo identidade como um serviço, com suporte para protocolos padrão do setor, como OAuth 2,0 e OpenID Connect, bem como bibliotecas de software livre para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Ele permite que os desenvolvedores criem aplicativos que se conectam a todas as identidades da Microsoft, obtenham tokens para chamar [Microsoft Graph](https://developer.microsoft.com/graph/), outras APIs da Microsoft ou APIs que os desenvolvedores criaram. Para obter mais informações, consulte [evolução da plataforma de identidade da Microsoft](about-microsoft-identity-platform.md).

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos que abrangem noções básicas de autenticação e autorização:

* Consulte [tokens de segurança](security-tokens.md) para saber como tokens de acesso, tokens de atualização e tokens de ID são usados em autenticação e autorização.
* Consulte [modelo de aplicativo](application-model.md) para saber mais sobre o processo de registro de seu aplicativo para que ele possa ser integrado à plataforma de identidade da Microsoft.
* Consulte [fluxo de entrada do aplicativo](app-sign-in-flow.md) para saber mais sobre o fluxo de entrada de aplicativos Web, de área de trabalho e móveis na plataforma Microsoft Identity.

Para saber mais sobre os protocolos que a plataforma de identidade da Microsoft implementa:

* Consulte [protocolos oauth 2,0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md) para obter mais informações sobre os padrões do OpenID Connect e do OAuth 2,0.
* Consulte [protocolo SAML de logon único](single-sign-on-saml-protocol.md) para obter mais informações sobre como a plataforma de identidade da Microsoft dá suporte ao logon único.
