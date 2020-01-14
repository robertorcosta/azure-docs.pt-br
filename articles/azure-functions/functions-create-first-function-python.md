---
title: Criar uma função Python disparada por HTTP no Azure
description: Aprenda a criar sua primeira função Python no Azure usando o Azure Functions Core Tools e a CLI do Azure.
ms.date: 11/07/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: bd20c55fdaef5e820773b4ff4e0ddf3d36c6e371
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433205"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Início Rápido: Criar uma função Python disparada por HTTP no Azure

Este artigo mostra como usar ferramentas de linha de comando para criar um projeto do Python executado no Azure Functions. Também é possível criar uma função disparada por uma solicitação HTTP. Depois de executar localmente, você publica seu projeto para ser executado como uma [função sem servidor](functions-scale.md#consumption-plan) no Azure. 

Este artigo é o primeiro de dois inícios rápidos do Python para o Azure Functions. Depois de concluir este início rápido, você poderá [adicionar uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-python.md) à sua função.

Há também uma [versão baseada no Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01) deste artigo.

## <a name="prerequisites"></a>Prerequisites

Antes de começar, é necessário:

+ Instale o [Python 3.7.4](https://www.python.org/downloads/). Essa versão do Python é verificada com o Functions. Ainda não há suporte para o Python 3.8 e versões posteriores.

+ Instale o [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.7.1846 ou posterior.

+ Instale a [CLI do Azure](/cli/azure/install-azure-cli) versão 2.0.76 ou uma versão posterior.

+ Ter uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Criar e ativar um ambiente virtual

Você deve usar um ambiente Python 3.7 para desenvolver localmente funções do Python. Execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv`.

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

Um projeto de funções pode ter várias funções que compartilham as mesmas configurações locais e de hospedagem.

No ambiente virtual, execute os seguintes comandos:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

O comando `func init` cria uma pasta _MyFunctionProj_. O projeto Python nesta pasta ainda não possui nenhuma função. Você as adicionará a seguir.

## <a name="create-a-function"></a>Criar uma função

Para adicionar uma função ao projeto, execute o seguinte comando:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Este comando cria uma subpasta chamada _HttpTrigger_, que contém os seguintes arquivos:

* *function.json*: arquivo de configuração que define a função, o gatilho e outras associações. Veja que, nesse arquivo, o valor de `scriptFile` aponta para o arquivo que contém a função, enquanto o gatilho de invocação e as associações são definidos na matriz `bindings`.

    Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook.md#output).

* *\_\_init\_\_.py*: arquivo de script que é a função disparada por HTTP. Observe que esse script tem um `main()` padrão. Os dados HTTP do gatilho passam para a função usando o `req` chamado `binding parameter`. O `req`, que é definido em function.json, é uma instância da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). 

    O objeto de retorno, definido como `$return` em *function.json*, é uma instância da [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](functions-bindings-http-webhook.md).

Agora você pode executar a nova função no seu computador local.

## <a name="run-the-function-locally"></a>Executar a função localmente

Este comando inicia o aplicativo de funções, usando o runtime do Azure Functions (func.exe):

```console
func host start
```

Você deve ver as seguintes informações gravadas na saída:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

Copie a URL da sua função `HttpTrigger` da saída e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. A captura de tela a seguir mostra a resposta no navegador à solicitação GET retornada pela função local:

![Verificar localmente no navegador](./media/functions-create-first-function-python/function-test-local-browser.png)

Use Ctrl+C para desligar a execução do aplicativo de funções.

Agora que você executou sua função localmente, pode implantar seu código de função no Azure.  
Antes de implantar seu aplicativo, você precisará criar alguns recursos do Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Criar um aplicativo de funções no Azure

Um aplicativo de funções fornece um ambiente para execução do código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. 

Execute o comando a seguir. Substitua `<APP_NAME>` por um nome de aplicativo de funções exclusivo. Substitua `<STORAGE_NAME>` pelo nome da conta de armazenamento. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. O nome precisa ser exclusivo em todos os aplicativos no Azure.

> [!NOTE]
> Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `myResourceGroup` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

O comando anterior cria um aplicativo de função, executando o Python 3.7.4. Ele também provisiona uma instância do Aplicativo Azure Insights associada no mesmo grupo de recursos. Você pode usar essa instância para monitorar seu aplicativo de funções e exibir logs. 

Agora você está pronto para publicar seu projeto de funções local no aplicativo de funções no Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implantar o projeto de aplicativo de funções no Azure

Depois que o aplicativo de funções for criado no Azure, você poderá usar o comando do Core Tools [func azure functionapp publish](functions-run-local.md#project-file-deployment) para implantar o código do projeto no Azure. Nesse exemplo, substitua `<APP_NAME>` pelo nome de seu aplicativo.

```console
func azure functionapp publish <APP_NAME>
```

Seu projeto do Python é criado remotamente no Azure usando os arquivos no pacote de implantação. 

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
