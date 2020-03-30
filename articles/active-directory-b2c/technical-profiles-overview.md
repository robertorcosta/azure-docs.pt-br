---
title: Visão geral dos perfis técnicos em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como os perfis técnicos são usados em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057305"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Sobre perfis técnicos em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece uma estrutura com um mecanismo embutido para se comunicar com diferentes tipos de partes usando uma política personalizada no Azure Active Directory B2C (Azure AD B2C). Os perfis técnicos são usados para se comunicar com o locatário do Azure AD B2C, para criar um usuário ou ler um perfil de usuário. Um perfil técnico pode ser autodeclarado para permitir a interação com o usuário. Por exemplo, colete a credencial do usuário para entrar e renderizar a página de inscrição ou a página de redefinição de senha.

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico permite esses tipos de cenários:

- [Insights do aplicativo](application-insights-technical-profile.md) - Envio de dados de eventos para [o Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) – oferece suporte ao gerenciamento de usuário do Azure Active Directory B2C.
- [Autenticação multifatorial do Azure](multi-factor-auth-technical-profile.md) - fornece suporte para verificar um número de telefone usando o MFA (Multi-Factor Authentication, autenticação multifatorial) do Azure. 
- [Transformação de declarações](claims-transformation-technical-profile.md) – chama transformações de declarações de saída para manipular valores de declarações, validar declarações ou definir valores padrão para um conjunto de declarações de saída.
- [Emissor de token JWT](jwt-issuer-technical-profile.md) – emite um token JWT que é retornado para o aplicativo de terceira parte confiável.
- [OAuth1](oauth1-technical-profile.md) – federação com qualquer provedor de identidade de protocolo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) – federação com qualquer provedor de identidade de protocolo OAuth 2.0.
- [Senha única](one-time-password-technical-profile.md) - Fornece suporte para gerenciar a geração e verificação de uma senha única.
- [OpenID Connect](openid-connect-technical-profile.md) - Federação com qualquer provedor de identidade de protocolo OpenID Connect.
- [Fator telefônico](phone-factor-technical-profile.md) - Suporte para inscrição e verificação de números de telefone.
- [Provedor RESTful](restful-technical-profile.md) - Serviços de API call to REST, como validar a entrada do usuário, enriquecer os dados do usuário ou integrar-se a aplicativos de linha de negócios.
- [SAML2](saml-technical-profile.md) – federação com qualquer provedor de identidade de protocolo SAML.
- [Emissor de token SAML](saml-issuer-technical-profile.md) - Emite um token SAML que é devolvido ao aplicativo de parte que depende.
- [Self-Asserted](self-asserted-technical-profile.md) – Interagir com o usuário. Por exemplo, colete a credencial do usuário para entrar e renderizar a página de inscrição ou a redefinição de senha.
- [Gerenciamento de sessão](custom-policy-reference-sso.md) – lida com diferentes tipos de sessões.

## <a name="technical-profile-flow"></a>Fluxo do perfil técnico

Todos os tipos de perfis técnicos compartilham o mesmo conceito. Você envia declarações de entrada, executa a transformação de declarações e se comunica com a parte configurada, como um provedor de identidade, uma API REST ou serviços de diretório do Azure AD. Após a conclusão do processo, o perfil técnico retorna as reivindicações de saída e pode executar a transformação de sinistros de saída. O diagrama a seguir mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Independentemente da entidade com a qual o perfil técnico interage, após a execução de qualquer declaração de transformação, as declarações de saída do perfil técnico são imediatamente armazenadas no recipiente de declarações.

