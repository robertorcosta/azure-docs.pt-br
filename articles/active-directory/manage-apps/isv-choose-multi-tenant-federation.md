---
title: Escolha o protocolo de federação certo para aplicação de vários inquilinos
description: Orientação para fornecedores independentes de software sobre a integração com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443385"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Escolha o protocolo de federação certo para o seu aplicativo multi-inquilino

Ao desenvolver seu aplicativo de software como serviço (SaaS), você deve selecionar o protocolo de federação que melhor atende às necessidades de seus clientes. Essa decisão é baseada em sua plataforma de desenvolvimento e no seu desejo de se integrar com os dados disponíveis no ecossistema Office 365 e Azure AD de seus clientes.

Veja a lista completa de [protocolos disponíveis para integrações sso](what-is-single-sign-on.md) com o Azure Active Directory.
A tabela a seguir compara 
* Autenticação aberta 2.0 (OAuth 2.0)
* Conectar id aberto (OIDC)
* SAML (Security Assertion Markup Language)
* Federação de Serviços Web (WSFed)

| Recurso| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Login único baseado na Web| √| √ |
| Outa única baseada na Web| √| √ |
| Adesão única baseada em dispositivos móveis| √| √* |
| Saída única baseada em dispositivos móveis| √| √* |
| Políticas de Acesso Condicional para aplicativos móveis| √| X |
| Experiência MFA perfeita para aplicações móveis| √| X |
| Acesse o Microsoft Graph| √| X |

*Possível, mas a Microsoft não fornece amostras ou orientações.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 e Open ID Connect

OAuth 2.0 é um protocolo [padrão do setor](https://oauth.net/2/) para autorização. OIDC (OpenID Connect) é uma camada de autenticação de identidade padrão do [setor](https://openid.net/connect/) construída em cima do protocolo OAuth 2.0.

### <a name="benefits"></a>Benefícios

A Microsoft recomenda o uso do OIDC/OAuth 2.0, pois eles têm autenticação e autorização incorporadas aos protocolos. Com o SAML, você deve implementar adicionalmente a autorização.

A autorização inerente a esses protocolos permite que seu aplicativo acesse e se integre com dados ricos de usuários e organizações através da API do Microsoft Graph.

O uso do OAuth 2.0 e do OIDC simplifica a experiência do usuário final de seus clientes ao adotar o SSO para o seu aplicativo. Você pode definir facilmente os conjuntos de permissões necessários, que são então representados automaticamente ao administrador ou ao consentimento do usuário final.

Além disso, o uso desses protocolos permite que seus clientes usem políticas de Acesso Condicional e MFA para controlar o acesso aos aplicativos. A Microsoft fornece bibliotecas e [amostras de código em várias plataformas tecnológicas](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para ajudar no seu desenvolvimento.  

### <a name="implementation"></a>Implementação

Você registra seu aplicativo no Microsoft Identity, que é um provedor OAuth 2.0. Você também pode registrar seu aplicativo baseado no OAuth 2.0 com qualquer outro Provedor de Identidade com o que deseja integrar. 

Para obter informações sobre como registrar seu aplicativo e implementar esses protocolos para SSO para aplicativos web, consulte [Autorizar o acesso a aplicativos web usando o OpenID Connect e o Azure Active Directory](../develop/sample-v2-code.md).  Para obter informações sobre como implementar esses protocolos para SSO em aplicativos móveis, consulte o seguinte: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Plataforma Universal do Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 e WSFed

O SAML (Security Assertion Markup Language, linguagem de marcação de afirmação de segurança) é geralmente usado para aplicativos web. Veja [como o Azure usa o protocolo SAML](../develop/active-directory-saml-protocol-reference.md) para uma visão geral. 

A Web Services Federation (WSFed) é um [padrão do setor](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) geralmente usado para aplicações web que são desenvolvidos usando a plataforma .Net.

### <a name="benefits"></a>Benefícios

O SAML 2.0 é um padrão maduro e a maioria das plataformas de tecnologia suportabibliotecas de código aberto para o SAML 2.0. Você pode fornecer aos seus clientes uma interface de administração para configurar o SAML SSO. Eles podem configurar o SAML SSO para o Microsoft Azure AD e qualquer outro provedor de identidade que suporte o SAML 2

### <a name="trade-offs"></a>Compensações

Ao usar protocolos SAML 2.0 ou WSFed para aplicativos móveis, certas políticas de Acesso Condicional, incluindo A Autenticação Multifatorial (MFA), terão uma experiência degradada. Além disso, se você quiser acessar o Microsoft Graph, você precisará implementar a autorização através do OAuth 2.0 para gerar tokens necessários. 

### <a name="implementation"></a>Implementação

A Microsoft não fornece bibliotecas para a implementação do SAML ou recomenda bibliotecas específicas. Há muitas bibliotecas de código aberto disponíveis.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e usando a API de descanso do gráfico da Microsoft 

O Microsoft Graph é a malha de dados de toda a Microsoft 365, incluindo o Office 365, o Windows 10 e a Enterprise Mobility and Security, e produtos adicionais, como o Dynamics 365. Isso inclui os esquemas principais das entidades, como Usuários, Grupos, Calendário, Correio, Arquivos e muito mais, que impulsionam a produtividade do usuário. O Microsoft Graph oferece três interfaces para desenvolvedores uma API baseada em REST, conexão de dados Microsoft Graph e Conectores que permitem que os desenvolvedores adicionem seus próprios dados ao Microsoft Graph.  

O uso de qualquer um dos protocolos acima para SSO permite o acesso do seu aplicativo aos dados ricos disponíveis através da API Microsoft Graph REST. Isso permite que seus clientes obtenham mais valor com seu investimento no Microsoft 365. Por exemplo, seu aplicativo pode chamar a API do Microsoft Graph para se integrar à instância do Office 365 de seus clientes e aos itens do Microsoft Office e SharePoint dos usuários de superfície dentro do seu aplicativo. 

Se você estiver usando o Open ID Connect para autenticar, então sua experiência de desenvolvimento é perfeita porque você usará o OAuth2, a base do Open ID Connect, para adquirir tokens pode ser usado para invocar APIs do Microsoft Graph. Se o seu aplicativo estiver usando SAML ou WSFed, você deve adicionar código adicional dentro do seu aplicativo para obter que esses OAuth2 adquiram os tokens necessários para invocar APIs do Microsoft Graph. 

## <a name="next-steps"></a>Próximas etapas

[Habilitar o SSO para seu aplicativo multilocatário](isv-sso-content.md)

[Crie documentação para seu aplicativo multi-inquilino](isv-create-sso-documentation.md)
