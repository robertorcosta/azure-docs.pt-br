---
title: Desabilitar a verificação de email durante a inscrição do cliente com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba como desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136135"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Desabilitar a verificação de email durante a inscrição do cliente usando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada de trabalho para inscrever-se e entrar com contas sociais e locais.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicionar os metadados ao perfil técnico autodeclarado

O perfil técnico do **LocalAccountSignUpWithLogonEmail** é uma [declaração automática](self-asserted-technical-profile.md), que é invocada durante o fluxo de inscrição. Para desabilitar a verificação de email, defina os metadados de `EnforceEmailVerification` como false. Substitua os perfis técnicos do LocalAccountSignUpWithLogonEmail no arquivo de extensão. 

1. Abra o arquivo de extensões da política. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>.
1. Localize o elemento `ClaimsProviders`. Se o elemento não existir, adicione-o.
1. Adicione o seguinte provedor de declarações ao elemento `ClaimsProviders`:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário do Azure AD.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Carregar política personalizada** e carregue os dois arquivos de política alterados.
2. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
3. Você deve ser capaz de se inscrever usando um endereço de email sem a validação.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [perfil técnico autodeclarado](self-asserted-technical-profile.md) na referência do IEF.
