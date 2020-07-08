---
title: Usar o Local Process with Kubernetes com o Visual Studio Code (versão prévia)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Saiba como usar o processo local com o kubernetes para conectar seu computador de desenvolvimento a um cluster kubernetes com Azure Dev Spaces
keywords: Processo local com kubernetes, Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316457"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Usar o Local Process with Kubernetes com o Visual Studio Code (versão prévia)

O processo local com o kubernetes permite que você execute e depure o código em seu computador de desenvolvimento, enquanto ainda está conectado ao cluster kubernetes com o restante do seu aplicativo ou serviços. Por exemplo, se você tiver uma grande arquitetura de microserviços com muitos bancos de dados e serviços interdependentes, é difícil replicar essas dependências no seu computador de desenvolvimento. Além disso, a criação e a implantação de código em seu cluster kubernetes para cada alteração de código durante o desenvolvimento de loop interno podem ser lentas, demoradas e difíceis de usar com um depurador.

O processo local com kubernetes evita que você precise compilar e implantar seu código no cluster, em vez de criar uma conexão diretamente entre o computador de desenvolvimento e o cluster. Conectar seu computador de desenvolvimento ao cluster durante a depuração permite que você teste e desenvolva rapidamente seu serviço no contexto do aplicativo completo sem criar nenhuma configuração de Docker ou kubernetes.

O processo local com o kubernetes redireciona o tráfego entre o cluster kubernetes conectado e o computador de desenvolvimento. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução no cluster kubernetes se comuniquem como se estivessem no mesmo cluster kubernetes. O processo local com o kubernetes também fornece uma maneira de replicar as variáveis de ambiente e os volumes montados disponíveis para pods no seu cluster kubernetes em seu computador de desenvolvimento. Fornecer acesso a variáveis de ambiente e volumes montados em seu computador de desenvolvimento permite que você trabalhe rapidamente em seu código sem ter que replicar essas dependências manualmente.

Neste guia, você aprenderá a usar o processo local com kubernetes para redirecionar o tráfego entre o cluster do kubernetes e o código em execução no seu computador de desenvolvimento. Este guia também fornece um script para implantar um aplicativo de exemplo grande com vários microserviços em um cluster kubernetes.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][preview-terms]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="before-you-begin"></a>Antes de começar

Este guia usa o [aplicativo de exemplo de compartilhamento de bicicletas Azure dev Spaces][bike-sharing-github] para demonstrar a conexão do seu computador de desenvolvimento a um cluster kubernetes. Se você já tiver seu próprio aplicativo em execução em um cluster kubernetes, ainda poderá seguir as etapas abaixo e usar os nomes dos seus próprios serviços.

### <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure][azure-cli] instalada.
* [Visual Studio Code][vs-code] em execução no MacOS ou no Windows 10.
* A versão de extensão [Azure dev Spaces][azds-vs-code] 2.0.220200601 ou superior instalada em Visual Studio Code.
* [Azure dev Spaces CLI instalada][azds-cli].

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

Crie um cluster AKS em uma [região com suporte][supported-regions]. Os comandos a seguir criam um grupo de recursos chamado *MyResourceGroup* e um cluster do AKS chamado *MyAKS*.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>Instalar o aplicativo de exemplo

Instale o aplicativo de exemplo no cluster usando o script fornecido. Você pode executar esse script em seu computador de desenvolvimento ou usando o [Azure cloud Shell][azure-cloud-shell].

> [!IMPORTANT]
> Você deve ter acesso de *proprietário* ou *colaborador* ao cluster para executar o script.

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Navegue até o aplicativo de exemplo que executa o cluster abrindo sua URL pública, que é exibida na saída do script de instalação.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

