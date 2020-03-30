---
title: Adicione autenticação aos seus aplicativos móveis com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o Visual Studio App Center que ajudam a configurar a autenticação do usuário e permitem que os aplicativos móveis se autentiquem com contas sociais, diretório ativo do Azure e autenticação personalizada.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241044"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Adicione autenticação e gerencie identidades de usuário em seus aplicativos móveis

Ter uma visão do usuário e seu comportamento em todo o seu aplicativo capacita os desenvolvedores a engajar melhor os usuários, criando experiências personalizadas para eles. Se você é um desenvolvedor de aplicativos que está construindo um aplicativo de colaboração para usuários dentro de sua organização ou está criando a próxima plataforma de rede social, você precisa de uma maneira de autenticar usuários e gerenciar identidades de usuários. Um serviço de gerenciamento de identidade é uma das características mais importantes de um serviço de back-end móvel.

Use os seguintes serviços para permitir a autenticação do usuário em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Auth](/appcenter/auth/) é um serviço de gerenciamento de identidade baseado em nuvem que os desenvolvedores podem usar para autenticar usuários e gerenciar identidades de usuários. App Center Auth também se integra com outras partes do Visual Studio App Center. Os desenvolvedores podem usar a identidade do usuário para [visualizar dados do usuário](/appcenter/data/index) em outros serviços e até mesmo enviar [notificações push aos usuários em vez de dispositivos individuais.](/appcenter/push/push-to-user#setting-user-identity) 

**Principais características**
- Alimentado por Azure Active Directory B2C (Azure AD B2C). 
    - Grau empresarial.
    - Altamente disponível.
    - Serviço seguro e global.
- Traga sua própria identidade e a opção de usar outros provedores de gerenciamento de identidade e acesso populares, como Auth0 e Firebase.
- Suporte ao Azure Active Directory.
    - Conecte os inquilinos Azure AD existentes. 
    - Habilite a autenticação contra um domínio corporativo.
    - Gerencie o acesso a dados confidenciais.
- Experiência simples do usuário e experiência mágica de SDK envolvendo a Biblioteca de Autenticação da Microsoft com o Visual Studio App Center SDK.
- Suporte à plataforma para iOS, Android, Xamarin e React Native.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Comece com o App Center Auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[O Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade de negócios para consumidores (B2C) que os desenvolvedores podem usar para autenticar seus clientes. Este serviço de white-label permite que os desenvolvedores personalizem e controlem como os usuários interagem com segurança com seus aplicativos web, desktop, mobile ou de uma página única. Usando o Azure AD B2C, os usuários podem se inscrever, entrar, redefinir senhas e editar perfis. O Azure AD B2C implementa um formulário dos protocolos OpenID Connect e OAuth 2.0. 

**Principais características**
- Autenticar com segurança os clientes com seu provedor de identidade preferido.
- Gerencie a identidade e o acesso do cliente.
- Ganhe suporte de login para mídias sociais como Facebook, GitHub, Google, LinkedIn, Twitter, WeChat e Weibo.
- Conecte-se às suas contas de usuário usando protocolos padrão do setor, como OpenID Connect ou SAML, para tornar o gerenciamento de identidade possível em uma variedade de plataformas.
- Fornecer experiências de registro e login de marca.
- Integre facilmente com bancos de dados de CRM, ferramentas de análise de marketing e sistemas de verificação de contas.
- Capture dados de login, preferência e conversão para clientes.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Documentação Azure AD B2C](/azure/active-directory-b2c/)
- [Inícios rápidos](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Amostras](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[O Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft, que ajuda seus funcionários a fazer login e obter acesso a:
- Recursos externos, como o Microsoft Office 365, o portal Azure, e milhares de outros aplicativos de software como serviço (SaaS).
- Recursos internos, como aplicativos em sua rede corporativa e intranet, juntamente com outros aplicativos de nuvem desenvolvidos por sua organização.

**Principais características**
- Acesso perfeito e altamente seguro conectando os usuários aos aplicativos de que precisam.
- Proteção abrangente de identidade e segurança aprimorada para identidades e acesso com base no contexto do usuário, localização, dispositivo, dados e aplicativos.
- Milhares de aplicativos pré-integrados para aplicativos comerciais e personalizados, como Office 365, Salesforce.com e Box.
- Capacidade de gerenciar o acesso em escala.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [O que é o AD do Azure?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Introdução ao Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [Inícios rápidos](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)