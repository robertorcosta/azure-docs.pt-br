---
title: Definir um perfil técnico para um emissor SAML em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico para um emissor de token SAML (Security Assertion Markup Language) em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bb5383ee7930cb3d54593f71a709c033d3850889
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88521205"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico para um emissor de token SAML em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C (Azure Active Directory B2C) emite vários tipos de tokens de segurança ao processar cada fluxo de autenticação. Um perfil técnico para um emissor de token SAML emite um token SAML que é retornado para o aplicativo de terceira parte confiável (provedor de serviço). Normalmente, esse perfil técnico é a última etapa de orquestração no percurso do usuário.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `None`. Defina o elemento **OutputTokenFormat** como `SAML2`.

O exemplo a seguir mostra um perfil técnico para `Saml2AssertionIssuer`:

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Entrada, saída e manter declarações

Os elementos **InputClaims**, **OutputClaims** e **PersistClaims** estão vazios ou ausentes. Os elementos **InutputClaimsTransformations** e **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| IssuerUri | Não | O nome do emissor que aparece na resposta SAML. O valor deve ser o nome configurado no aplicativo de terceira parte confiável. |
| XmlSignatureAlgorithm | Não | O método que Azure AD B2C usa para assinar a Asserção SAML. Valores possíveis: `Sha256`, `Sha384`, `Sha512` ou `Sha1`. Certifique-se de configurar o algoritmo de assinatura em ambos os lados com o mesmo valor. Use apenas o algoritmo com suporte do seu certificado. Para configurar a resposta SAML, consulte [metadados SAML](relyingparty.md#metadata) de terceira parte confiável|

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| MetadataSigning | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar os metadados de SAML. O Azure AD B2C usa essa chave para assinar os metadados. |
| SamlMessageSigning| Sim| Especifique o certificado X509 (conjunto de chaves RSA) a ser usado para assinar as mensagens SAML. O Azure AD B2C usa essa chave para assinar o envio da resposta `<samlp:Response>` para a terceira parte confiável.|

## <a name="session-management"></a>Gerenciamento da sessão

Para configurar as sessões de SAML do Azure AD B2C entre um aplicativo de terceira parte confiável, o atributo do elemento `UseTechnicalProfileForSessionManagement`, consulte a sessão de SSO [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="next-steps"></a>Próximas etapas

Confira o seguinte artigo para obter exemplos de como usar um perfil técnico de emissor SAML:

- [Registrar um aplicativo SAML no Azure AD B2C](connect-with-saml-service-providers.md)

