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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80408708"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C

A configuração de política personalizada no Azure Active Directory B2C agora está disponível para o público geral. Esse método de configuração é direcionado a desenvolvedores de identidade avançada que criam soluções de identidade complexas. As políticas personalizadas tornam o poder da estrutura de experiência de identidade disponível em locatários Azure AD B2C.
Os desenvolvedores de identidade avançada usando políticas personalizadas devem planejar investir algum tempo concluindo orientações e lendo documentos de referência.

Embora a maioria das opções de política personalizada disponíveis agora esteja disponível para o público geral, há recursos subjacentes, como tipos de perfil técnico e APIs de definição de conteúdo que estão em diferentes estágios no ciclo de vida do software. Muito mais estão chegando. A tabela abaixo especifica o nível de disponibilidade em um nível mais granular.

## <a name="features-that-are-generally-available"></a>Recursos que estão geralmente disponíveis

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.
    - Descrever percursos do usuário passo a passo como trocas entre provedores de declarações.
    - Definir a ramificação condicional em percursos do usuário.
- Interopere com serviços habilitados para API REST em suas jornadas do usuário de autenticação personalizada.
- Federar com provedores de identidade que são compatíveis com o protocolo OpenIDConnect.
- Federar com provedores de identidade que aderem ao protocolo SAML 2,0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de conjunto de recursos de política personalizada

A configuração de política manual concede acesso de nível inferior à plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura exclusiva de confiança. As muitas permutas possíveis de provedores de identidade personalizados, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo exigem uma abordagem unidirecional para design e configuração.

Os desenvolvedores que consomem o conjunto de recursos de política personalizada devem aderir às seguintes diretrizes:

- Familiarize-se com o idioma de configuração das políticas personalizadas e do gerenciamento de chaves/segredos. Para obter mais informações, consulte [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Apropriar-se de cenários e de integrações personalizadas. Documente seu trabalho e informe sua organização do site ativo.
- Executar testes de cenário metódicos.
- Siga as práticas recomendadas de desenvolvimento/preparo de software com no mínimo um ambiente de desenvolvimento e teste um ambiente de produção.
- Mantenha-se informado sobre novos desenvolvimentos de provedores de identidade e de serviços para integração. Por exemplo, acompanhe as alterações em segredos e alterações programadas e não programadas no serviço.
- Configure o monitoramento ativo e monitore a capacidade de resposta dos ambientes de produção. Para obter mais informações sobre como integrar com Application Insights, consulte [Azure Active Directory B2C: coletando logs](analytics-with-application-insights.md).
- Mantenha atualizados os endereços de email de contato na assinatura do Azure e permaneça responsivo aos emails da equipe de site ativo da Microsoft.
- Execute ação em tempo hábil quando for aconselhado a fazer isso pela equipe de site ativo da Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termos para recursos na visualização pública

- Incentivamos você a usar os recursos de visualização pública apenas para fins de avaliação.
- Os SLAs (contratos de nível de serviço) não se aplicam aos recursos de visualização pública.
- As solicitações de suporte para recursos de visualização pública podem ser arquivadas por meio de canais de suporte regular.

## <a name="features-by-stage-and-known-issues"></a>Recursos por estágio e problemas conhecidos

Recursos personalizados de estrutura de experiência de identidade/política estão em constante e desenvolvimento rápido. A tabela a seguir é um índice de recursos e disponibilidade de componentes.


### <a name="protocols-and-authorization-flows"></a>Protocolos e fluxos de autorização

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Código de autorização do OAuth2](authorization-code-flow.md) |  |  | X |  |
| Código de autorização do OAuth2 com PKCE |  |  | X | Somente aplicativos móveis  |
| [Fluxo implícito do OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenciais de senha do proprietário do recurso OAuth2](ropc-custom.md) |  | X |  |  |
| [OIDC conectar](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |X  |  | PÓS e redirecionar associações. |
| OAuth1 |  |  |  | Sem suporte. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificar Federação de provedores 

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Por exemplo, Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Por exemplo, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Por exemplo, Twitter. |
| [SAML2](saml-technical-profile.md) |  |   | X | Por exemplo, Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integração da API REST

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| [API REST com autenticação básica](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [API REST com autenticação de certificado de cliente](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [API REST com autenticação de portador OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Suporte a componentes

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticação do fator de telefone](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticação do Azure MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Senha de uso único](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) como diretório local |  |  | X |  |
| Subsistema de email do Azure para verificação de email |  |  | X |  |
| [Provedores de serviço de email de terceiros](custom-email.md) |  |X  |  |  |
| [Suporte a vários idiomas](localization.md)|  |  | X |  |
| [Validações de predicado](predicates.md) |  |  | X | Por exemplo, complexidade de senha. |
| [Controles de exibição](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Versões do layout da página

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [Suporte a JavaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>Integração de aplicativo IEF

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro de cadeia de caracteres de consulta`domain_hint` |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Parâmetro de cadeia de caracteres de consulta`login_hint` |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Inserir JSON na jornada do usuário via`client_assertion` | X |  |  | Será preterido. |
| Inserir JSON na jornada do usuário como`id_token_hint` |  | X |  | Abordagem de avanço para passar JSON. |
| [Passar token do provedor de identidade para o aplicativo](idp-pass-through-custom.md) |  | X |  | Por exemplo, do Facebook para o aplicativo. |

### <a name="session-management"></a>Gerenciamento da sessão

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Provedor de sessão de SSO padrão](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Provedor de sessão de logon externo](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provedor de sessão de SSO do SAML](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Segurança

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política – gerar, manual, upload |  |  | X |  |
| Chaves de política – RSA/Cert, segredos |  |  | X |  |


### <a name="developer-interface"></a>Interface do desenvolvedor

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portal do Azure – IEF UX |  |  | X |  |
| Upload de política |  |  | X |  |
| [Application Insights os logs de jornada do usuário](troubleshoot-with-application-insights.md) |  | X |  | Usado para solução de problemas durante o desenvolvimento.  |
| [Application Insights logs de eventos](application-insights-technical-profile.md) |  | X |  | Usado para monitorar fluxos de usuário na produção. |


## <a name="next-steps"></a>Próximas etapas

- Verifique as [operações de Microsoft Graph disponíveis para Azure ad B2C](microsoft-graph-operations.md)
- Saiba mais sobre [políticas personalizadas e as diferenças com fluxos de usuário](custom-policy-overview.md).
