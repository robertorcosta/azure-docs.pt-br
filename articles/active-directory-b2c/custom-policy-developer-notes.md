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
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189388"
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

### <a name="identity-providers-tokens-protocols"></a>Provedores de Identidade, Tokens, Protocolos

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Por exemplo, o Google+.  |
| IDP-OAUTH2 |  |  | X | Por exemplo, facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Por exemplo, twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Sem suporte |
| IDP-SAML |  |   | X | Por exemplo, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Festa de Dependência OAUTH1 |  |  |  | Sem suporte. |
| Festa de Dependência OAUTH2 |  |  | X |  |
| OIDC de terceira parte confiável |  |  | X |  |
| SAML de terceira parte confiável |  |X  |  |  |
| WSFED de terceira parte confiável | X |  |  |  |
| API REST com auth básico e certificado |  |  | X | Por exemplo, Azure Logic Apps. |

### <a name="component-support"></a>Suporte do componente

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Autenticação Multifator do Azure |  |  | X |  |
| Azure Active Directory como diretório local |  |  | X |  |
| Subsistema de e-mail do Azure para verificação de e-mail |  |  | X |  |
| Suporte a vários idiomas|  |  | X |  |
| Validações de predicados |  |  | X | Por exemplo, complexidade de senha. |
| Usando provedores de serviços de e-mail de terceiros |  |X  |  |  |

### <a name="content-definition"></a>Definição de conteúdo

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Página de erro, api.error |  |  | X |  |
| Página de seleção de IDP, api.idpselections |  |  | X |  |
| Seleção de IDP para inscrição, api.idpselections.signup |  |  | X |  |
| Esqueceu a senha, api.localaccountpasswordreset |  |  | X |  |
| Entrada na Conta Local, api.localaccountsignin |  |  | X |  |
| Inscrição na Conta Local, api.localaccountsignup |  |  | X |  |
| Página MFA, api.phonefactor |  |  | X |  |
| Auto-afirmado inscrição de conta social, api.auto-afirmado |  |  | X |  |
| Atualização de perfil autodeclarada, api.selfasserted.profileupdate |  |  | X |  |
| Página de inscrição unificada ou login, api.signuporsignin, com parâmetro "disableSignup" |  |  | X |  |
| JavaScript / Layout de página |  | X |  |  |

### <a name="app-ief-integration"></a>Integração de aplicativo IEF

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro de cadeia de consulta domain_hint |  |  | X | Disponível como reclamação, pode ser passado para ODP. |
| Parâmetro de cadeia de consulta login_hint |  |  | X | Disponível como reclamação, pode ser passado para ODP. |
| Inserir JSON UserJourney via client_assertion | X |  |  | Será preterido. |
| Inserir JSON em UserJourney como id_token_hint |  | X |  | Abordagem para avançar para passar JSON. |
| Passe o IDP TOKEN para o aplicativo |  | X |  | Por exemplo, do Facebook ao aplicativo. |

### <a name="session-management"></a>Gerenciamento da sessão

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Provedor de sessão SSO |  |  | X |  |
| Provedor de sessão de logon externo |  |  | X |  |
| Provedor de sessão de SSO do SAML |  |  | X |  |
| Provedor de sessão Padrão SSO |  |  | X |  |

### <a name="security"></a>Segurança

| Recurso | Desenvolvimento | Visualização | GA | Observações |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política – gerar, manual, upload |  |  | X |  |
| Chaves de política – RSA/Cert, segredos |  |  | X |  |
| Upload de políticas |  |  | X |  |

### <a name="developer-interface"></a>Interface do desenvolvedor

| Recurso | Desenvolvimento | Visualização | GA | Observações |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portal do Azure – IEF UX |  |  | X |  |
| Logs UserJourney do Application Insights |  | X |  | Usado para solução de problemas durante o desenvolvimento.  |
| Registros de eventos do Application Insights (através de etapas de orquestração) |  | X |  | Usado para monitorar fluxos de usuários na produção. |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [políticas personalizadas e as diferenças com os fluxos de usuário.](custom-policy-overview.md)
