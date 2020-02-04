---
title: Criar uma função Python disparada por HTTP no Azure
description: Crie e implante código Python sem servidor na nuvem usando o Azure Functions.
ms.date: 01/15/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 452c5aeab5d2a1092cb7d338d37e26a82d92396e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845498"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Início Rápido: Criar uma função Python disparada por HTTP no Azure

Neste artigo, você usa ferramentas de linha de comando para criar uma função em Python que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions. A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Há também uma [versão baseada no Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01) deste artigo.

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- O [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.7.1846 ou posterior.
- A [CLI do Azure](/cli/azure/install-azure-cli) versão 2.0.76 ou posterior. 
- [Python 3.7.4 de 64 bits](https://www.python.org/downloads/release/python-374/). (O Python 3.7.4 é verificado com o Azure Functions; ainda não há suporte para o Python 3.8 e para versões posteriores.)

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

1. Em um terminal ou uma janela Comando, execute `func --version` para verificar se a versão do Azure Functions Core Tools é 2.7.1846 ou posterior.
1. Execute `az --version` para verificar se a versão da CLI do Azure é a 2.0.76 ou posterior.
1. Execute `az login` para entrar no Azure e verifique se a assinatura está ativa.
1. Execute `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar se a versão do Python é 3.7.x.

## <a name="create-and-activate-a-virtual-environment"></a>Criar e ativar um ambiente virtual

Em uma pasta adequada, execute os comandos a seguir para criar e ativar um ambiente virtual chamado `.venv`. Lembre-se de usar o Python 3.7, que é compatível com o Azure Functions.


# <a name="bashtabbash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se o Python não instalou o pacote venv na distribuição do Linux, execute o seguinte comando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Você executará todos os comandos posteriores neste ambiente virtual ativado. (Para sair do ambiente virtual, execute `deactivate`.)

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

No Azure Functions, um projeto de função é um contêiner para uma ou mais funções individuais que respondem, cada uma, a um gatilho específico. Todas as funções em um projeto compartilham as configurações locais e de hospedagem. Nesta seção, você cria um projeto de função que contém apenas uma função.

1. No ambiente virtual, execute o comando `func init` para criar um projeto de função em uma pasta chamada *LocalFunctionProj* com o runtime especificado:

    ```
    func init LocalFunctionProj --python
    ```
    
    Essa pasta contém vários arquivos do projeto, incluindo arquivos de configuração chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *local.settings.json* pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo *.gitignore*.

    > [!TIP]
    > Como um projeto de função está vinculado a um runtime específico, todas as funções no projeto precisam ser escritas com a mesma linguagem.

1. Navegue até a pasta do projeto:

    ```
    cd LocalFunctionProj
    ```
    
1. Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função e o argumento `--template` especifica o gatilho da função. `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de código apropriado para a linguagem escolhida do projeto, bem como um arquivo de configuração chamado *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examinar o conteúdo do arquivo

Se preferir, você poderá ir diretamente para [Executar a função localmente](#run-the-function-locally) e examinar o conteúdo do arquivo mais tarde.

### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* contém uma função de Python `main()` que é disparada de acordo com a configuração em *function.json*.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Para o gatilho HTTP, a função recebe dados de solicitação na variável `req`, conforme definido em *function.json*. `req` é uma instância da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *function.json*, é uma instância da [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](functions-bindings-http-webhook.md).

### <a name="functionjson"></a>function.json

*function.json* é um arquivo de configuração que define as `bindings` de entrada e de saída da função, incluindo o tipo de gatilho. Se preferir, você poderá alterar `scriptFile` para invocar um arquivo Python diferente.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook.md#trigger) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook.md#output).


## <a name="run-the-function-locally"></a>Executar a função localmente

Inicie a função iniciando o host de runtime local do Azure Functions na pasta *LocalFunctionProj*:

```
func start
```

A saída a seguir deve aparecer. (Se HttpExample não aparece conforme mostrado abaixo, é provável que você tenha iniciado o host de dentro da pasta *HttpExample*. Nesse caso, use **Ctrl**+**C** para parar o host, navegue até a pasta *LocalFunctionProj* pai e execute `func start` novamente.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

Copie a URL da função `HttpExample` dessa saída para um navegador e acrescente a cadeia de caracteres de consulta `?name=<your-name>`, fazendo com que a URL completa seja `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve exibir uma mensagem como `Hello Functions`:

![Resultado da execução local da função no navegador](./media/functions-create-first-function-python/function-test-local-browser.png)

O terminal em que você executou `func start` também mostra a saída do log conforme você faz solicitações.

Quando estiver pronto, pressione **CTRL**+**C** para parar o host das funções.

## <a name="create-supporting-azure-resources-for-your-function"></a>Criar recursos de suporte do Azure para a função

Para implantar o código da função no Azure, você precisa criar três recursos:

- Um grupo de recursos, que é um contêiner lógico para recursos relacionados.
- Uma conta de Armazenamento do Azure, que mantém o estado e outras informações sobre seus projetos.
- Um aplicativo de funções do Azure, que fornece o ambiente para execução do código de função. Um aplicativo de funções é mapeado para seu projeto de função local e permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Use comandos da CLI do Azure para criar esses itens. Cada comando fornece uma saída em JSON após a conclusão.

1. Se ainda não tiver feito isso, entre no Azure usando o comando [az login](/cli/azure/reference-index#az-login):

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado `AzureFunctionsQuickstart-rg` na região `westeurope`. (De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível com o comando `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `AzureFunctionsQuickstart-rg` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.
    
1. Crie uma conta de armazenamento para uso geral em seu grupo de recursos e região usando o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). No exemplo a seguir, substitua `<storage_name>` por um nome exclusivo globalmente que for adequado para você. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta para uso geral típica.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    Para este início rápido, a conta de armazenamento incorre em apenas alguns centavos de dólar.
    
1. Crie o aplicativo do Functions usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). No exemplo a seguir, substitua `<storage_name>` pelo nome da conta usada na etapa anterior e substitua `<app_name>` por um nome globalmente exclusivo que seja adequado para você. O `<app_name>` também é o domínio do DNS padrão para o aplicativo de funções.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    Este comando cria um aplicativo de funções que executa o runtime na linguagem especificada no [Plano de consumo do Azure Functions](functions-scale.md#consumption-plan), que é gratuito para o uso que você fará aqui. O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.
    
## <a name="deploy-the-function-project-to-azure"></a>Implantar o projeto de funções no Azure

Com os recursos necessários configurados, você está pronto para implantar seu projeto de funções local no aplicativo de funções no Azure usando o comando [func azure functionapp publish](functions-run-local.md#project-file-deployment). No exemplo a seguir, substitua `<app_name>` pelo nome do aplicativo.

```
func azure functionapp publish <app_name>
```

Se vir o erro "Não é possível encontrar o aplicativo com o nome...", aguarde alguns segundos e tente novamente, pois o Azure pode não ter inicializado totalmente o aplicativo após o comando `az functionapp create` anterior.

O comando de publicação mostra resultados semelhantes à seguinte saída (truncado para fins de simplicidade):

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>Invocar a função no Azure

Como a função usa um gatilho HTTP, você a invoca fazendo uma solicitação HTTP para sua URL no navegador ou usando uma ferramenta como curl. Nos dois casos, o parâmetro da URL `code` é sua chave de função exclusiva que autoriza a invocação com o ponto de extremidade da função.

# <a name="browsertabbrowser"></a>[Navegador](#tab/browser)

Copie a **URL de invocação** completa mostrada na saída do comando de publicação para a barra de endereços de um navegador, acrescentando o parâmetro de consulta `&name=Azure`. O navegador deverá exibir uma saída semelhante à que foi exibida quando você executou a função localmente.

![A saída da função executada no Azure em um navegador](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curltabcurl"></a>[curl](#tab/curl)

Execute [curl](https://curl.haxx.se/) com a **URL de invocação**, acrescentando o parâmetro `&name=Azure`. A saída do comando deverá ser o texto "Olá, Azure".

![A saída da função executada no Azure usando curl](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> Para exibir logs quase em tempo real para um aplicativo do Python publicado, use o [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Limpar os recursos

Se prosseguir para a próxima etapa, [Adicionar uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-python.md), mantenha todos os seus recursos, pois você se baseará no que já fez.

Caso contrário, use o comando a seguir para excluir o grupo de recursos e todos os recursos contidos nele para evitar custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-python.md)
