---
title: Personalizar a interface do usuário
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface do usuário para seus aplicativos que usam o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ddf57057b736cd76a74276e5593a865e7df8cc
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666864"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalize a interface do usuário no Azure Active Directory B2C

Branding e personalização da interface de usuário que o Azure Active Directory B2C (Azure AD B2C) exibe para seus clientes ajuda a fornecer uma experiência de usuário perfeita em seu aplicativo. Essas experiências incluem se inscrever, fazer login, edição de perfil e redefinição de senha. Este artigo introduz os métodos de personalização da interface do usuário (UI) tanto para fluxos de usuário quanto políticas personalizadas.

## <a name="ui-customization-in-different-scenarios"></a>Personalização da IU em diferentes cenários

Existem várias maneiras de personalizar a interface do usuário das experiências do seu aplicativo, cada uma apropriada para diferentes cenários.

### <a name="user-flows"></a>Fluxos de usuário

Se você usar [fluxos](user-flow-overview.md)de usuário, você pode alterar a aparência de suas páginas de fluxo de usuário usando modelos de layout de *página incorporados,* ou usando seu próprio HTML e CSS. Ambos os métodos são discutidos posteriormente neste artigo.

Você usa o [portal Azure](tutorial-customize-ui.md) para configurar a personalização da IU para fluxos de usuário.

> [!TIP]
> Se você quiser modificar apenas o logotipo do banner, a imagem de fundo e a cor de fundo das páginas de fluxo do usuário, você pode experimentar o recurso [de marca da empresa (visualização)](#company-branding-preview) descrito mais tarde neste artigo.

### <a name="custom-policies"></a>Políticas personalizadas

Se você estiver usando [políticas personalizadas](custom-policy-overview.md) para fornecer login ou login, redefinição de senha ou edição de perfil em seu aplicativo, use [arquivos de diretiva para personalizar a interface do usuário](custom-policy-ui-customization.md).

Se você precisar fornecer conteúdo dinâmico com base na decisão de um cliente, use políticas personalizadas que podem [alterar o conteúdo da página dinamicamente](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) dependendo de um parâmetro enviado em uma seqüência de consultas. Por exemplo, você pode alterar a imagem de fundo na página de inscrição ou login do Azure AD B2C com base em um parâmetro que você passa do seu aplicativo web ou móvel.

### <a name="javascript"></a>JavaScript

Você pode habilitar o código JavaScript do lado do cliente em [fluxos de usuário](user-flow-javascript-overview.md) e [políticas personalizadas.](page-layout.md)

### <a name="sign-in-only-ui-customization"></a>Faça a personalização de iu somente em iCarros

Se você estiver fornecendo apenas login, juntamente com sua página de redefinição de senha e e-mails de verificação, use as mesmas etapas de personalização que são usadas para uma [página de login azure AD](../active-directory/fundamentals/customize-branding.md).

Se os clientes tentarem editar seu perfil antes de fazer login, eles serão redirecionados para uma página que você personaliza usando as mesmas etapas usadas para personalizar a página de login do Azure AD.

## <a name="page-layout-templates"></a>Modelos de layout de página

Os fluxos de usuário fornecem vários modelos incorporados que você pode escolher para dar às páginas de experiência do usuário um visual profissional. Esses modelos de layout também podem e servem como ponto de partida para sua própria personalização.

Em **Personalizar no** menu à esquerda, selecione **Layouts de página** e, em seguida, selecione **Modelo**.

![Seleção de modelo saqueada na página de fluxo de usuário do portal Azure](media/customize-ui-overview/template-selection.png)

Em seguida, selecione um modelo da lista. Aqui estão exemplos das páginas de login para cada modelo:

| Azul-do-Oceano | Cinza ardósia | Clássico |
|:-:|:-:|:-:|
|![Exemplo do modelo Ocean Blue renderizado na página de inscrição](media/customize-ui-overview/template-ocean-blue.png)|![Exemplo do modelo Slate Gray renderizado na página de inscrição](media/customize-ui-overview/template-slate-gray.png)|![Exemplo do modelo Clássico renderizado na página de inscrição](media/customize-ui-overview/template-classic.png)|

Quando você escolhe um modelo, o layout selecionado é aplicado a todas as páginas em seu fluxo de usuário e o URI de cada página é visível no campo **URI da página personalizada**.

## <a name="custom-html-and-css"></a>HTML personalizado e CSS

Se você deseja projetar seu próprio layout de política com seu HTML e CSS personalizados, você pode fazê-lo alternando o alternância "Usar conteúdo de página personalizada" para cada um dos nomes de layout presentes em sua política. Siga as instruções abaixo sobre as configurações de layout personalizadas:

O Azure AD B2C executa código no navegador do seu cliente usando uma abordagem chamada [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/).

Em tempo de execução, o conteúdo é carregado a partir de uma URL que você especifica no fluxo de usuário ou na política personalizada. Cada página na experiência do usuário carrega seu conteúdo a partir da URL especificada para essa página. Depois que o conteúdo é carregado a partir de sua URL, ele é mesclado com um fragmento HTML inserido pelo Azure AD B2C e, em seguida, a página é exibida para o seu cliente.

Revise as seguintes orientações antes de usar seus próprios arquivos HTML e CSS para personalizar a ia:

- O Azure AD B2C **mescla** conteúdo HTML em suas páginas. Não copie nem tente alterar o conteúdo de padrão fornecido pelo Azure AD B2C. É melhor criar seu conteúdo HTML do zero e usar o conteúdo padrão como referência.
- **JavaScript** pode ser incluído em seu conteúdo personalizado para [fluxos de usuário](user-flow-javascript-overview.md) e [políticas personalizadas.](javascript-samples.md)
- As **versões** suportadas do navegador são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Compatibilidade limitada com Internet Explorer 9 e 8
  - Google Chrome 42.0 e superior
  - Mozilla Firefox 38.0 e superior
  - Safari para iOS e macOS, versão 12 e superior
- Não inclua **tags de formulário** em seu HTML. Tags de formulário interferem com as operações POST geradas pelo HTML injetado pelo Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Onde armazeno o conteúdo da interface do usuário?

Ao usar seus próprios arquivos HTML e CSS para personalizar a iu, você pode hospedar seu conteúdo de IU em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento Azure Blob,](../storage/blobs/storage-blobs-introduction.md)servidores web, CDNs, AWS S3 ou sistemas de compartilhamento de arquivos.

