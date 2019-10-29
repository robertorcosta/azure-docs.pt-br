---
title: Criar uma função disparada por HTTP no Azure
description: Aprenda a criar sua primeira função Python no Azure usando o Azure Functions Core Tools e a CLI do Azure.
author: ggailey777
ms.author: glenga
ms.date: 09/11/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: f2602e5a13f83090291656e7062c74c245bc6568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693345"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Criar uma função disparada por HTTP no Azure

Este artigo mostra como usar ferramentas de linha de comando para criar um projeto do Python executado no Azure Functions. Também é possível criar uma função disparada por uma solicitação HTTP. Por fim, você publicará seu projeto para ser executado como uma [função sem servidor](functions-scale.md#consumption-plan) no Azure.

Este artigo é o primeiro de dois inícios rápidos do Python para o Azure Functions. Depois de concluir este início rápido, você poderá [adicionar uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-python.md) à sua função.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é necessário:

+ Instale o [Python 3.6.8](https://www.python.org/downloads/). Essa versão do Python é verificada com o Functions. Ainda não há suporte para a versão 3.7 e versões posteriores.

+ Instale o [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.7.1575 ou posterior.

+ Instalar a [CLI do Azure](/cli/azure/install-azure-cli) versão 2.x ou posterior.

+ Ter uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment-optional"></a>Criar e ativar um ambiente virtual (opcional)

Você deve usar um ambiente Python 3.6.x para desenvolver localmente funções do Python. Execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv`.

> [!NOTE]
> Se o Python não instalou o venv em sua distribuição do Linux, você pode instalá-lo usando o seguinte comando:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash:

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell ou um prompt de comando do Windows:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Agora que você ativou o ambiente virtual, execute os comandos restantes nele. Para sair do ambiente virtual, execute `deactivate`.

## <a name="create-a-local-functions-project"></a>Criar um projeto local do Functions

Um projeto do Functions é o equivalente a um aplicativo de funções no Azure. Ele pode conter várias funções que compartilham as mesmas configurações locais e de hospedagem.

1. No ambiente virtual, execute o seguinte comando:

    ```console
    func init MyFunctionProj
    ```

1. Selecione **python** como seu runtime de trabalho.

    O comando cria uma pasta _MyFunctionProj_. Ela contém estes três arquivos:

    * *local.settings.json*: usado para armazenar as configurações do aplicativo e as cadeias de conexão durante a execução local. Esse arquivo não é publicado no Azure.
    * *requirements.txt*: contém a lista de pacotes que o sistema instalará na publicação no Azure.
    * *host.json*: contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Esse arquivo é publicado do Azure.

1. Navegue até a nova pasta *MyFunctionProj*:

    ```console
    cd MyFunctionProj
    ```

## <a name="create-a-function"></a>Criar uma função

Adicionar uma função ao novo projeto.

1. Para adicionar uma função ao projeto, execute o seguinte comando:

    ```console
    func new
    ```

1. Use a seta para baixo para selecionar o modelo do **gatilho HTTP**.

1. Quando for solicitado um nome de função, digite *HttpTrigger* e pressione Enter.

Esses comandos criam uma subpasta chamada _HttpTrigger_. Ele contém os seguintes arquivos:

* *function.json*: arquivo de configuração que define a função, o gatilho e outras associações. Veja que, nesse arquivo, o valor de `scriptFile` aponta para o arquivo que contém a função, enquanto o gatilho de invocação e as associações são definidos na matriz `bindings`.

    Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*: arquivo de script que é a função disparada por HTTP. Observe que esse script tem um `main()` padrão. Os dados HTTP do gatilho passam para a função usando o `req` chamado `binding parameter`. O `req`, que é definido em function.json, é uma instância da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    O objeto de retorno, definido como `$return` em *function.json*, é uma instância da [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Executar a função localmente

A função é executada localmente usando o Azure Functions Runtime.

1. Esse comando inicia o aplicativo de funções:

    ```console
    func host start
    ```

    Quando o host do Azure Functions é iniciado, ele escreve algo parecido com a seguinte saída. Ele está truncado aqui para que você possa ler melhor:

    ```output
    
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %
    
    ...
    
    Content root path: C:\functions\MyFunctionProj
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    ...
    
    Http Functions:
    
            HttpTrigger: http://localhost:7071/api/HttpTrigger
    
    [8/27/2018 10:38:27 PM] Host started (29486ms)
    [8/27/2018 10:38:27 PM] Job host started
    ```

1. Copie a URL da função `HttpTrigger` da saída do tempo de execução de função e cole-a na barra de endereços do navegador.

1. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. A captura de tela a seguir mostra a resposta no navegador à solicitação GET retornada pela função local:

    ![Verificar localmente no navegador](./media/functions-create-first-function-python/function-test-local-browser.png)

1. Selecione CTRL + C para desligar seu aplicativo de funções.

Agora que você executou a função localmente, poderá criar o aplicativo de funções e outros recursos necessários no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Criar um aplicativo de funções no Azure

Um aplicativo de funções fornece um ambiente para execução do código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Execute o comando a seguir. Substitua `<APP_NAME>` por um nome de aplicativo de funções exclusivo. Substitua `<STORAGE_NAME>` pelo nome da conta de armazenamento. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. O nome precisa ser exclusivo em todos os aplicativos no Azure.

> [!NOTE]
> Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `myResourceGroup` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

O comando anterior também provisiona uma instância do Aplicativo Azure insights associada no mesmo grupo de recursos. Você pode usar essa instância para monitorar seu aplicativo de funções e exibir logs.

Agora você está pronto para publicar seu projeto de funções local no aplicativo de funções no Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implantar o projeto de aplicativo de funções no Azure

Depois que o aplicativo de funções for criado no Azure, você poderá usar o comando do Core Tools [func azure functionapp publish](functions-run-local.md#project-file-deployment) para implantar o código do projeto no Azure. Nesse exemplo, substitua `<APP_NAME>` pelo nome de seu aplicativo.

```console
func azure functionapp publish <APP_NAME> --build remote
```

A opção `--build remote` cria seu projeto do Python remotamente no Azure usando os arquivos no pacote de implantação. 

Você verá uma saída semelhante ao exemplo a seguir. Ele está truncado aqui para que você possa ler melhor:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Você pode copiar o valor `Invoke url` para seu `HttpTrigger` e usá-lo para verificar sua função no Azure. A URL contém um valor de cadeia de caracteres de consulta `code` que é sua tecla de função, o que dificulta para outras pessoas chamarem seu ponto de extremidade de gatilho HTTP no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Para exibir logs quase em tempo real para um aplicativo do Python publicado, use o [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="next-steps"></a>Próximas etapas

Você criou um projeto de funções do Python com uma função disparada por HTTP, executou-a no computador local e implantou-a no Azure. Agora, estenda sua função por meio da...

> [!div class="nextstepaction"]
> [Adição de uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-python.md)
