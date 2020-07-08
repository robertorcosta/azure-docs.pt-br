---
title: Personalizar a interface do usuário do seu aplicativo com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba mais sobre como personalizar uma interface do usuário usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb833ff35dae4fe1c0c27204ec66fa6b4cdb82c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388877"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao concluir as etapas neste artigo, você cria uma política personalizada de inscrição e entrada com sua marca e aparência. Com o Azure Active Directory B2C (Azure AD B2C), você obtém controle quase total do conteúdo HTML e CSS apresentado aos usuários. Ao usar uma política personalizada, a personalização da interface do usuário é configurada em XML em vez de usar controles no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. modificar o arquivo de extensões

Para configurar a personalização da interface do usuário, copie o **ContentDefinition** e seus elementos filho do arquivo base para o arquivo de extensões.

1. Abra o arquivo base da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>. Esse arquivo base é um dos arquivos de política incluídos no pacote de início de política personalizada, que você deve ter obtido no pré-requisito, [introdução às políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Pesquise e copie todo o conteúdo do elemento **ContentDefinitions**.
1. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions.xml*. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
1. Cole todo o conteúdo do elemento **ContentDefinitions** que você copiou como um filho do elemento **BuildingBlocks** .
1. Pesquise o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML copiado.
1. Altere o valor de **LoadUri** para a URL do arquivo HTML que você carregou no armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Sua política personalizada deve ser semelhante ao seguinte trecho de código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salve o arquivo de extensões.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Carregue e teste sua política personalizada atualizada

#### <a name="51-upload-the-custom-policy"></a>5,1 carregar a política personalizada

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **Carregar política personalizada**.
1. Carregue o arquivo de extensões que você alterou anteriormente.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 testar a política personalizada usando **executar agora**

1. Selecione a política que você carregou e, em seguida, selecione **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurar URI de conteúdo de página personalizada dinâmica

Ao usar Azure AD B2C políticas personalizadas, você pode enviar um parâmetro no caminho da URL ou uma cadeia de caracteres de consulta. Passando o parâmetro para seu ponto de extremidade HTML, é possível alterar dinamicamente o conteúdo da página. Por exemplo, é possível alterar a imagem de tela de fundo na página de inscrição ou de entrada do Azure AD B2C, com base em um parâmetro passado do seu aplicativo Web ou móvel. O parâmetro pode ser qualquer [resolvedor de declaração](claim-resolver-overview.md), como a ID do aplicativo, ID de idioma ou parâmetro de cadeia de caracteres de consulta personalizada, como `campaignId` .

### <a name="sending-query-string-parameters"></a>Enviando parâmetros de cadeia de caracteres de consulta

Para enviar parâmetros de cadeia de caracteres de consulta, na política de terceira parte [confiável](relyingparty.md), adicione um `ContentDefinitionParameters` elemento, conforme mostrado abaixo.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Em sua definição de conteúdo, altere o valor de `LoadUri` para `https://<app_name>.azurewebsites.net/home/unified` . Sua política personalizada `ContentDefinition` deve ser semelhante ao seguinte trecho de código:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quando Azure AD B2C carrega a página, ela faz uma chamada para o ponto de extremidade do servidor Web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI de conteúdo de página dinâmica

O conteúdo pode ser extraído de locais diferentes com base nos parâmetros usados. No ponto de extremidade habilitado para CORS, configure uma estrutura de pastas para hospedar conteúdo. Por exemplo, você pode organizar o conteúdo na estrutura a seguir. *Pasta/pasta raiz por idioma/seus arquivos HTML*. Por exemplo, o URI da página personalizada pode ser semelhante a:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C envia o código ISO de duas letras para a linguagem, `fr` para francês:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre elementos de interface do usuário que podem ser personalizados, consulte o [Guia de referência para a personalização da interface do usuário para fluxos de usuários](customize-ui-overview.md).