![Diagrama ilustrando o fluxo do perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Gerenciamento de sessão de inscrição única (SSO)** - Restaura o estado de sessão do perfil técnico, usando [o gerenciamento de sessão SSO](custom-policy-reference-sso.md).
1. **Transformação de sinistros** de entrada - As reivindicações de entrada de cada transformação de [sinistros](claimstransformations.md) de entrada são captadas a partir do saco de sinistros.  As declarações de saída de uma transformação de declarações de entrada podem ser declarações de entrada de uma transformação de declarações de entrada subsequentes.
1. **Reivindicações de entrada** - As reclamações são retiradas do saco de sinistros e são utilizadas para o perfil técnico. Por exemplo, um [perfil técnico autodeclarado](self-asserted-technical-profile.md) usa as declarações de entrada para preencher antecipadamente as declarações de saída fornecidas pelo usuário. Um perfil técnico da API REST usa as declarações de entrada para enviar parâmetros de entrada ao ponto de extremidade da API REST. O Azure Active Directory usa a declaração de entrada como um identificador exclusivo para ler, atualizar ou excluir uma conta.
1. **Execução do perfil técnico** – o perfil técnico troca as declarações pela entidade configurada. Por exemplo: 
    - Redirecione o usuário para o provedor de identidade para concluir a conexão. Após conectar-se, o usuário retornará e prosseguirá com a execução do perfil técnico.
    - Chame uma API REST enquanto envia parâmetros como o InputClaims e receba de volta informações como o OutputClaims.
    - Crie ou atualize a conta de usuário.
    - Envia e verifica a mensagem de texto da MFA.
1. **Perfis técnicos de validação** - Um [perfil técnico auto-afirmado](self-asserted-technical-profile.md) pode chamar [perfis técnicos de validação](validation-technical-profile.md). O perfil técnico de validação valida os dados analisados pelo usuário e retorna uma mensagem de erro ou OK, com ou sem declarações de saída. Por exemplo, antes de criar uma nova conta, o Azure AD B2C verifica se o usuário já existe nos serviços de diretório. Você pode chamar um perfil técnico da API REST para adicionar sua própria lógica de negócios.<p>O escopo das reivindicações de saída de um perfil técnico de validação limita-se ao perfil técnico que invoca o perfil técnico de validação. e outros perfis técnicos de validação o mesmo perfil técnico. Se você quiser usar as declarações de saída na próxima etapa da orquestração, será necessário incluir as declarações de saída no perfil técnico que chama o perfil técnico de validação.
1. **Reivindicações de saída** - As reivindicações são devolvidas ao saco de sinistros. Você pode usar essas declarações na próxima etapa de orquestrações ou transformações de declarações de saída.
1. **Transformações de sinistros** de saída - As reivindicações de entrada de cada transformação de [sinistros](claimstransformations.md) de saída são captadas do saco de sinistros. As declarações de saída do perfil técnico das etapas anteriores podem ser entradas de uma transformação de declarações de saída. Após a execução, as declarações de saída são recolocadas no recipiente de declarações. As declarações de saída de uma transformação de declarações de saída também podem ser declarações de entrada de uma transformação de declarações de saída subsequentes.
1. **Gerenciamento de sessão de inscrição única (SSO)** - Persiste os dados do perfil técnico para a sessão, usando o [gerenciamento de sessão SSO](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Inclusão de perfil técnico

Um perfil técnico pode incluir outro perfil técnico para alterar configurações ou adicionar novas funcionalidades.  O `IncludeTechnicalProfile` elemento é uma referência ao perfil técnico básico do qual um perfil técnico é derivado. Não há nenhum limite ao número de níveis.

Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NoError** inclui o **AAD-UserReadUsingAlternativeSecurityId**. Esse perfil técnico `RaiseErrorIfClaimsPrincipalDoesNotExist` define o `true`item de metadados para , e levanta um erro se uma conta social não existir no diretório. **AAD-UserReadUsingAlternativeSecurityId-NoError** substitui esse comportamento e desativa essa mensagem de erro.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclui o perfil técnico `AAD-Common`.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Tanto **o AAD-UserReadReadSId-NoError** quanto **o AAD-UserReadAlternativeSecurityId** não especificam o elemento **de protocolo** necessário, porque ele está especificado no perfil técnico **AAD-Common.**

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
