---
title: Personalizar a interface do usuário
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface do usuário para seus aplicativos que usam Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c0b6ba4045e1e92d395752f106258d3c0709108
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486708"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário no Azure Active Directory B2C

A identidade visual e a personalização da interface do usuário que o Azure Active Directory B2C (Azure AD B2C) exibe para seus clientes ajuda a fornecer uma experiência de usuário direta em seu aplicativo. Essas experiências incluem a inscrição, a entrada, a edição de perfil e a redefinição de senha. Este artigo apresenta os métodos de personalização da interface do usuário para fluxos de usuário e políticas personalizadas.

## <a name="ui-customization-in-different-scenarios"></a>Personalização da interface do usuário em cenários diferentes

Há várias maneiras de personalizar a interface do usuário de seu aplicativo, cada um apropriado para cenários diferentes.

### <a name="user-flows"></a>Fluxos de usuário

Se você usar [fluxos de usuário](user-flow-overview.md), poderá alterar a aparência de suas páginas de fluxo de usuário usando modelos de *layout de página*internos ou usando seu próprio HTML e CSS. Os dois métodos são discutidos posteriormente neste artigo.

Você usa o [portal do Azure](tutorial-customize-ui.md) para configurar a personalização da interface do usuário para fluxos de usuários.

> [!TIP]
> Se você quiser modificar apenas o logotipo de faixa, a imagem de tela de fundo e a cor da tela de fundo das suas páginas de fluxo de usuário, você poderá experimentar o recurso de [identidade visual da empresa (versão prévia)](#company-branding-preview) descrito posteriormente neste artigo.

### <a name="custom-policies"></a>Políticas personalizadas

Se você estiver usando [políticas personalizadas](custom-policy-overview.md) para fornecer inscrição ou entrada, redefinição de senha ou edição de perfil em seu aplicativo, use [arquivos de política para personalizar a interface do usuário](custom-policy-ui-customization.md).

Se você precisar fornecer conteúdo dinâmico com base na decisão de um cliente, use políticas personalizadas que possam [alterar o conteúdo da página dinamicamente](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) , dependendo de um parâmetro enviado em uma cadeia de caracteres de consulta. Por exemplo, você pode alterar a imagem de plano de fundo na página Azure AD B2C inscrição ou entrada com base em um parâmetro que você passa de seu aplicativo Web ou móvel.

### <a name="javascript"></a>JavaScript

Você pode habilitar o código JavaScript do lado do cliente tanto em [fluxos de usuário](user-flow-javascript-overview.md) quanto em [políticas personalizadas](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Personalização da interface do usuário somente de entrada

Se você estiver fornecendo somente entrada, junto com sua página de redefinição de senha e emails de verificação que o acompanham, use as mesmas etapas de personalização usadas para uma [página de entrada do Azure ad](../active-directory/fundamentals/customize-branding.md).

Se os clientes tentarem editar seu perfil antes de entrar, eles serão redirecionados para uma página que você personalizar usando as mesmas etapas que são usadas para personalizar a página de entrada do Azure AD.

## <a name="page-layout-templates"></a>Modelos de layout de página

Os fluxos de usuário fornecem vários modelos internos que você pode escolher para dar às suas páginas de experiência do usuário uma aparência profissional. Esses modelos de layout também podem e servem como ponto de partida para sua própria personalização.

Em **Personalizar** no menu à esquerda, selecione **layouts de página** e, em seguida, selecione **modelo**.

![Lista suspensa seleção de modelos na página fluxo do usuário do portal do Azure](media/customize-ui-overview/template-selection.png)

Em seguida, selecione um modelo na lista. Aqui estão exemplos das páginas de entrada para cada modelo:

| Azul-marinho | Cinza-acinzentado | Clássico |
|:-:|:-:|:-:|
|![Exemplo do modelo azul do oceano renderizado na página de entrada de inscrição](media/customize-ui-overview/template-ocean-blue.png)|![Exemplo do modelo cinza-acinzentado renderizado na página de entrada de inscrição](media/customize-ui-overview/template-slate-gray.png)|![Exemplo do modelo clássico renderizado na página de entrada de inscrição](media/customize-ui-overview/template-classic.png)|

Quando você escolhe um modelo, o layout selecionado é aplicado a todas as páginas em seu fluxo de usuário e o URI de cada página é visível no campo **URI da página personalizada**.

## <a name="custom-html-and-css"></a>HTML e CSS personalizados

Se você quiser projetar seu próprio layout de política com o HTML e o CSS personalizados, poderá fazer isso alternando a opção "usar conteúdo de página personalizado" para cada um dos nomes de layout presentes em sua política. Siga as instruções abaixo sobre as configurações de layout personalizado:

Azure AD B2C executa o código no navegador do cliente usando uma abordagem chamada [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).

No tempo de execução, o conteúdo é carregado de uma URL que você especifica em seu fluxo de usuário ou política personalizada. Cada página na experiência do usuário carrega seu conteúdo da URL que você especificar para essa página. Depois que o conteúdo é carregado de sua URL, ele é mesclado com um fragmento de HTML inserido por Azure AD B2C e, em seguida, a página é exibida para o cliente.

Examine as diretrizes a seguir antes de usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário:

- Azure AD B2C **mescla** o conteúdo HTML em suas páginas. Não copie nem tente alterar o conteúdo de padrão fornecido pelo Azure AD B2C. É melhor criar seu conteúdo HTML do zero e usar o conteúdo padrão como referência.
- O **JavaScript** pode ser incluído em seu conteúdo personalizado para [fluxos de usuário](user-flow-javascript-overview.md) e [políticas personalizadas](javascript-samples.md).
- **As versões de navegador** com suporte são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Compatibilidade limitada com Internet Explorer 9 e 8
  - Google Chrome 42.0 e superior
  - Mozilla Firefox 38.0 e superior
  - Safari para iOS e macOS, versão 12 e posterior
- Não inclua **marcas de formulário** em seu HTML. As marcas de formulário interferem com as operações de POSTAgem geradas pelo HTML injetadas por Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Onde armazeno o conteúdo da interface do usuário?

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, você pode hospedar o conteúdo da interface do usuário em qualquer ponto de extremidade HTTPS publicamente disponível que ofereça suporte a CORS. Por exemplo, [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md), servidores Web, CDNS, AWS S3 ou sistemas de compartilhamento de arquivos.

O ponto importante é que você hospeda o conteúdo em um ponto de extremidade HTTPS disponível publicamente com [CORS habilitado](https://enable-cors.org/server.html). Você deve usar uma URL absoluta ao especificá-lo em seu conteúdo.

> [!NOTE]
> Para obter detalhes sobre a criação de conteúdo HTML, o carregamento de conteúdo no armazenamento de BLOBs do Azure e a configuração do CORS, consulte a seção [instruções de conteúdo da página personalizada](custom-policy-ui-customization.md#custom-page-content-walkthrough) no artigo personalização da interface do usuário.

## <a name="get-started-with-custom-html-and-css"></a>Introdução ao HTML e CSS personalizados

Comece a usar seu próprio HTML e CSS em suas páginas de experiência do usuário seguindo estas diretrizes.

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

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmentos de HTML do Azure AD B2C

A tabela abaixo lista os fragmentos de HTML que o Azure AD B2C mescla ao elemento `<div id="api"></div>` localizado no seu conteúdo.

| Página inserida | Descrição do HTML |
| ------------- | ------------------- |
| Seleção do provedor de identidade | Contém uma lista de botões para provedores de identidade na qual o cliente pode fazer a seleção durante a inscrição ou a entrada. Esses botões incluem provedores de identidade social como Facebook, Google ou contas locais (baseadas em endereço de email ou nome de usuário). |
| Inscrição da conta local | Contém um formulário para inscrições de conta local baseada em um endereço de email ou um nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. |
| Inscrição de conta social | Pode aparecer ao se inscrever usando uma conta existente de um provedor de identidade social, como o Facebook ou Google. Ele é usado quando informações adicionais devem ser coletadas do cliente usando um formulário de inscrição. |
| Inscrição ou entrada unificada | Controla tanto a inscrição quanto a entrada de clientes, que podem usar provedores de identidade social como Facebook, Google ou contas locais. |
| Autenticação Multifator | Os clientes podem verificar seus números de telefone (usando mensagem de texto ou de voz) durante a inscrição ou entrada. |
| Erro | Fornece informações de erro para o cliente. |

## <a name="company-branding-preview"></a>Identidade visual da empresa (versão prévia)

Você pode personalizar suas páginas de fluxo de usuário com um logotipo de faixa, uma imagem de tela de fundo e uma cor de plano de fundo usando Azure Active Directory [identidade visual da empresa](../active-directory/fundamentals/customize-branding.md).

Para personalizar suas páginas de fluxo de usuário, primeiro configure a identidade visual da empresa no Azure Active Directory, em seguida, habilite-a nos layouts de página de seus fluxos de usuário no Azure AD B2C.

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>Configurar identidade visual da empresa

Comece definindo o logotipo de faixa, a imagem de tela de fundo e a cor da tela de fundo dentro **da identidade visual da empresa**.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **gerenciar**, selecione **identidade visual da empresa**.
1. Siga as etapas em [Adicionar identidade visual à página de entrada Azure Active Directory de sua organização](../active-directory/fundamentals/customize-branding.md).

Lembre-se destas coisas ao configurar a identidade visual da empresa no Azure AD B2C:

* A identidade visual da empresa no Azure AD B2C está limitada atualmente à **imagem de plano de fundo**, ao logotipo da **faixa**e à personalização da cor da **tela de fundo** . As outras propriedades no painel de identidade visual da empresa, por exemplo, em **Configurações avançadas**, *não têm suporte*.
* Nas páginas de fluxo do usuário, a cor do plano de fundo é mostrada antes da imagem de plano de fundo ser carregada. Recomendamos que você escolha uma cor de plano de fundo que corresponda melhor às cores na sua imagem de plano de fundo para uma experiência de carregamento mais suave.
* O logotipo de faixa aparece nos emails de verificação enviados aos usuários quando eles iniciam um fluxo de usuário de inscrição.

### <a name="enable-branding-in-user-flow-pages"></a>Habilitar identidade visual em páginas de fluxo do usuário

Depois de configurar a identidade visual da empresa, habilite-a em seus fluxos de usuário.

1. No menu à esquerda da portal do Azure, selecione **Azure ad B2C**.
1. Em **Políticas**, selecione **Fluxos de usuários (políticas)** .
1. Selecione o fluxo de usuário para o qual você deseja habilitar a identidade visual da empresa. A identidade visual da empresa **não tem suporte** para a *entrada* padrão e os tipos de fluxo de usuário de *edição de perfil* padrão.
1. Em **Personalizar**, selecione **layouts de página**e, em seguida, selecione o layout que você deseja marcar. Por exemplo, selecione **página de inscrição ou inscrição unificada**.
1. Para a **versão de layout de página (visualização)**, escolha a versão **1.2.0** ou superior.
1. Clique em **Salvar**.

Se você quiser marcar todas as páginas no fluxo do usuário, defina a versão de layout da página para cada layout de página no fluxo do usuário.

![Seleção de layout de página em Azure AD B2C no portal do Azure](media/customize-ui-overview/portal-02-page-layout-select.png)

Este exemplo anotado mostra um logotipo de faixa personalizado e uma imagem de tela de fundo em uma página de *entrada e* entrada de fluxo de usuário que usa o modelo azul do oceano:

![Página de inscrição/entrada com marca fornecida por Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Usar ativos de identidade visual da empresa em HTML personalizado

Para usar os ativos de identidade visual da empresa em HTML personalizado, adicione as seguintes marcas fora da `<div id="api">` marca:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

A origem da imagem é substituída pela imagem de plano de fundo e pelo logotipo de faixa. Conforme descrito na seção Introdução ao [HTML e CSS personalizados](#get-started-with-custom-html-and-css) , use classes CSS para estilizar e posicionar os ativos na página.

## <a name="localize-content"></a>Conteúdo de localização

Você localiza seu conteúdo HTML habilitando a [personalização de idioma](user-flow-language-customization.md) em seu locatário do Azure AD B2C. Habilitar esse recurso permite que Azure AD B2C encaminhe o parâmetro OpenID Connect `ui_locales` para seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML específicas a um idioma.

O conteúdo pode ser extraído de diferentes locais com base na localidade usada. No ponto de extremidade habilitado para CORS, você configura uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará adequadamente se usar o valor curinga `{Culture:RFC5646}`.

Por exemplo, o URI da página personalizada pode ser semelhante a:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Você pode carregar a página em francês Obtendo conteúdo de:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exemplos

Você pode encontrar vários arquivos de modelo de exemplo no repositório [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) no github.

Os arquivos HTML e CSS de exemplo nos modelos estão localizados no diretório [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Próximas etapas

- Se você estiver usando **fluxos de usuário**, você pode começar a personalizar sua interface de usuário com o tutorial:

    [Personalize a interface do usuário de seus aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md).
- Se você estiver usando **políticas personalizadas**, poderá começar a personalizar a interface do usuário com o artigo:

    [Personalize a interface do usuário do seu aplicativo usando uma política personalizada no Azure Active Directory B2C](custom-policy-ui-customization.md).
