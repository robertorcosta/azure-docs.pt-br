---
title: Defina um perfil técnico para um emissor SAML em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico para um emissor de token de linguagem de marcação de afirmação de segurança (SAML) em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967266"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico para um emissor de token SAML em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C (Azure Active Directory B2C) emite vários tipos de tokens de segurança ao processar cada fluxo de autenticação. Um perfil técnico para um emissor de token SAML emite um token SAML que é devolvido ao aplicativo de parte de contagem (provedor de serviços). Normalmente, esse perfil técnico é a última etapa de orquestração no percurso do usuário.

## <a name="protocol"></a>Protocolo

O atributo **Nome** do elemento **Protocolo** precisa ser definido como `None`. Defina o elemento **OutputTokenFormat** como `SAML2`.

O exemplo a seguir mostra um perfil técnico para `Saml2AssertionIssuer`:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
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
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Entrada, saída e manter declarações

Os elementos **InputClaims**, **OutputClaims** e **PersistClaims** estão vazios ou ausentes. Os elementos **InutputClaimsTransformations** e **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| IssuerUri | Não | O nome do emissor que aparece na resposta SAML. O valor deve ser o mesmo nome configurado na aplicação do partido de dependência. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| MetadataSigning | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar os metadados de SAML. O Azure AD B2C usa essa chave para assinar os metadados. |
| SamlMessageSigning| Sim| Especifique o certificado X509 (conjunto de chaves RSA) para usar para assinar mensagens SAML. O Azure AD B2C usa `<samlp:Response>` essa chave para assinar a resposta enviada à parte que confia.|

## <a name="session-management"></a>Gerenciamento da sessão

Para configurar as sessões De SAML AD B2C do Azure `UseTechnicalProfileForSessionManagement` entre um aplicativo de parte que depende, o atributo do elemento, referência à sessão [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO.

## <a name="next-steps"></a>Próximas etapas

Veja o artigo a seguir, por exemplo, usando um perfil técnico do emissor SAML:

- [Registre um aplicativo SAML no Azure AD B2C](connect-with-saml-service-providers.md)












