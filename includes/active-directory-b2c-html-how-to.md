---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/26/2020
ms.author: mimart
ms.openlocfilehash: 053349996e15dbc0f58f062ffa966d0d894f5e0d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188997"
---
## <a name="use-custom-page-content"></a>Usar conteúdo de página personalizado

Usando o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada. Também pode manter a consistência visual e da marca entre seu aplicativo e o Azure AD B2C.

### <a name="how-it-works"></a>Como ele funciona

Azure AD B2C executa o código no navegador do cliente usando o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/). No tempo de execução, o conteúdo é carregado de uma URL que você especifica em seu fluxo de usuário ou política personalizada. Cada página na experiência do usuário carrega seu conteúdo da URL que você especificar para essa página. Depois que o conteúdo é carregado de sua URL, ele é mesclado com um fragmento de HTML inserido por Azure AD B2C e, em seguida, a página é exibida para o cliente.

![Margem de conteúdo da página personalizada](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Conteúdo da página HTML personalizada

Crie uma página HTML com sua própria identidade visual para fornecer o conteúdo da página personalizada. Essa página pode ser uma página de `*.html` estática ou uma página dinâmica como .NET, Node. js ou PHP.

O conteúdo da página personalizada pode conter qualquer elemento HTML, incluindo CSS e JavaScript, mas não pode incluir elementos inseguros como iframes. O único elemento necessário é um elemento div com `id` definido como `api`, como este `<div id="api"></div>` na sua página HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalizar as páginas de Azure AD B2C padrão

Em vez de criar o conteúdo da página personalizada do zero, você pode personalizar o conteúdo da página padrão do Azure AD B2C.

A tabela a seguir lista o conteúdo de página padrão fornecido pelo Azure AD B2C. Baixe os arquivos e use-os como um ponto de partida para criar suas próprias páginas personalizadas.

| Página padrão | DESCRIÇÃO | ID de definição de conteúdo<br/>(somente política personalizada) |
|:-----------------------|:--------|-------------|
| [Exception. html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Página Autodeclarada**. Use esse arquivo como um conteúdo de página personalizado para uma página de inscrição de conta social, uma página de inscrição de conta local, uma página de entrada de conta local, redefinição de senha e muito mais. O formulário pode conter diferentes controles de entrada, por exemplo: caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. | *API. localaccountsignin*, *API. localaccountsignup* , *API. localaccountpasswordreset*, *API. selfasserted* |
| [Multifactor-1.0.0. html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator**. Nesta página, os usuários podem verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem acessar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto os campos de entrada de senha. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada**. Esta página manipula o processo de inscrição e de entrada. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hospedando o conteúdo da página

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, hospede o conteúdo da interface do usuário em qualquer ponto de extremidade HTTPS publicamente disponível que ofereça suporte a CORS. Por exemplo, [armazenamento de BLOBs do Azure](../articles/storage/blobs/storage-blobs-introduction.md), [serviços Azure apps](/azure/app-service/), servidores Web, CDNs, AWS S3 ou sistemas de compartilhamento de arquivos.

## <a name="guidelines-for-using-custom-page-content"></a>Diretrizes para usar o conteúdo da página personalizada

- Use uma URL absoluta quando você incluir recursos externos, como mídia, CSS e arquivos JavaScript em seu arquivo HTML.
- Usando o [layout de página versão](../articles/active-directory-b2c/page-layout.md) 1.2.0 e superior, você pode adicionar o atributo `data-preload="true"` em suas marcas HTML para controlar a ordem de carregamento para CSS e JavaScript. Com `data-preload=true`, a página é construída antes de ser exibida para o usuário. Esse atributo ajuda a impedir que a página "oscilação", precarregando o arquivo CSS, sem que o HTML com estilo seja mostrado ao usuário. O trecho de código HTML a seguir mostra o uso da marca `data-preload`.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Recomendamos que você comece com o conteúdo da página padrão e se baseie nele.
- Você pode incluir o JavaScript em seu conteúdo personalizado para [fluxos de usuário](../articles/active-directory-b2c/user-flow-javascript-overview.md) e [políticas personalizadas](../articles/active-directory-b2c/javascript-samples.md).
- As versões de navegador compatíveis são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Compatibilidade limitada com Internet Explorer 9 e 8
  - Google Chrome 42.0 e superior
  - Mozilla Firefox 38.0 e superior
- Devido a restrições de segurança, Azure AD B2C não dá suporte a `frame`, `iframe`ou `form` elementos HTML.

## <a name="custom-page-content-walkthrough"></a>Explicação do conteúdo da página personalizada

Aqui está uma visão geral do processo:

1. Prepare um local para hospedar o conteúdo da página personalizada (um ponto de extremidade HTTPS habilitado para CORS, acessível publicamente).
1. Baixe e personalize um arquivo de conteúdo de página padrão, por exemplo `unified.html`.
1. Publique seu conteúdo de página personalizada no ponto de extremidade HTTPS disponível publicamente.
1. Defina o CORS (compartilhamento de recurso entre origem) para seu aplicativo Web.
1. Aponte sua política para o URI de conteúdo da política personalizada.

### <a name="1-create-your-html-content"></a>1. criar seu conteúdo HTML

Crie um conteúdo de página personalizada com o nome da marca do produto no título.

1. Copie o snippet de HTML a seguir. É um HTML5 bem formado com um elemento vazio chamado *\<div id = "api"\>\</div\>* localizado dentro das marcas *\<body\>* . Esse elemento marca o local em que o conteúdo do Azure AD B2C será inserido.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Cole o snippet copiado em um editor de texto e salve o arquivo como *customize-ui.html*.

> [!NOTE]
> Os elementos de formulário HTML serão removidos devido a restrições de segurança se você usar login.microsoftonline.com. Se você quiser usar elementos de formulário HTML em seu conteúdo HTML personalizado, [use b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. criar uma conta de armazenamento de BLOBs do Azure

Neste artigo, usamos o Armazenamento de Blobs do Azure para hospedar nosso conteúdo. Você pode optar por hospedar seu conteúdo em um servidor Web, mas deve [habilitar CORS em seu servidor Web](https://enable-cors.org/server.html).

Para hospedar seu conteúdo HTML no armazenamento de BLOBs, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu **Hub**, selecione **Novo** > **Armazenamento** > **Conta de armazenamento**.
1. Selecione uma **Assinatura** para a conta de armazenamento.
1. Crie um **Grupo de recursos** ou selecione um existente.
1. Insira um **Nome** exclusivo para sua conta de armazenamento.
1. Selecione a **Localização geográfica** para sua conta de armazenamento.
1. O **Modelo de implantação** pode permanecer **Gerenciador de Recursos**.
1. O **Desempenho** pode permanecer **Padrão**.
1. Altere **Tipo de Conta** para **Armazenamento de blobs**.
1. A **Replicação** pode permanecer **RA-GRS**.
1. A **Camada de acesso** pode permanecer **Dinâmica**.
1. Selecione **examinar + criar** para criar a conta de armazenamento.
    Depois que a implantação for concluída, a página **conta de armazenamento** será aberta automaticamente.

#### <a name="21-create-a-container"></a>2,1 criar um contêiner

Para criar um contêiner público no armazenamento de BLOBs, execute as seguintes etapas:

1. Em **serviço blob** no menu à esquerda, selecione **BLOBs**.
1. Selecione **+ contêiner**.
1. Para **nome**, insira *raiz*. Isso pode ser um nome de sua escolha, por exemplo, *wingtiptoys*, mas usamos *root* neste exemplo para simplificar.
1. Para **nível de acesso público**, selecione **blob**e **OK**.
1. Selecione **raiz** para abrir o novo contêiner.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 carregar seus arquivos de conteúdo de página personalizada

1. Escolha **Carregar**.
1. Selecione o ícone de pasta ao lado de **selecionar um arquivo**.
1. Navegue até e selecione **Customize-UI. html**, que você criou anteriormente na seção personalização da interface do usuário da página.
1. Se você quiser carregar para uma subpasta, expanda **avançado** e insira um nome de pasta em **carregar para a pasta**.
1. Escolha **Carregar**.
1. Selecione o blob **Customize-UI. html** que você carregou.
1. À direita da caixa de texto **URL** , selecione o ícone **copiar para área de transferência** para copiar a URL para a área de transferência.
1. No navegador da Web, navegue até a URL que você copiou para verificar se o blob que você carregou está acessível. Se ele estiver inacessível, por exemplo, se você encontrar um erro de `ResourceNotFound`, verifique se o tipo de acesso do contêiner está definido como **blob**.

### <a name="3-configure-cors"></a>3. configurar CORS

Configure o armazenamento de BLOB para compartilhamento de recursos entre origens executando as seguintes etapas:

1. No menu, selecione **CORS**.
1. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao inserir o nome do locatário.
1. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
1. Para **cabeçalhos permitidos**, digite um asterisco (*).
1. Para **cabeçalhos expostos**, digite um asterisco (*).
1. Para **Idade máxima de**, insira 200.
1. Clique em **Salvar**.

#### <a name="31-test-cors"></a>CORS de teste 3,1

Valide se você está pronto executando as seguintes etapas:

1. Navegue até [www.Test-CORS.org](https://www.test-cors.org/) e cole a URL na caixa **URL remota** .
1. Selecione **Enviar solicitação**.
    Se você receber um erro, verifique se as configurações de CORS estão corretas. Você também pode precisar limpar o cache do navegador ou abrir uma sessão de navegação particular pressionando Ctrl+Shift+P.
