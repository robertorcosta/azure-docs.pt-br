---
title: Personalize a interface do usuário do seu aplicativo com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba mais sobre como personalizar uma interface do usuário usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e50d6d0623e87dfa68a7cc9744c3f595ff0179c6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396375"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao completar as etapas deste artigo, você cria uma política personalizada de inscrição e login com sua marca e aparência. Com o Azure Active Directory B2C (Azure AD B2C), você obtém controle quase total do conteúdo HTML e CSS apresentado aos usuários. Ao usar uma política personalizada, a personalização da interface do usuário é configurada em XML em vez de usar controles no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Modifique o arquivo de extensões

Para configurar a personalização da interface do ui, copie o **ContentDefinition** e seus elementos filho do arquivo base para o arquivo de extensões.

1. Abra o arquivo base da sua política. Por exemplo, <em> `SocialAndLocalAccounts/` </em>. Este arquivo base é um dos arquivos de diretiva incluídos no pacote de inicialização de políticas personalizadas, que você deve ter obtido no pré-requisito, [Comece com políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Pesquise e copie todo o conteúdo do elemento **ContentDefinitions**.
1. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions.xml*. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
1. Cole todo o conteúdo do elemento **ContentDefinitions** que você copiou como filho do elemento **BuildingBlocks**.
1. Pesquise o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML copiado.
1. Altere o valor de **LoadUri** para a URL do arquivo HTML que você carregou no armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Sua política personalizada deve parecer com o seguinte trecho de código:

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

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Faça upload e teste sua política personalizada atualizada

#### <a name="51-upload-the-custom-policy"></a>5.1 Faça upload da política personalizada

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Procure e selecione **Azure AD B2C**.
1. Em **Políticas,** selecione **Identity Experience Framework**.
1. Selecione **Enviar política personalizada**.
1. Carregue o arquivo de extensões que você alterou anteriormente.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Teste a política personalizada usando **executar agora**

1. Selecione a diretiva que você carregou e selecione **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Configure o uri de conteúdo de página personalizado dinâmico

Usando políticas personalizadas Azure AD B2C, você pode enviar um parâmetro no caminho da URL ou uma seqüência de consultas. Passando o parâmetro para seu ponto de extremidade HTML, é possível alterar dinamicamente o conteúdo da página. Por exemplo, é possível alterar a imagem de tela de fundo na página de inscrição ou de entrada do Azure AD B2C, com base em um parâmetro passado do seu aplicativo Web ou móvel. O parâmetro pode ser qualquer [resolução de sinistro,](claim-resolver-overview.md)como o ID do aplicativo, o `campaignId`ID do idioma ou o parâmetro de seqüência de consulta personalizada, tais como .

### <a name="sending-query-string-parameters"></a>Enviando parâmetros de seqüência de consulta

Para enviar parâmetros de seqüência de consultas, na [política de parte de confiar,](relyingparty.md)adicione um `ContentDefinitionParameters` elemento conforme mostrado abaixo.

```XML
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

Na sua definição de conteúdo, altere o valor de `LoadUri` . `https://<app_name>.azurewebsites.net/home/unified` Sua política `ContentDefinition` personalizada deve parecer com o seguinte trecho de código:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quando o Azure AD B2C carrega a página, ele faz uma chamada para o ponto final do servidor web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Uri de conteúdo de página dinâmica

O conteúdo pode ser extraído de diferentes lugares com base nos parâmetros utilizados. No ponto final habilitado para CORS, configure uma estrutura de pasta para hospedar conteúdo. Por exemplo, você pode organizar o conteúdo na seguinte estrutura. Pasta *raiz/pasta por idioma/seus arquivos html*. Por exemplo, sua página personalizada URI pode parecer:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C envia as duas letras `fr` código ISO para o idioma, para francês:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre elementos de IA que podem ser personalizados, consulte [o guia de referência para personalização da ia de](customize-ui-overview.md)usuário para fluxos de usuário .
