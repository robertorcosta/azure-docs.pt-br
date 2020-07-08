---
title: Visão geral de perfis técnicos em políticas personalizadas
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
ms.openlocfilehash: 16fdc38d6235ddd0f72c7a35a3d71973ce01a4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203207"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Sobre perfis técnicos em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico fornece uma estrutura com um mecanismo interno para se comunicar com diferentes tipos de partes usando uma política personalizada no Azure Active Directory B2C (Azure AD B2C). Os perfis técnicos são usados para se comunicar com o locatário do Azure AD B2C, para criar um usuário ou ler um perfil de usuário. Um perfil técnico pode ser autodeclarado para permitir a interação com o usuário. Por exemplo, colete a credencial do usuário para entrar e renderizar a página de inscrição ou a página de redefinição de senha.

## <a name="type-of-technical-profiles"></a>Tipo de perfis técnicos

Um perfil técnico permite esses tipos de cenários:

- [Application insights](application-insights-technical-profile.md) -enviando dados de evento para [Application insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) – oferece suporte ao gerenciamento de usuário do Azure Active Directory B2C.
- [Autenticação multifator do Azure](multi-factor-auth-technical-profile.md) -fornece suporte para verificar um número de telefone usando a MFA (autenticação multifator do Azure). 
- [Transformação de declarações](claims-transformation-technical-profile.md) – chama transformações de declarações de saída para manipular valores de declarações, validar declarações ou definir valores padrão para um conjunto de declarações de saída.
- [Emissor de token JWT](jwt-issuer-technical-profile.md) – emite um token JWT que é retornado para o aplicativo de terceira parte confiável.
- [OAuth1](oauth1-technical-profile.md) – federação com qualquer provedor de identidade de protocolo OAuth 1.0.
- [OAuth2](oauth2-technical-profile.md) – federação com qualquer provedor de identidade de protocolo OAuth 2.0.
- [Senha de uma vez](one-time-password-technical-profile.md) – fornece suporte para gerenciar a geração e a verificação de uma senha de uso único.
- [OpenID Connect](openid-connect-technical-profile.md) – Federação com qualquer provedor de identidade de protocolo OpenID Connect.
- [Fator de telefone](phone-factor-technical-profile.md) -suporte para registro e verificação de números de telefone.
- [Provedor RESTful](restful-technical-profile.md) -chamada para serviços de API REST, como validar entrada do usuário, enriquecer dados do usuário ou integrar com aplicativos de linha de negócios.
- [Provedor de identidade SAML](saml-identity-provider-technical-profile.md) – Federação com qualquer provedor de identidade de protocolo SAML.
- [Emissor de token SAML](saml-issuer-technical-profile.md) – emite um token SAML que é retornado para o aplicativo de terceira parte confiável.
- [Self-Asserted](self-asserted-technical-profile.md) – Interagir com o usuário. Por exemplo, colete a credencial do usuário para entrar e renderizar a página de inscrição ou a redefinição de senha.
- [Gerenciamento de sessão](custom-policy-reference-sso.md) – lida com diferentes tipos de sessões.

## <a name="technical-profile-flow"></a>Fluxo do perfil técnico

Todos os tipos de perfis técnicos compartilham o mesmo conceito. Você envia declarações de entrada, executa a transformação de declarações e se comunica com a parte configurada, como um provedor de identidade, uma API REST ou serviços de diretório do Azure AD. Depois que o processo for concluído, o perfil técnico retornará as declarações de saída e poderá executar a transformação de declarações de saída. O diagrama a seguir mostra como as transformações e mapeamentos referenciados no perfil técnico são processados. Independentemente da entidade com a qual o perfil técnico interage, após a execução de qualquer declaração de transformação, as declarações de saída do perfil técnico são imediatamente armazenadas no recipiente de declarações.

![Diagrama ilustrando o fluxo de perfil técnico](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)