O ponto importante é que você hospeda o conteúdo em um ponto final HTTPS disponível [publicamente](https://enable-cors.org/server.html)com cors ativado . Você deve usar uma URL absoluta ao especificá-lo em seu conteúdo.

> [!NOTE]
> Para obter detalhes sobre a criação de conteúdo HTML, o upload de conteúdo para o armazenamento Do Azure Blob e a configuração do CORS, consulte a seção passo a passo de [conteúdo da página Personalizada](custom-policy-ui-customization.md#custom-page-content-walkthrough) no artigo de personalização da UI.

## <a name="get-started-with-custom-html-and-css"></a>Comece com HTML personalizado e CSS

Comece a usar seu próprio HTML e CSS em suas páginas de experiência do usuário seguindo essas diretrizes.

- Crie conteúdo HTML bem formado com um elemento `<div id="api"></div>` vazio localizado em algum lugar no `<body>`. Esse elemento marca onde o conteúdo do Azure AD B2C é inserido. O exemplo a seguir mostra uma página básica:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Use CSS para definir o estilo para os elementos de interface do usuário inseridos pelo Azure AD B2C na página. O exemplo a seguir mostra um arquivo CSS simples que também inclui configurações para os elementos HTML injetados da inscrição:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Hospede seu conteúdo em um ponto de extremidade HTTPS (com CORS permitido). Os métodos de solicitação GET e OPTIONS precisam ser habilitados ao configurar o CORS.
- Crie ou edite um fluxo de usuário ou uma política personalizada para usar o conteúdo que você criou.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmentos HTML do Azure AD B2C

A tabela abaixo lista os fragmentos de HTML que o Azure AD B2C mescla ao elemento `<div id="api"></div>` localizado no seu conteúdo.

| Página inserida | Descrição do HTML |
| ------------- | ------------------- |
| Seleção do provedor de identidade | Contém uma lista de botões para provedores de identidade na qual o cliente pode fazer a seleção durante a inscrição ou a entrada. Esses botões incluem provedores de identidade social como Facebook, Google ou contas locais (baseadas em endereço de email ou nome de usuário). |
| Inscrição da conta local | Contém um formulário para inscrições de conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| Inscrição de conta social | Pode aparecer ao se inscrever usando uma conta existente de um provedor de identidade social, como o Facebook ou Google. É usado quando informações adicionais devem ser coletadas do cliente usando um formulário de inscrição. |
| Inscrição ou entrada unificada | Controla tanto a inscrição quanto a entrada de clientes, que podem usar provedores de identidade social como Facebook, Google ou contas locais. |
| Autenticação multifator | Os clientes podem verificar seus números de telefone (usando mensagem de texto ou de voz) durante a inscrição ou entrada. |
| Erro | Fornece informações de erro para o cliente. |

## <a name="company-branding-preview"></a>Marca da empresa (visualização)

Você pode personalizar suas páginas de fluxo de usuário com um logotipo de banner, imagem de fundo e cor de fundo usando a marca Azure Active Directory [Company](../active-directory/fundamentals/customize-branding.md).

Para personalizar as páginas de fluxo do usuário, primeiro configure a marca da empresa no Azure Active Directory e, em seguida, habilite-a nos layouts de página dos fluxos de usuário no Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Configurar identidade visual da empresa

Comece definindo o logotipo do banner, a imagem de fundo e a cor de fundo dentro **da marca da empresa.**

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Gerenciar,** selecione **Marca da empresa**.
1. Siga as etapas em [Adicionar marca à página de login do Azure Active Directory da sua organização](../active-directory/fundamentals/customize-branding.md).

Tenha essas coisas em mente ao configurar a marca da empresa no Azure AD B2C:

* A marca da empresa no Azure AD B2C está atualmente limitada à **imagem de fundo,** **logotipo do banner**e personalização de cores de **fundo.** As outras propriedades no painel de marca da empresa, por exemplo, as de **configurações Avançadas,** não são *suportadas.*
* Nas páginas de fluxo do usuário, a cor de fundo é mostrada antes da imagem de fundo ser carregada. Recomendamos que você escolha uma cor de fundo que corresponda de perto às cores da sua imagem de fundo para uma experiência de carregamento mais suave.
* O logotipo do banner aparece nos e-mails de verificação enviados aos seus usuários quando eles iniciam um fluxo de usuário de inscrição.

### <a name="enable-branding-in-user-flow-pages"></a>Habilite a marca em páginas de fluxo de usuário

Depois de configurar a marca da empresa, habilite-a nos fluxos de usuário.

1. No menu esquerdo do portal Azure, selecione **Azure AD B2C**.
1. Em **Políticas,** selecione **Fluxos de usuário (políticas)**.
1. Selecione o fluxo de usuário para o qual você gostaria de ativar a marca da empresa. A marca da empresa não é **suportada** para os tipos de fluxo de usuário *Sign in v1* e *Profile.*
1. Em **Personalizar,** selecione **Layouts de**página e selecione o layout que deseja marcar. Por exemplo, selecione **'Inscreva-se' ou faça login na página**.
1. Para a **versão de layout de página (Preview)**, escolha a versão **1.2.0** ou superior.
1. Clique em **Salvar**.

Se você quiser marcar todas as páginas no fluxo de usuário, defina a versão de layout da página para cada layout de página no fluxo do usuário.

![Seleção de layout de página no Azure AD B2C no portal Azure](media/customize-ui-overview/portal-02-page-layout-select.png)

Este exemplo anotado mostra um logotipo de banner personalizado e uma imagem de fundo em uma página *de inscrição e login na* página de fluxo do usuário que usa o modelo Ocean Blue:

![Página de inscrição/login com marca servida pelo Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Use ativos de marca da empresa em HTML personalizado

Para usar os ativos de marca da sua `<div id="api">` empresa em HTML personalizado, adicione as seguintes tags fora da tag:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

A fonte de imagem é substituída pela imagem de fundo e logotipo do banner. Conforme descrito na seção [Iniciar com HTML personalizado e CSS,](#get-started-with-custom-html-and-css) use classes CSS para estilizar e posicionar os ativos na página .

## <a name="localize-content"></a>Localize o conteúdo

Você localiza seu conteúdo HTML habilitando a [personalização de idioma](user-flow-language-customization.md) em seu locatário do Azure AD B2C. A ativação deste recurso permite que o Azure AD `ui-locales` B2C encaminhe o parâmetro OpenID Connect para o seu ponto final. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML específicas a um idioma.

O conteúdo pode ser extraído de diferentes locais com base na localidade usada. No ponto de extremidade habilitado para CORS, você configura uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará adequadamente se usar o valor curinga `{Culture:RFC5646}`.

Por exemplo, sua página personalizada URI pode parecer:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Você pode carregar a página em francês puxando o conteúdo de:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exemplos

Você pode encontrar vários arquivos de modelo de exemplo no repositório [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) no GitHub.

Os arquivos HTML e CSS de exemplo nos modelos estão localizados no diretório [/sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Próximas etapas

- Se você estiver usando fluxos de **usuário,** você pode começar a personalizar sua iu com o tutorial:

    [Personalizar a interface do usuário dos aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md).
- Se você estiver usando **políticas personalizadas,** você pode começar a personalizar a iu com o artigo:

    [Personalize a interface de usuário do seu aplicativo usando uma política personalizada no Azure Active Directory B2C](custom-policy-ui-customization.md).
