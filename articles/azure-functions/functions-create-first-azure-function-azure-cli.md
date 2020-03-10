---
title: Criar uma função no Azure que responda a solicitações HTTP
description: Saiba como criar uma função na linha de comando e, em seguida, publicar o projeto local na hospedagem sem servidor no Azure Functions.
ms.date: 01/28/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 2a02e1481d975f877508bde02948bc65561b9f13
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272755"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Início Rápido: Criar uma função no Azure que responda a solicitações HTTP

Neste artigo, você usará ferramentas de linha de comando para criar uma função que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions. A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

Há também uma [versão baseada no Visual Studio Code](functions-create-first-function-vs-code.md) deste artigo.

## <a name="configure-your-local-environment"></a>Configurar o ambiente local

Antes de começar, você deverá ter o seguinte:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ O [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.7.1846 ou uma versão 2.x posterior.

+ A [CLI do Azure](/cli/azure/install-azure-cli) versão 2.0.76 ou posterior. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) ou [Python 3.6](https://www.python.org/downloads/release/python-368/), que são compatíveis com o Azure Functions. Ainda não há suporte para o Python 3.8 e versões posteriores. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ O [SDK do .NET Core 2.2+](https://www.microsoft.com/net/download)
::: zone-end

### <a name="check-your-environment"></a>Verificar o ambiente

+ Em um terminal ou uma janela Comando, execute `func --version` para verificar se a versão do Azure Functions Core Tools é 2.7.1846 ou uma versão 2.x posterior.

+ Execute `az --version` para verificar se a versão da CLI do Azure é a 2.0.76 ou posterior.

+ Execute `az login` para entrar no Azure e verifique se a assinatura está ativa.

::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Execute `node --version` para verificar se a versão do Node.js é 8.x ou 10.x.
::: zone-end
::: zone pivot="programming-language-python"
+ Execute `python --version` (Linux/macOS) ou `py --version` (Windows) para verificar se a versão do Python é 3.7.x ou 3.6.x.

## <a name="create-venv"></a>Criar e ativar um ambiente virtual

Em uma pasta adequada, execute os comandos a seguir para criar e ativar um ambiente virtual chamado `.venv`. Lembre-se de usar o Python 3.7 ou 3.6, que é compatível com o Azure Functions.


# <a name="bash"></a>[Bash](#tab/bash)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Você executará todos os comandos posteriores neste ambiente virtual ativado. (Para sair do ambiente virtual, execute `deactivate`.)

::: zone-end

## <a name="create-a-local-function-project"></a>Criar um projeto de função local

No Azure Functions, um projeto de função é um contêiner para uma ou mais funções individuais que respondem, cada uma, a um gatilho específico. Todas as funções em um projeto compartilham as configurações locais e de hospedagem. Nesta seção, você cria um projeto de função que contém apenas uma função.

1. No ambiente virtual, execute o comando `func init` para criar um projeto de função em uma pasta chamada *LocalFunctionProj* com o runtime especificado:

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionProj --python
    ```
    ::: zone-end
    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionProj --dotnet
    ```
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionProj --javascript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionProj --typescript
    ```
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionProj --powershell
    ```
    ::: zone-end


    Essa pasta contém vários arquivos do projeto, incluindo arquivos de configuração chamados [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Como *local.settings.json* pode conter segredos baixados do Azure, o arquivo é excluído do controle do código-fonte por padrão no arquivo *.gitignore*.

1. Navegue até a pasta do projeto:

    ```
    cd LocalFunctionProj
    ```
    
1. Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função e o argumento `--template` especifica o gatilho da função. 

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

    ::: zone pivot="programming-language-csharp"
    `func new` cria um arquivo de código HttpExample.cs.
    ::: zone-end
    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
    `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de código apropriado para a linguagem escolhida do projeto, bem como um arquivo de configuração chamado *function.json*.
    ::: zone-end

### <a name="optional-examine-the-file-contents"></a>(Opcional) Examinar o conteúdo do arquivo

Se preferir, você poderá ir diretamente para [Executar a função localmente](#run-the-function-locally) e examinar o conteúdo do arquivo mais tarde.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* contém um método `Run` que recebe dados de solicitação na variável `req` é uma [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) decorada com o **HttpTriggerAttribute**, que define o comportamento do gatilho. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

O objeto de retorno é um [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) que retorna uma mensagem de resposta como um [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) ou um [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* contém uma função de Python `main()` que é disparada de acordo com a configuração em *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Para um gatilho HTTP, a função recebe dados de solicitação na variável `req`, conforme definido em *function.json*. `req` é uma instância da [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). O objeto de retorno, definido como `$return` em *function.json*, é uma instância da [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporta uma função que é disparada de acordo com a configuração em *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

Para um gatilho HTTP, a função recebe dados de solicitação na variável `req`, conforme definido em *function.json*. O objeto de retorno, definido como `$return` em *function.json*, é a resposta. Para saber mais, confira [Gatilhos e associações HTTP do Azure Functions](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts* exporta uma função que é disparada de acordo com a configuração em *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Para um gatilho HTTP, a função recebe dados de solicitação na variável `req` do tipo **HttpRequest**, conforme definido em *function.json*. O objeto de retorno, definido como `$return` em *function.json*, é a resposta. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* contém um script de função que é disparado de acordo com a configuração em *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Para um gatilho HTTP, a função recebe dados de solicitação transmitidos para o parâmetro `$Request` definido em *function.json*. O objeto de retorno, definido como `Response` em *function.json*, é transmitido para o cmdlet `Push-OutputBinding` como a resposta. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* é um arquivo de configuração que define as `bindings` de entrada e de saída da função, incluindo o tipo de gatilho. 
::: zone-end

::: zone pivot="programming-language-python"
Se preferir, você poderá alterar `scriptFile` para invocar um arquivo Python diferente.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Cada associação exige uma direção, um tipo e um nome exclusivo. O gatilho HTTP tem uma associação de entrada do tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e uma associação de saída do tipo [`http`](functions-bindings-http-webhook-output.md).
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Criar recursos de suporte do Azure para a função

Antes de poder implantar o código da função no Azure, você precisa criar três recursos:

- Um grupo de recursos, que é um contêiner lógico para recursos relacionados.
- Uma conta de armazenamento, que mantém o estado e outras informações sobre seus projetos.
- Um aplicativo de funções, que fornece o ambiente para a execução do código de função. Um aplicativo de funções é mapeado para seu projeto de função local e permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Use os comandos da CLI do Azure a seguir para criar esses itens. Cada comando fornece uma saída em JSON após a conclusão.

1. Se ainda não tiver feito isso, entre no Azure usando o comando [az login](/cli/azure/reference-index#az-login):

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado `AzureFunctionsQuickstart-rg` na região `westeurope`. (De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível com o comando `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    ::: zone pivot="programming-language-python"  
    > [!NOTE]
    > Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `AzureFunctionsQuickstart-rg` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.
    ::: zone-end  
    
1. Crie uma conta de armazenamento para uso geral em seu grupo de recursos e região usando o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). No exemplo a seguir, substitua `<STORAGE_NAME>` por um nome exclusivo globalmente que for adequado para você. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta de uso geral, que é [compatível com o Functions](storage-considerations.md#storage-account-requirements).

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    A conta de armazenamento gera apenas alguns centavos de dólar para este início rápido.
    
1. Crie o aplicativo do Functions usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). No exemplo a seguir, substitua `<STORAGE_NAME>` pelo nome da conta usada na etapa anterior e substitua `<APP_NAME>` por um nome globalmente exclusivo que seja adequado para você. O `<APP_NAME>` também é o domínio do DNS padrão para o aplicativo de funções. 

    ::: zone pivot="programming-language-python"  
    Se estiver usando o Python 3.6, altere também a `--runtime-version` para `3.6`.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-javascript,programming-language-typescript"  
    Se estiver usando o Node.js 8, altere também a `--runtime-version` para `8`.

    
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    ::: zone pivot="programming-language-csharp"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  
    
    ::: zone pivot="programming-language-powershell"  
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions_version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    ::: zone-end  

    Este comando cria um aplicativo de funções que executa o runtime da linguagem especificada no [Plano de Consumo do Azure Functions](functions-scale.md#consumption-plan), que é gratuito para o uso que você fará aqui. O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.
    
## <a name="deploy-the-function-project-to-azure"></a>Implantar o projeto de funções no Azure

::: zone pivot="programming-language-typescript"  
Antes de usar o Core Tools para implantar seu projeto no Azure, você criará um build pronto para produção de arquivos JavaScript com base nos arquivos de origem TypeScript.

O seguinte comando prepara seu projeto TypeScript para implantação:

```
npm run build:production 
```
::: zone-end  

Com os recursos necessários configurados, você está pronto para implantar seu projeto de funções local no aplicativo de funções no Azure usando o comando [func azure functionapp publish](functions-run-local.md#project-file-deployment). No exemplo a seguir, substitua `<APP_NAME>` pelo nome do aplicativo.

```
func azure functionapp publish <APP_NAME>
```

Se vir o erro "Não é possível encontrar o aplicativo com o nome...", aguarde alguns segundos e tente novamente, pois o Azure pode não ter inicializado totalmente o aplicativo após o comando `az functionapp create` anterior.

O comando de publicação mostra resultados semelhantes à seguinte saída (truncado para fins de simplicidade):

<pre>
...

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
</pre>

## <a name="invoke-the-function-on-azure"></a>Invocar a função no Azure

Como a função usa um gatilho HTTP, você a invoca fazendo uma solicitação HTTP para sua URL no navegador ou usando uma ferramenta como curl. Nos dois casos, o parâmetro da URL `code` é a [chave de função](functions-bindings-http-webhook-trigger.md#authorization-keys) exclusiva que autoriza a invocação do ponto de extremidade da função.

# <a name="browser"></a>[Navegador](#tab/browser)

Copie a **URL de Invocação** completa mostrada na saída do comando de publicação na barra de endereços de um navegador, acrescentando o parâmetro de consulta `&name=Functions`. O navegador deverá exibir uma saída semelhante à que foi exibida quando você executou a função localmente.

![A saída da função executada no Azure em um navegador](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

Execute [`curl`](https://curl.haxx.se/) com a **URL de Invocação**, acrescentando o parâmetro `&name=Functions`. A saída do comando deverá ser o texto, "Olá, Functions".

![A saída da função executada no Azure usando curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Para ver os logs quase em tempo real de um aplicativo de funções publicado, use o [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Limpar os recursos

Se prosseguir para a próxima etapa, [Adicionar uma associação de saída de fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-cli.md), mantenha todos os seus recursos, pois você se baseará no que já fez.

Caso contrário, use o comando a seguir para excluir o grupo de recursos e todos os recursos contidos nele para evitar custos adicionais.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conectar-se a uma fila do Armazenamento do Azure](functions-add-output-binding-storage-queue-cli.md)
