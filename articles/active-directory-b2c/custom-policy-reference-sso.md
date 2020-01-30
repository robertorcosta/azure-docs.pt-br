---
title: Gerenciamento de sessão de logon único usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como gerenciar sessões de SSO usando políticas personalizadas no Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ee32b13820cb50fc1649672b78b34e7e293d65b5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849078"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gerenciamento de sessão de logon único no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O gerenciamento de sessão de SSO (logon único) no Azure Active Directory B2C (Azure AD B2C) permite que um administrador controle a interação com um usuário depois que o usuário já tiver se autenticado. Por exemplo, o administrador pode controlar se a seleção de provedores de identidade é exibida ou se os detalhes de conta local precisam ser inseridos novamente. Este artigo descreve como definir as configurações de SSO para o Azure AD B2C.

O gerenciamento de sessão de SSO tem duas partes. A primeira lida com interações do usuário diretamente com o Azure AD B2C e a outra lida com interações do usuário com parceiros externos, como Facebook. O Azure AD B2C não substitui ou ignora as sessões de SSO que podem ser mantidas por parceiros externos. Em vez disso, a rota por meio do Azure AD B2C para chegar ao parceiro externo é “lembrada”, evitando a necessidade de solicitar novamente que o usuário selecione seu provedor de identidade social ou empresarial. A decisão de SSO final permanece com o parceiro externo.

O gerenciamento de sessão de SSO usa a mesma semântica que qualquer outro perfil técnico em políticas personalizadas. Quando uma etapa de orquestração é executada, o perfil técnico associado à etapa é consultado quanto a uma referência `UseTechnicalProfileForSessionManagement`. Se existir uma, o provedor de sessão de SSO referenciado será verificado para ver se o usuário é um participante da sessão. Se sim, o provedor de sessão de SSO será usado para popular novamente a sessão. Da mesma forma, quando a execução de uma etapa de orquestração for concluída, o provedor será usado para armazenar informações na sessão se um provedor de sessão de SSO tiver sido especificado.

O Azure AD B2C definiu vários provedores de sessão de SSO que podem ser usados:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

As classes de gerenciamento de SSO são especificadas usando o elemento `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` de um perfil de técnico.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Como o nome indica, este provedor não faz nada. Esse provedor pode ser usado para suprimir o comportamento de SSO para um determinado perfil técnico.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Esse provedor pode ser usado para armazenar as declarações em uma sessão. Normalmente, esse provedor é referenciado em um perfil técnico usado para gerenciar contas locais. Ao usar o DefaultSSOSessionProvider para armazenar declarações em uma sessão, é necessário garantir que quaisquer declarações que precisam ser retornadas ao aplicativo ou usadas por pré-condições nas etapas subsequentes sejam armazenadas na sessão ou aumentadas por uma leitura do perfil dos usuários no diretório. Isso garantirá que o percurso de autenticação não falhe em reivindicações ausentes.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Para adicionar declarações à sessão, use o elemento `<PersistedClaims>` do perfil técnico. Quando o provedor é usado para popular novamente a sessão, as declarações persistentes são adicionadas ao conjunto de declarações. `<OutputClaims>` é usado para recuperar as declarações da sessão.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Esse provedor é usado para suprimir a tela "escolher o provedor de identidade". Normalmente, ele é referenciado em um perfil técnico configurado para um provedor de identidade externo, como o Facebook.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Esse provedor é usado para gerenciar as sessões de SAML do Azure AD B2C entre aplicativos, bem como provedores de identidade SAML externos.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Há dois itens de metadados no perfil técnico:

| Item | Valor Padrão | Valores possíveis | Description
| --- | --- | --- | --- |
| IncludeSessionIndex | true | true/false | Indica ao provedor que o índice de sessão deve ser armazenado. |
| RegisterServiceProviders | true | true/false | Indica que o provedor deve registrar todos os provedores de serviço SAML que emitiram uma declaração. |

Ao usar o provedor para armazenar uma sessão de provedor de identidade SAML, os itens acima devem ser falsos. Ao usar o provedor para armazenar a sessão de SAML do B2C, os itens acima devem ser verdadeiros ou omitidos, já que os padrões são verdadeiros. O logoff da sessão de SAML requer que `SessionIndex` e `NameID` sejam concluídos.

