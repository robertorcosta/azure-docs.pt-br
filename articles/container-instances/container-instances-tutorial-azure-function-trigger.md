---
title: Tutorial – Disparar grupo de contêineres por função do Azure
description: Criar uma função do PowerShell sem servidor disparada por HTTP para automatizar a criação de Instâncias de Contêiner do Azure
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 49eb0721972a92f33bda2532367bc78280b6e655
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533368"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Tutorial: Usar uma função do Azure disparada por HTTP para criar um grupo de contêineres

O [Azure Functions](../azure-functions/functions-overview.md) é um serviço de computação sem servidor que pode executar scripts ou código em resposta a uma variedade de eventos, como uma solicitação HTTP, um temporizador ou uma mensagem em uma fila do Armazenamento do Azure.

Neste tutorial, você criará uma função do Azure que usa uma solicitação HTTP e dispara a implantação de um [grupo de contêineres](container-instances-container-groups.md). Este exemplo mostra os conceitos básicos do uso do Azure Functions para a criação automática de recursos nas Instâncias de Contêiner do Azure. Modifique ou estenda o exemplo para cenários mais complexos ou outros gatilhos de evento. 

Você aprenderá como:

> [!div class="checklist"]
> * Usar o Visual Studio Code com a [extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar uma função básica do PowerShell disparada por HTTP.
> * Habilitar uma identidade no aplicativo de funções e conceder a ela permissões para criar recursos do Azure.
> * Modificar e publicar novamente a função do PowerShell para automatizar a implantação de um grupo de contêineres de contêiner único.
> * Verificar a implantação disparada por HTTP do contêiner.

> [!IMPORTANT]
> O PowerShell para o Azure Functions está atualmente em versão prévia. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="prerequisites"></a>Prerequisites

Confira [Criar sua primeira função do PowerShell no Azure](../azure-functions/functions-create-first-function-powershell.md#prerequisites) para obter os pré-requisitos para instalar e usar o Visual Studio Code com o Azure Functions no sistema operacional.

Algumas etapas deste artigo usam a CLI do Azure. Use o Azure Cloud Shell ou uma instalação localização da CLI do Azure para concluir essas etapas. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Criar uma função básica do PowerShell

Siga as etapas em [Criar sua primeira função do PowerShell no Azure](../azure-functions/functions-create-first-function-powershell.md) para criar uma função do PowerShell usando o modelo de gatilho HTTP. Use o nome da função padrão do Azure **HttpTrigger**. Conforme mostrado no início rápido, teste a função localmente e publique o projeto em um aplicativo de funções no Azure. Este exemplo é uma função básica disparada por HTTP que retorna uma cadeia de texto. Nas etapas posteriores deste artigo, você modificará a função para criar um grupo de contêineres.

Este artigo pressupõe que você publique o projeto usando o nome *myfunctionapp*, em um grupo de recursos do Azure automaticamente nomeado de acordo com o nome do aplicativo de funções (também *myfunctionapp*). Substitua os nomes exclusivos do aplicativo de funções e do grupo de recursos nas etapas posteriores.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Habilitar uma identidade gerenciada do Azure no aplicativo de funções

Agora habilite uma [identidade gerenciada](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity) atribuída ao sistema no aplicativo de funções. O host do PowerShell que executa o aplicativo pode se autenticar automaticamente usando essa identidade, permitindo que as funções executem ações nos serviços do Azure aos quais a identidade recebeu acesso. Neste tutorial, você concederá as permissões de identidade gerenciada para criar recursos no grupo de recursos do aplicativo de funções. 

Primeiro use o comando [az group show][az-group-show] para obter a ID do grupo de recursos do aplicativo de funções e armazená-la em uma variável de ambiente. Este exemplo pressupõe que você execute o comando em um shell do Bash.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Execute [az functionapp identity app assign][az-functionapp-identity-app-assign] para atribuir uma identidade local ao aplicativo de funções e atribuir uma função de colaborador ao grupo de recursos. Essa função permite que a identidade crie recursos adicionais, como grupos de contêineres no grupo de recursos.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Modificar a função HttpTrigger

Modifique o código do PowerShell na função **HttpTrigger** para criar um grupo de contêineres. No arquivo `run.ps1` da função, localize o bloco de código a seguir. Esse código exibirá um valor de nome, caso um seja passado como uma cadeia de consulta na URL da função:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Substitua esse código pelo bloco de exemplo a seguir. Aqui, se um valor de nome for passado na cadeia de consulta, ele será usado para nomear e criar um grupo de contêineres usando o cmdlet [New-AzContainerGroup][new-azcontainergroup]. Lembre-se de substituir o nome do grupo de recursos *myfunctionapp* pelo nome do grupo de recursos do aplicativo de funções:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Este exemplo cria um grupo de contêineres que consiste em uma única instância de contêiner que executa a imagem `alpine`. O contêiner executa um único comando `echo` e, em seguida, é encerrado. Em um exemplo do mundo real, você pode disparar a criação de um ou mais grupos de contêineres para executar um trabalho em lotes.
 
## <a name="test-function-app-locally"></a>Testar o aplicativo de funções localmente

Verifique se a função é executada de maneira correta localmente antes de publicar novamente o projeto de aplicativo de funções no Azure. Conforme mostrado no [início rápido do PowerShell](../azure-functions/functions-create-first-function-powershell.md), insira um ponto de interrupção local no script do PowerShell e uma chamada `Wait-Debugger` acima dele. Para obter diretrizes de depuração, confira [Depurar o PowerShell Azure Functions localmente](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Publicar novamente o aplicativo de funções do Azure

Depois de verificar se a função é executada corretamente no computador local, é hora de publicar o projeto novamente no aplicativo de funções existente no Azure.

> [!NOTE]
> Lembre-se de remover todas as chamadas a `Wait-Debugger` antes de publicar suas funções no Azure.

1. No Visual Studio Code, abra a paleta de comandos. Pesquise e selecione `Azure Functions: Deploy to function app...`.
1. Selecione a pasta de trabalho atual como zip e realize a implantação.
1. Selecione a assinatura e, em seguida, o nome do aplicativo de funções existente (*myfunctionapp*). Confirme que você deseja substituir a implantação anterior.

Uma notificação é exibida depois que seu aplicativo de funções é criado e o pacote de implantação é aplicado. Selecione **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure atualizados.

## <a name="run-the-function-in-azure"></a>Executar a função no Azure

Depois que a implantação for concluída com êxito, obtenha a URL da função. Por exemplo, use a área **Azure: Functions** no Visual Studio Code para copiar a URL da função **HttpTrigger** ou obtenha a URL da função no [portal do Azure](../azure-functions/functions-create-first-azure-function.md#test-the-function).

A URL da função inclui um código exclusivo e está no formato:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Executar a função sem passar um nome

Como um primeiro teste, execute o comando `curl` e passe a URL da função sem acrescentar uma cadeia de consulta `name`. Lembre-se de incluir o código exclusivo da função.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

A função retorna o código de status 400 e o texto `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Executar a função e passar o nome de um grupo de contêineres

Agora execute o comando `curl` acrescentando o nome de um grupo de contêineres (*mycontainergroup*) como uma cadeia de consulta `&name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

A função retorna o código de status 200 e dispara a criação do grupo de contêineres:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Verifique se o contêiner foi executado com o comando [az container logs][az-container-logs]:

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Exemplo de saída:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Limpar recursos

Caso não precise mais de nenhum dos recursos criados neste tutorial, execute o comando [az group delete][az-group-delete] para remover o grupo de recursos e todos os recursos que ele contém. Esse comando exclui o Registro de contêiner que você criou, bem como o contêiner em execução e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma função do Azure que usa uma solicitação HTTP e dispara a implantação de um grupo de contêineres. Você aprendeu como:

> [!div class="checklist"]
> * Usar o Visual Studio Code com a extensão Azure Functions para criar uma função básica do PowerShell disparada por HTTP.
> * Habilitar uma identidade no aplicativo de funções e conceder a ela permissões para criar recursos do Azure.
> * Modificar o código da função do PowerShell para automatizar a implantação de um grupo de contêineres de contêiner único.
> * Verificar a implantação disparada por HTTP do contêiner.

Para obter um exemplo detalhado para iniciar e monitorar um trabalho em contêiner, confira a postagem no blog [Contêineres sem servidor controlados por evento com o PowerShell Azure Functions e as Instâncias de Contêiner do Azure](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) e o [exemplo de código](https://github.com/anthonychu/functions-powershell-run-aci) complementar.

Confira a [documentação do Azure Functions](/azure/azure-functions/) para obter diretrizes detalhadas sobre como criar funções do Azure e publicar um projeto de funções. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
