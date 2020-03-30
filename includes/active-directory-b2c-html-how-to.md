---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116973"
---
## <a name="use-custom-page-content"></a>Use o conteúdo da página personalizada

Usando o recurso de personalização da interface do usuário da página, você pode personalizar a aparência de qualquer política personalizada. Também pode manter a consistência visual e da marca entre seu aplicativo e o Azure AD B2C.

### <a name="how-it-works"></a>Como ele funciona

O Azure AD B2C executa código no navegador do seu cliente usando [o Cors (Cross-Origin Resource Sharing, compartilhamento de recursos de origem cruzada).](https://www.w3.org/TR/cors/) Em tempo de execução, o conteúdo é carregado a partir de uma URL que você especifica no fluxo de usuário ou na política personalizada. Cada página na experiência do usuário carrega seu conteúdo a partir da URL especificada para essa página. Depois que o conteúdo é carregado a partir de sua URL, ele é mesclado com um fragmento HTML inserido pelo Azure AD B2C e, em seguida, a página é exibida para o seu cliente.

![Margem de conteúdo de página personalizada](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Conteúdo de página HTML personalizado

Crie uma página HTML com sua própria marca para servir o conteúdo da sua página personalizada. Esta página pode `*.html` ser uma página estática ou uma página dinâmica como .NET, Node.js ou PHP.

O conteúdo da página personalizada pode conter quaisquer elementos HTML, incluindo CSS e JavaScript, mas não pode incluir elementos inseguros como iframes. O único elemento necessário é `id` um `api`elemento div `<div id="api"></div>` com set para, como este dentro de sua página HTML.

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalize as páginas AD B2C padrão do Azure

Em vez de criar o conteúdo da página personalizada do zero, você pode personalizar o conteúdo padrão da página do Azure AD B2C.

A tabela a seguir lista o conteúdo da página padrão fornecida pelo Azure AD B2C. Baixe os arquivos e use-os como ponto de partida para criar suas próprias páginas personalizadas.

| Página padrão | Descrição | ID de definição de conteúdo<br/>(somente política personalizada) |
|:-----------------------|:--------|-------------|
| [exceção.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Essa página é exibida quando uma exceção ou um erro é encontrado. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Página auto-afirmada**. Use este arquivo como um conteúdo de página personalizado para uma página de inscrição de conta social, uma página de inscrição de conta local, uma página de login de conta local, redefinição de senha e muito mais. O formulário pode conter diferentes controles de entrada, por exemplo: caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla. | *api.localaccountsignin*, *api.localaccountsignup*, *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifator-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multifator**. Nesta página, os usuários podem verificar seus números de telefone (usando mensagem de texto ou por voz) durante a inscrição ou entrada. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfil**. Esta página contém um formulário que os usuários podem acessar para atualizar o perfil. Esta página é semelhante à página de inscrição de conta social, exceto os campos de entrada de senha. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou entrada unificada**. Esta página manipula o processo de inscrição e de entrada. Os usuários podem usar provedores de identidade corporativa, provedores de identidade social, como Facebook e Google+ ou contas locais. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hospedagem do conteúdo da página

Ao usar seus próprios arquivos HTML e CSS para personalizar a ida e volta, hospede seu conteúdo de IU em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento Azure Blob,](../articles/storage/blobs/storage-blobs-introduction.md) [Azure App Services,](/azure/app-service/)servidores web, CDNs, AWS S3 ou sistemas de compartilhamento de arquivos.

## <a name="guidelines-for-using-custom-page-content"></a>Diretrizes para o uso de conteúdo de página personalizado

- Use uma URL absoluta quando você incluir recursos externos como mídia, CSS e arquivos JavaScript em seu arquivo HTML.
- Usando a versão 1.2.0 e superior `data-preload="true"` do layout da [página,](../articles/active-directory-b2c/page-layout.md) você pode adicionar o atributo em suas tags HTML para controlar a ordem de carregamento para CSS e JavaScript. Com `data-preload=true`, a página é construída antes de ser mostrada ao usuário. Esse atributo ajuda a impedir que a página "cintile" pré-carregando o arquivo CSS, sem que o HTML não-estilizado seja mostrado ao usuário. O seguinte trecho de código HTML `data-preload` mostra o uso da tag.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Recomendamos que você comece com o conteúdo da página padrão e construa em cima dele.
- Você pode incluir JavaScript em seu conteúdo personalizado para [fluxos de usuário](../articles/active-directory-b2c/user-flow-javascript-overview.md) e [políticas personalizadas.](../articles/active-directory-b2c/javascript-samples.md)
- As versões de navegador compatíveis são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Compatibilidade limitada com Internet Explorer 9 e 8
  - Google Chrome 42.0 e superior
  - Mozilla Firefox 38.0 e superior
  - Safari para iOS e macOS, versão 12 e superior
- Devido a restrições de segurança, o Azure `frame`AD B2C não suporta `iframe`elementos HTML ou `form` HTML.

## <a name="custom-page-content-walkthrough"></a>Passo a passo do conteúdo da página personalizada

Aqui está uma visão geral do processo:

1. Prepare um local para hospedar o conteúdo da página personalizada (um ponto final HTTPS acessível ao público e habilitado para CORS).
1. Baixe e personalize um arquivo de `unified.html`conteúdo de página padrão, por exemplo.
1. Publique seu conteúdo de página personalizado seu ponto final HTTPS disponível publicamente.
1. Defina o CORS (compartilhamento de recurso entre origem) para seu aplicativo Web.
1. Aponte sua política para o uri de conteúdo de política personalizado.

### <a name="1-create-your-html-content"></a>1. Crie seu conteúdo HTML

Crie um conteúdo de página personalizado com a marca do seu produto no título.

1. Copie o snippet de HTML a seguir. É html5 bem formado com um elemento vazio chamado * \<div\>\<id="api" /div\> * localizado dentro das etiquetas do * \<corpo.\> * Esse elemento marca o local em que o conteúdo do Azure AD B2C será inserido.

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

### <a name="2-create-an-azure-blob-storage-account"></a>2. Crie uma conta de armazenamento Azure Blob

Neste artigo, usamos o Armazenamento de Blobs do Azure para hospedar nosso conteúdo. Você pode optar por hospedar seu conteúdo em um servidor Web, mas deve [habilitar CORS em seu servidor Web](https://enable-cors.org/server.html).

Para hospedar seu conteúdo HTML no armazenamento Blob, execute as seguintes etapas:

1. Faça login no [portal Azure](https://portal.azure.com).
1. No menu **Hub,** selecione **Nova conta** > **de armazenamento de****armazenamento** > .
1. Selecione uma **assinatura** para sua conta de armazenamento.
1. Crie um **grupo de recursos** ou selecione um já existente.
1. Digite um **Nome** exclusivo para sua conta de armazenamento.
1. Selecione a **localização geográfica** para sua conta de armazenamento.
1. O **Modelo de implantação** pode permanecer **Gerenciador de Recursos**.
1. O **Desempenho** pode permanecer **Padrão**.
1. Altere **Tipo de Conta** para **Armazenamento de blobs**.
1. A **Replicação** pode permanecer **RA-GRS**.
1. A **Camada de acesso** pode permanecer **Dinâmica**.
1. Selecione **'Revisar + criar** para criar a conta de armazenamento'.
    Depois que a implantação é concluída, a página **da conta armazenamento** é aberta automaticamente.

#### <a name="21-create-a-container"></a>2.1 Criar um recipiente

Para criar um contêiner público no armazenamento Blob, execute as seguintes etapas:

1. Em **serviço Blob** no menu à esquerda, selecione **Blobs**.
1. Selecione **+Container**.
1. Para **Nome,** digite *raiz*. O nome pode ser um nome de sua escolha, por exemplo *contoso,* mas usamos *raiz* neste exemplo para simplicidade.
1. Para **nível de acesso público,** selecione **Blob,** depois **OK**.
1. Selecione **raiz** para abrir o novo recipiente.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Faça upload de arquivos de conteúdo de sua página personalizada

1. Selecione **Carregar**.
1. Selecione o ícone da pasta ao lado **de Selecionar um arquivo**.
1. Navegue e selecione **customize-ui.html**, que você criou anteriormente na seção de personalização de ICarros de página.
1. Se você quiser fazer upload para uma subpasta, expanda **Avançado** e insira um nome de pasta em **Upload to folder**.
1. Selecione **Carregar**.
1. Selecione o **blob customize-ui.html** que você carregou.
1. À direita da caixa de texto **URL,** selecione o ícone **Copiar para a área de transferência** para copiar a URL na área de transferência.
1. No navegador da Web, navegue até a URL que você copiou para verificar se a bolha que você carregou está acessível. Se ele estiver inacessível, por `ResourceNotFound` exemplo, se você encontrar um erro, certifique-se de que o tipo de acesso ao contêiner está definido como **blob**.

### <a name="3-configure-cors"></a>3. Configure cors

Configure o armazenamento Blob para compartilhamento de recursos cross-origin realizando as seguintes etapas:

1. No menu, selecione **CORS**.
1. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao inserir o nome do seu inquilino.
1. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
1. Para **cabeçalhos permitidos**, digite um asterisco (*).
1. Para **cabeçalhos expostos**, digite um asterisco (*).
1. Para **Idade máxima de**, insira 200.
1. Selecione **Salvar**.

#### <a name="31-test-cors"></a>3.1 Teste CORS

Valide se você está pronto realizando as seguintes etapas:

1. Repita a etapa cors configurada. Para **origens permitidas,** digite`https://www.test-cors.org`
1. Navegue até [www.test-cors.org](https://www.test-cors.org/) 
1. Para a caixa **URL remota,** cole a URL do seu arquivo HTML. Por exemplo, `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. Selecione **Enviar solicitação**.
    O resultado `XHR status: 200`deve ser . 
    Se você receber um erro, verifique se as Configurações do CORS estão corretas. Você também pode precisar limpar o cache do navegador ou abrir uma sessão de navegação particular pressionando Ctrl+Shift+P.
