---
title: Adicionar identidade visual à página de entrada da sua organização
titleSuffix: Azure AD B2C
description: Saiba como adicionar a identidade visual da sua organização às páginas Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111113"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Adicionar identidade visual às páginas de Azure Active Directory B2C da sua organização

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Você pode personalizar suas páginas de Azure AD B2C com um logotipo de faixa, uma imagem de tela de fundo e uma cor de plano de fundo usando Azure Active Directory [identidade visual da empresa](../active-directory/fundamentals/customize-branding.md). A identidade visual da empresa inclui a inscrição, a entrada, a edição de perfil e a redefinição de senha. 

> [!TIP]
> Para personalizar outros aspectos de suas páginas de fluxo de usuário além do logotipo de faixa, imagem de tela de fundo ou cor da tela de fundo, consulte como [Personalizar a interface do usuário com o modelo HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Para personalizar suas páginas de fluxo de usuário, primeiro configure a identidade visual da empresa no Azure Active Directory, em seguida, habilite-a nos layouts de página de seus fluxos de usuário no Azure AD B2C.

## <a name="configure-company-branding"></a>Configurar identidade visual da empresa

Comece definindo o logotipo de faixa, a imagem de tela de fundo e a cor da tela de fundo dentro **da identidade visual da empresa**.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **gerenciar**, selecione **identidade visual da empresa**.
1. Siga as etapas em [Adicionar identidade visual à página de entrada Azure Active Directory de sua organização](../active-directory/fundamentals/customize-branding.md).

Lembre-se destas coisas ao configurar a identidade visual da empresa no Azure AD B2C:

* A identidade visual da empresa no Azure AD B2C está limitada atualmente à **imagem de plano de fundo**, ao logotipo da **faixa** e à personalização da cor da **tela de fundo** . As outras propriedades no painel de identidade visual da empresa, por exemplo, em **Configurações avançadas**, *não têm suporte*.
* Nas páginas de fluxo do usuário, a cor do plano de fundo é mostrada antes da imagem de plano de fundo ser carregada. Recomendamos que você escolha uma cor de plano de fundo que corresponda melhor às cores na sua imagem de plano de fundo para uma experiência de carregamento mais suave.
* O logotipo de faixa aparece nos emails de verificação enviados aos usuários quando eles iniciam um fluxo de usuário de inscrição.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Habilitar identidade visual em páginas de fluxo do usuário

Depois de configurar a identidade visual da empresa, habilite-a em seus fluxos de usuário.

1. No menu à esquerda da portal do Azure, selecione **Azure ad B2C**.
1. Em **Políticas**, selecione **Fluxos de usuários (políticas)** .
1. Selecione o fluxo de usuário para o qual você deseja habilitar a identidade visual da empresa. A identidade visual da empresa **não tem suporte** para a *entrada* padrão e os tipos de fluxo de usuário de *edição de perfil* padrão.
1. Em **Personalizar**, selecione **layouts de página** e, em seguida, selecione o layout que você deseja marcar. Por exemplo, selecione **página de inscrição ou inscrição unificada**.
1. Para a **versão de layout de página (visualização)**, escolha a versão **1.2.0** ou superior.
1. Clique em **Salvar**.

Se você quiser marcar todas as páginas no fluxo do usuário, defina a versão de layout da página para cada layout de página no fluxo do usuário.

![Seleção de layout de página em Azure AD B2C no portal do Azure](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Selecionar um layout de página

Para habilitar a identidade visual da empresa, você precisa [definir uma versão de layout de página](contentdefinitions.md#migrating-to-page-layout) com a `contract` versão de página para *todas* as definições de conteúdo em sua política personalizada. O formato do valor deve conter a palavra `contract` : _urn: com: Microsoft: AAD: B2C: elements:page-Name: Version_. Para especificar um layout de página em suas políticas personalizadas que usam um valor antigo de **DataUri** .

Saiba como [migrar para o layout da página](contentdefinitions.md#migrating-to-page-layout) com a versão da página.

O exemplo a seguir mostra os identificadores de definição de conteúdo e o **DataUri** correspondente com o contrato de página: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

O exemplo a seguir mostra um logotipo de faixa personalizado e uma imagem de tela de fundo em uma página de *entrada e* entrada de fluxo de usuário que usa o modelo azul do oceano:

![Página de inscrição/entrada com marca fornecida por Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Usar ativos de identidade visual da empresa em HTML personalizado

Para usar os ativos de identidade visual da empresa em [HTML personalizado](customize-ui-with-html.md), adicione as seguintes marcas fora da `<div id="api">` marca:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

A origem da imagem é substituída pela imagem de plano de fundo e pelo logotipo de faixa.

## <a name="next-steps"></a>Próximas etapas

Encontre mais informações sobre como você pode personalizar a interface do usuário de seus aplicativos em [Personalizar a interface do usuário do seu aplicativo no Azure Active Directory B2C](customize-ui-with-html.md).