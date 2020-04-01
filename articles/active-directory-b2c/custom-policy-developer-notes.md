---
title: Notas do desenvolvedor para políticas personalizadas
titleSuffix: Azure AD B2C
description: Observações para os desenvolvedores sobre a configuração e a manutenção do Azure AD B2C com políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408708"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C

A configuração de diretiva personalizada no Azure Active Directory B2C está agora geralmente disponível. Este método de configuração é direcionado para desenvolvedores de identidade avançados que constroem soluções complexas de identidade. As políticas personalizadas tornam o poder do Identity Experience Framework disponível nos inquilinos Azure AD B2C.
Desenvolvedores avançados de identidade usando políticas personalizadas devem planejar investir algum tempo completando walk-throughs e lendo documentos de referência.

Embora a maioria das opções de política personalizadas disponíveis agora estejam geralmente disponíveis, existem recursos subjacentes, como tipos de perfil técnico e APIs de definição de conteúdo que estão em diferentes estágios no ciclo de vida do software. Muitos mais estão chegando. A tabela abaixo especifica o nível de disponibilidade em um nível mais granular.

## <a name="features-that-are-generally-available"></a>Recursos que geralmente estão disponíveis

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.
    - Descrever percursos do usuário passo a passo como trocas entre provedores de declarações.
    - Definir a ramificação condicional em percursos do usuário.
- Interoperar com serviços habilitados para API REST em suas jornadas personalizadas de usuário de autenticação.
- Federação com provedores de identidade compatíveis com o protocolo OpenIDConnect.
- Federação com provedores de identidade que aderem ao protocolo SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de conjunto de recursos de política personalizada

A configuração da diretiva manual concede acesso de nível inferior à plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura única e confiável. As muitas possíveis permutações de provedores de identidade personalizados, relacionamentos de confiança, integrações com serviços externos e fluxos de trabalho passo a passo exigem uma abordagem metódica para o design e a configuração.

Os desenvolvedores que consomem o conjunto de recursos de diretiva personalizado devem seguir as seguintes diretrizes:

- Familiarize-se com a linguagem de configuração das políticas personalizadas e do gerenciamento de chaves/segredos. Para obter mais informações, consulte [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Apropriar-se de cenários e de integrações personalizadas. Documente seu trabalho e informe sua organização do site ao vivo.
- Executar testes de cenário metódicos.
- Siga as práticas recomendadas de desenvolvimento/preparo de software com no mínimo um ambiente de desenvolvimento e teste um ambiente de produção.
- Mantenha-se informado sobre novos desenvolvimentos de provedores de identidade e de serviços para integração. Por exemplo, acompanhe as alterações em segredos e alterações programadas e não programadas no serviço.
- Configure o monitoramento ativo e monitore a capacidade de resposta dos ambientes de produção. Para obter mais informações sobre a integração com o Application Insights, consulte [O Azure Active Directory B2C: Collecting Logs](analytics-with-application-insights.md).
- Mantenha atualizados os endereços de email de contato na assinatura do Azure e permaneça responsivo aos emails da equipe de site ativo da Microsoft.
- Execute ação em tempo hábil quando for aconselhado a fazer isso pela equipe de site ativo da Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termos para recursos em visualização pública

- Nós encorajamos você a usar os recursos de visualização pública apenas para fins de avaliação.
- Os contratos de nível de serviço (SLAs) não se aplicam aos recursos de visualização pública.
- As solicitações de suporte para recursos de visualização pública podem ser arquivadas através de canais de suporte regulares.

## <a name="features-by-stage-and-known-issues"></a>Recursos por estágio e problemas conhecidos

Os recursos personalizados da Policy/Identity Experience Framework estão em desenvolvimento constante e rápido. A tabela a seguir é um índice de recursos e disponibilidade de componentes.


### <a name="protocols-and-authorization-flows"></a>Protocolos e fluxos de autorização

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Código de autorização OAuth2](authorization-code-flow.md) |  |  | X |  |
| Código de autorização OAuth2 com PKCE |  |  | X | Apenas aplicativos móveis  |
| [Fluxo implícito OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenciais de senha do proprietário de recursos OAuth2](ropc-custom.md) |  | X |  |  |
| [Conexão OIDC](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |X  |  | Ligações POST e Redirecionar. |
| OAuth1 |  |  |  | Sem suporte. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificar a federação de provedores 

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Por exemplo, o Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Por exemplo, facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Por exemplo, twitter. |
| [SAML2](saml-technical-profile.md) |  |   | X | Por exemplo, Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integração da API REST

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [API REST com auth básico](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [API REST com certificado de cliente auth](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [API REST com OAuth2 portador auth](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Suporte a componentes

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticação do fator telefônico](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticação Azure MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Senha de uso único](one-time-password-technical-profile.md) |  | X |  |  |
| [Diretório Ativo do Azure](active-directory-technical-profile.md) como diretório local |  |  | X |  |
| Subsistema de e-mail do Azure para verificação de e-mail |  |  | X |  |
| [Provedores de serviços de e-mail de terceiros](custom-email.md) |  |X  |  |  |
| [Suporte a vários idiomas](localization.md)|  |  | X |  |
| [Validações de predicados](predicates.md) |  |  | X | Por exemplo, complexidade de senha. |
| [Controles de exibição](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Versões do layout da página

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [Suporte javaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>Integração de aplicativo IEF

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro de string de consulta`domain_hint` |  |  | X | Disponível como reclamação, pode ser passado para ODP. |
| Parâmetro de string de consulta`login_hint` |  |  | X | Disponível como reclamação, pode ser passado para ODP. |
| Insira JSON na jornada do usuário via`client_assertion` | X |  |  | Será preterido. |
| Insira JSON na jornada do usuário como`id_token_hint` |  | X |  | Abordagem para avançar para passar JSON. |
| [Passe o token do provedor de identidade para o aplicativo](idp-pass-through-custom.md) |  | X |  | Por exemplo, do Facebook ao aplicativo. |

### <a name="session-management"></a>Gerenciamento da sessão

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Provedor de sessão Padrão SSO](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Provedor de sessão de login externo](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provedor de sessão SAML SSO](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Segurança

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política – gerar, manual, upload |  |  | X |  |
| Chaves de política – RSA/Cert, segredos |  |  | X |  |


### <a name="developer-interface"></a>Interface do desenvolvedor

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portal do Azure – IEF UX |  |  | X |  |
| Upload de políticas |  |  | X |  |
| [Logs de jornada do usuário do Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Usado para solução de problemas durante o desenvolvimento.  |
| [Registros de eventos do Application Insights](application-insights-technical-profile.md) |  | X |  | Usado para monitorar fluxos de usuários na produção. |


## <a name="next-steps"></a>Próximas etapas

- Verifique as operações do [Microsoft Graph disponíveis para Azure AD B2C](microsoft-graph-operations.md)
- Saiba mais sobre [políticas personalizadas e as diferenças com os fluxos de usuário.](custom-policy-overview.md)
