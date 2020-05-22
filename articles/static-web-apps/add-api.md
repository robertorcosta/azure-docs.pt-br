---
title: Adicionar uma API a aplicativos Web Estáticos do Azure com o Azure Functions
description: Introdução aos Aplicativos Web Estáticos do Azure adicionando uma API sem servidor ao seu aplicativo Web estático usando o Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596145"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Adicionar uma API a aplicativos Web Estáticos do Azure com o Azure Functions

Você pode adicionar APIs sem servidor a aplicativos Web Estáticos do Azure por meio da integração com o Azure Functions. Este artigo demonstra como adicionar e implantar uma API em um site de Aplicativos Web Estáticos do Azure.

Para obter informações sobre como proteger as rotas da API, consulte o [guia de roteamento](routes.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão do Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code
- Extensão do [Código do Servidor do Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer).

## <a name="create-a-git-repository"></a>Criar um repositório Git 

As etapas a seguir demonstram como criar um novo repositório e clonar os arquivos para o computador.

1. Navegue até https://github.com/staticwebdev/vanilla-basic/generate para criar um novo repositório.
1. Na caixa _Nome do repositório_, insira **my-vanilla-api**.
1. Clique em **Criar repositório do modelo**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Criar um novo repositório do vanilla-basic":::

Depois que o projeto for criado, você poderá usar o Visual Studio Code para clonar o repositório Git.

1. Pressione **F1** para abrir o comando da Paleta de Comandos.
1. Cole a URL no arquivo no _Git: Clone_ o prompt e pressione **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Clonar um projeto do GitHub usando o Visual Studio Code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Clonar um projeto do GitHub usando o Visual Studio Code":::


## <a name="create-your-local-project"></a>Criar seu projeto local

Nesta seção, você usará o Visual Studio Code para criar um projeto local do Azure Functions. Posteriormente, você publica o aplicativo do Functions no Azure.

1. Dentro do projeto _my-vanilla-api_, crie uma subpasta chamada **api**.

   > [!NOTE]
   > Você pode dar a essa pasta qualquer nome. Este exemplo usa o `api`. 

2. Pressione **F1** para abrir a Paleta de Comandos
3. Digite **Azure Functions: Crie um Novo Projeto...**
4. Pressione **Enter**
5. Escolha **Procurar**
6. Selecione a pasta **API** como o diretório para o espaço de trabalho do projeto
7. Escolha **Selecionar**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Criar um novo Azure Functions usando o Visual Studio Code":::

8. Forneça as seguintes informações nos prompts:

    - _Selecione uma linguagem de programação para o seu projeto de função_: Escolha **JavaScript**
    - _Selecione um modelo para a primeira função do projeto_: Escolha o **gatilho HTTP**
    - _Forneça um nome de função_: Digite **GetMessage**
    - _Nível de autorização_: Escolha **Anônimo**, que permite que qualquer pessoa chame seu ponto de extremidade de função.
        - Para saber mais sobre os níveis de autorização, consulte [Chaves de autorização](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

9. Usando essas informações, o Visual Studio Code gera um projeto do Azure Functions com um gatilho HTTP.
    - Você pode exibir os arquivos do projeto local na janela do gerenciador do Visual Studio Code.
    - Para saber mais sobre os arquivos criados, confira [Arquivos de projeto gerados](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. Seu aplicativo agora deve ter uma estrutura de projeto semelhante a este exemplo.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Em seguida, atualize a função `GetMessage` em _api/GetMessage/index. js_ com o código a seguir.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Atualize a `GetMessage` configuração em `api/GetMessage/function.json` com as configurações a seguir.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
Com as configurações acima, o ponto de extremidade da API é:

- Disparo com uma solicitação HTTP é feito para a função
- Disponível para todas as solicitações, independentemente do status de autenticação
- Exposto por meio da rota _/api/message_ rota

## <a name="run-the-function-locally"></a>Executar a função localmente

O Visual Studio Code integra-se ao [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) para permitir que você execute esse projeto em seu computador de desenvolvimento local antes de publicar no Azure.

1. Execute a função pressionando **F5** para iniciar o aplicativo do Functions e a saída das ferramentas principais é exibida no painel _Terminal_.

2. Se o Azure Functions Core Tools ainda não estiver instalado, selecione **Instalar** no prompt.

    Quando o Core Tools for instalado, seu aplicativo será iniciado no painel _Terminal_. Como parte da saída, você pode ver o ponto de extremidade da URL de sua função disparada por HTTP localmente.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Criar um novo Azure Functions usando o Visual Studio Code":::

3. Com o Core Tools em execução, navegue até a URL a seguir para executar uma solicitação `GET`.

   <http://localhost:7071/api/message>

   Uma resposta é retornada, que é semelhante ao seguinte no navegador:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Criar um novo Azure Functions usando o Visual Studio Code":::

Depois de verificar se a função é executada corretamente, você pode chamar a API do aplicativo JavaScript.

### <a name="call-the-api-from-the-application"></a>Chamar a API do aplicativo

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Atualizar arquivos para acessar a API

1. Em seguida, atualize o conteúdo do arquivo _index. html_ com o seguinte código para buscar o texto da função da API e exibi-lo na tela:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Com as ferramentas principais em execução, use a extensão [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) do Visual Studio Code para fornecer o arquivo _index.html_ e abri-lo em um navegador. 

2. Pressione **F1** e escolha **Live Server: Abrir com Live Server**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Criar um novo Azure Functions usando o Visual Studio Code":::

   > [!NOTE]
   > Você pode usar outros servidores HTTP ou proxies para atender ao arquivo `index.html`. O acesso ao `index.html` do `file:///` não funcionará.

### <a name="commit-and-push-your-changes-to-github"></a>Confirmar e enviar por push suas alterações para o GitHub

Ao usar o Visual Studio Code, confirme e envie por push suas alterações para o repositório Git remoto.

1. Pressione **F1** para abrir a Paleta de Comandos
1. Digite **Git: Confirme tudo**
1. Adicione uma mensagem de confirmação
1. Digite no **Git: efetuar push**

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Criar um aplicativo estático no portal do Azure — tela 1":::

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquise **Aplicativos Web Estáticos**
1. Clique em **Aplicativos Web Estáticos (Versão Prévia)**
1. Clique em **Criar**
1. Selecione sua _Assinatura do Azure_.
1. Selecione ou crie um novo _Grupo de Recursos_
1. Nomeie o aplicativo **my-baunilha-api**.
1. Selecione a _Região_ mais próxima de você
1. Selecione o **SKU** _gratuito_
1. Clique no botão **Entrar com o GitHub** e autentique-se com o GitHub.
1. Selecione sua _Organização_ de preferência.
1. Selecione **my-vanilla-api** do menu suspenso do _Repositório_
1. Selecione **mestre** na lista suspensa _Branch_
1. Clique em **Avançar: Build >** para editar a configuração do build

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Criar um aplicativo estático no portal do Azure — tela 2":::

Em seguida, adicione os seguintes detalhes de build.

1. Insira **./** para o _Local do aplicativo_.

1. Insira a **API** na caixa da _localização de API_.

   Esse é p nome da pasta API criada na etapa anterior.
   
1. Limpe o valor padrão fora do _Local do artefato de aplicativo_, deixando a caixa vazia.

1. Clique em **Revisar + Criar**.

| Configuração | Descrição             | Obrigatório |
| -------- | ----------------------- |
|  Local do aplicativo | O local do código-fonte do aplicativo estático | Sim |
|  Local da API | O local do back-end da API. Isso aponta para o diretório raiz do projeto do Aplicativo Azure Functions | Não |
|  Local do artefato do aplicativo | O local da pasta de saída da compilação. Algumas estruturas JavaScript de front-end têm uma etapa de compilação que coloca os arquivos de produção em uma pasta. Essa configuração aponta para a pasta de saída da compilação. | Não |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Criar um aplicativo estático no portal do Azure — tela 3":::

1. Clique em **Criar**
1. Aguarde a conclusão da implantação (isso pode levar um minuto)
1. Navegue até `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Verifique se a compilação foi bem-sucedida

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="Fluxo de trabalho do GitHub":::

A API implantada estará disponível em `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>`.

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="Fluxo de trabalho do GitHub":::

Você também pode encontrar a URL do aplicativo no portal do Azure:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Acessar a URL do aplicativo estático no portal do Azure":::

Como alternativa, você pode acessar diretamente seu aplicativo Web Estático do Azure em `https://<STATIC_APP_NAME>.azurestaticapps.net` e verificar o resultado no navegador.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser manter esse aplicativo para uso adicional, poderá usar as etapas a seguir para excluir o aplicativo Web Estático do Azure e seus recursos relacionados.

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Na barra de pesquisa superior, digite **Grupos de recursos**
1. Clique em **Grupos de recursos** 
1. Selecione **myResourceGroup**
1. Na página _myResourceGroup_, certifique-se de que os recursos listados são aqueles que deseja excluir.
1. Selecione **Excluir**
1. Digite **myResourceGroup** na caixa de entrada
1. Selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Definir as configurações do aplicativo](./application-settings.md)