No exemplo acima, a URL pública é `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>Conectar-se ao cluster e depurar um serviço

No computador de desenvolvimento, baixe e configure a CLI do kubernetes para se conectar ao cluster do kubernetes usando [AZ AKs Get-Credentials][az-aks-get-credentials].

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Abra *dev-Spaces/Samples/BikeSharingApp/bicicletas* do [aplicativo de exemplo de compartilhamento de bicicletas Azure dev Spaces][bike-sharing-github] no Visual Studio Code. Abra a extensão do serviço kubernetes do Azure e selecione o namespace *dev* no cluster *MyAKS* .

![Selecionar namespace](../media/local-process-kubernetes-vs-code/select-namespace.png)

Use o `npm install` comando para instalar as dependências do aplicativo.

```console
npm install
```

Selecione o ícone de *depuração* à esquerda e selecione *processo local com kubernetes (visualização)* na parte superior.

![Escolha o processo local com kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Clique no botão iniciar ao lado de *processo local com kubernetes (versão prévia)*. Na primeira vez que você executar essa configuração de inicialização, será solicitado que você configure o serviço que deseja substituir, a porta a ser encaminhada do seu computador de desenvolvimento e a tarefa de inicialização a ser usada.

Escolha o serviço *bicicletas* .

![Escolher serviço](../media/local-process-kubernetes-vs-code/choose-service.png)

Todo o tráfego no cluster kubernetes é redirecionado para o serviço de *bicicletas* para a versão do seu aplicativo em execução no seu computador de desenvolvimento. O processo local com kubernetes também roteia todo o tráfego de saída do aplicativo de volta para o cluster kubernetes.

> [!IMPORTANT]
> Você só pode redirecionar os serviços que têm um único Pod.

Depois de selecionar o serviço, você será solicitado a inserir a porta TCP para seu aplicativo local. Para este exemplo, digite *3000*.

![Conectar escolher porta](../media/local-process-kubernetes-vs-code/choose-port.png)

Escolha *iniciar via NPM* como a tarefa de inicialização.

![Conectar escolher tarefa de inicialização](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> Você será solicitado a permitir que o *KubernetesDNSManager* execute com privilégios elevados e modifique o arquivo de hosts.

Seu computador de desenvolvimento está conectado quando a barra de status fica laranja e a extensão espaços de desenvolvimento mostra que você está conectado.

![Computador de desenvolvimento conectado](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> No subesquent é iniciado, você não será solicitado a fornecer o nome do serviço, a porta ou a tarefa de inicialização. Esses valores são salvos em *. vscode/tasks.jsem*.

Quando o computador de desenvolvimento estiver conectado, o tráfego começará a redirecionar para o seu computador de desenvolvimento para o serviço que você está substituindo.

## <a name="set-a-break-point"></a>Definir um ponto de interrupção

Abra [server.js][server-js-breakpoint] e clique em algum lugar na linha 233 para colocar o cursor lá. Defina um ponto de interrupção pressionando *F9* ou clicando em *executar* e *alternar ponto de interrupção*.

Navegue até o aplicativo de exemplo abrindo a URL pública. Selecione *Aurelia Briggs (cliente)* como o usuário e, em seguida, selecione uma bicicleta para alugar. Observe que a imagem da bicicleta não é carregada. Retorne para Visual Studio Code e observe que a linha 233 está realçada. O ponto de interrupção que você definiu pausou o serviço na linha 233. Para retomar o serviço, pressione *F5* ou clique em *executar* e *continuar*. Retorne ao seu navegador e verifique se você vê uma imagem de espaço reservado para a bicicleta.

Remova o ponto de interrupção colocando o cursor na linha 233 `server.js` e pressionando *F9*.

### <a name="update-your-application"></a>Atualizar o aplicativo

Edite `server.js` para remover as linhas 234 e 235:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A seção agora deve ser semelhante a:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salve as alterações e clique em *executar* e *reinicie a depuração*. Depois de reconectar-se, atualize o navegador e verifique se você não vê mais uma imagem de espaço reservado para a bicicleta.

Clique em *executar* e em *parar depuração* para interromper o depurador.

> [!NOTE]
> Por padrão, a interrupção da tarefa de depuração também desconecta o computador de desenvolvimento do cluster kubernetes. Você pode alterar esse comportamento pesquisando o *processo local com kubernetes: desconectar após a depuração* nas configurações de Visual Studio Code e removendo a marca de seleção ao lado de *desconectar automaticamente quando a depuração terminar.* Depois de atualizar essa configuração, o computador de desenvolvimento permanecerá conectado quando você parar e iniciar a depuração. Para desconectar o computador de desenvolvimento do seu cluster, clique na extensão Azure Dev Spaces na barra de status e escolha *Desconectar sessão atual*.
>
> Se Visual Studio Code finalizar abruptamente a conexão com o cluster ou terminar, o serviço que você está redirecionando poderá não ser restaurado para seu estado original antes de você se conectar com o processo local com kubernetes. Para corrigir esse problema, consulte o [Guia de solução de problemas][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>Usando log e diagnóstico

A saída de log é gravada na janela *espaços de desenvolvimento* depois que o computador de desenvolvimento está conectado ao cluster kubernetes.

![Saída](../media/local-process-kubernetes-vs-code/output.png)

Clique na barra de status Azure Dev Spaces e escolha *Mostrar informações de diagnóstico de conexão*. Esse comando imprime as variáveis de ambiente atuais e o DNS em toda a saída de log.

![Saída com diagnóstico](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Além disso, você pode encontrar os logs de diagnóstico no `Azure Dev Spaces` diretório no [diretório *temporário* do seu computador de desenvolvimento][azds-tmp-dir].

## <a name="remove-the-sample-application-from-your-cluster"></a>Remover o aplicativo de exemplo do cluster

Use o script fornecido para remover o aplicativo de exemplo do cluster.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>Próximas etapas

Saiba como usar as ações Azure Dev Spaces e GitHub para testar alterações de uma solicitação pull diretamente no AKS antes que a solicitação pull seja mesclada no Branch principal do repositório.

> [!div class="nextstepaction"]
> [Ações do GitHub & serviço kubernetes do Azure][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download