1. **Gerenciamento de sessão de logon único (SSO)** – restaura o estado de sessão do perfil técnico, usando o [Gerenciamento de sessão de SSO](custom-policy-reference-sso.md).
1. **Transformação de declarações de entrada** -as declarações de entrada de cada [transformação de declarações](claimstransformations.md) de entrada são selecionadas no recipiente de declarações.  As declarações de saída de uma transformação de declarações de entrada podem ser declarações de entrada de uma transformação de declarações de entrada subsequentes.
1. **Declarações de entrada** -as declarações são coletadas do recipiente de declarações e são usadas para o perfil técnico. Por exemplo, um [perfil técnico autodeclarado](self-asserted-technical-profile.md) usa as declarações de entrada para preencher antecipadamente as declarações de saída fornecidas pelo usuário. Um perfil técnico da API REST usa as declarações de entrada para enviar parâmetros de entrada ao ponto de extremidade da API REST. O Azure Active Directory usa a declaração de entrada como um identificador exclusivo para ler, atualizar ou excluir uma conta.
1. **Execução do perfil técnico** – o perfil técnico troca as declarações pela entidade configurada. Por exemplo:
    - Redirecione o usuário para o provedor de identidade para concluir a conexão. Após conectar-se, o usuário retornará e prosseguirá com a execução do perfil técnico.
    - Chame uma API REST enquanto envia parâmetros como o InputClaims e receba de volta informações como o OutputClaims.
    - Crie ou atualize a conta de usuário.
    - Envia e verifica a mensagem de texto da MFA.
1. **Perfis técnicos de validação** -um [perfil técnico autodeclarado](self-asserted-technical-profile.md) pode chamar [perfis técnicos de validação](validation-technical-profile.md). O perfil técnico de validação valida os dados analisados pelo usuário e retorna uma mensagem de erro ou OK, com ou sem declarações de saída. Por exemplo, antes de criar uma nova conta, o Azure AD B2C verifica se o usuário já existe nos serviços de diretório. Você pode chamar um perfil técnico da API REST para adicionar sua própria lógica de negócios.<p>O escopo das declarações de saída de um perfil técnico de validação é limitado ao perfil técnico que invoca o perfil técnico de validação. e outros perfis técnicos de validação no mesmo perfil técnico. Se você quiser usar as declarações de saída na próxima etapa da orquestração, será necessário incluir as declarações de saída no perfil técnico que chama o perfil técnico de validação.
1. **Declarações de saída** -as declarações são retornadas de volta para o recipiente de declarações. Você pode usar essas declarações na próxima etapa de orquestrações ou transformações de declarações de saída.
1. **Transformações de declarações de saída** – as declarações de entrada de cada [transformação de declarações](claimstransformations.md) de saída são coletadas do recipiente de declarações. As declarações de saída do perfil técnico das etapas anteriores podem ser entradas de uma transformação de declarações de saída. Após a execução, as declarações de saída são recolocadas no recipiente de declarações. As declarações de saída de uma transformação de declarações de saída também podem ser declarações de entrada de uma transformação de declarações de saída subsequentes.
1. **Gerenciamento de sessão de logon único (SSO)** – persiste os dados do perfil técnico para a sessão, usando o [Gerenciamento de sessão de SSO](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Inclusão de perfil técnico

Um perfil técnico pode incluir outro perfil técnico para alterar as configurações ou adicionar uma nova funcionalidade.  O `IncludeTechnicalProfile` elemento é uma referência ao perfil técnico base do qual um perfil técnico é derivado. Não há nenhum limite ao número de níveis.

Por exemplo, o perfil técnico **AAD-UserReadUsingAlternativeSecurityId-NoError** inclui o **AAD-UserReadUsingAlternativeSecurityId**. Esse perfil técnico define o `RaiseErrorIfClaimsPrincipalDoesNotExist` item de metadados como `true` e gerará um erro se uma conta social não existir no diretório. **AAD-UserReadUsingAlternativeSecurityId-NOERROR** substitui esse comportamento e desabilita essa mensagem de erro.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** inclui o perfil técnico `AAD-Common`.

```xml
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

O **AAD-UserReadUsingAlternativeSecurityId-NOERROR** e o **AAD-UserReadUsingAlternativeSecurityId** não especificam o elemento de **protocolo** necessário, pois ele é especificado no perfil técnico **AAD-Common** .

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
