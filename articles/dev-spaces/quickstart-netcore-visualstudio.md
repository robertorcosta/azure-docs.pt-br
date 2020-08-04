---
title: 'Depurar e iterar no Kubernetes: Visual Studio e .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Este início rápido mostra como usar o Azure Dev Spaces e o Visual Studio para depurar e iterar rapidamente um aplicativo .NET Core no Serviço de Kubernetes do Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 8279a32ece16209c1dd5bca13d08e22b283677ee
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006996"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Início Rápido: Depurar e iterar no Kubernetes: Visual Studio e .NET Core – Azure Dev Spaces

Neste guia, você aprenderá a:

- Configure o Azure Dev Spaces com um cluster Kubernetes gerenciado no Azure.
- Desenvolva o código em contêineres iterativamente usando o Visual Studio.
- Depure o código em execução no cluster usando o Visual Studio.

O Azure Dev Spaces também permite que você depure e itere usando:
- [Java e Visual Studio Code](quickstart-java.md)
- [Node.js e Visual Studio Code](quickstart-nodejs.md)
- [.NET Core e Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- Visual Studio 2019 no Windows com a carga de trabalho de Desenvolvimento do Azure instalada. Se você não tiver o Visual Studio instalado, baixe-o [aqui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- A [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) instalada.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Serviço de Kubernetes do Azure

Você precisa criar um cluster do AKS em uma [região com suporte][supported-regions]. Os comandos a seguir criam um grupo de recursos chamado *MyResourceGroup* e um cluster do AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar o Azure Dev Spaces no cluster do AKS

Use o comando `use-dev-spaces` para habilitar o Dev Spaces no cluster do AKS e siga os prompts. O comando abaixo habilita o Dev Spaces no cluster *MyAKS*, no grupo *MyResourceGroup* e cria um espaço de desenvolvimento *padrão*.

> [!NOTE]
> O comando `use-dev-spaces` também instalará a CLI do Azure Dev Spaces se ela ainda não estiver instalada. Não é possível instalar a CLI do Azure Dev Spaces no Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="create-a-new-aspnet-web-app"></a>Criar um aplicativo Web do ASP.NET

1. Abra o Visual Studio.
1. Criar um novo projeto.
1. Escolha *Aplicativo Web ASP.NET Core* e clique em *Avançar*.
1. Dê o nome *webfrontend* ao seu projeto e clique em *Criar*.
1. Quando solicitado, escolha *Aplicativo Web (Model-View-Controller)* como o modelo.
1. Selecione *.NET Core* e *ASP.NET Core 2.1* na parte superior.
1. Clique em *Criar*.

## <a name="connect-your-project-to-your-dev-space"></a>Conectar o projeto ao seu espaço de desenvolvimento

No eu projeto, selecione **Azure Dev Spaces** na lista suspensa de configurações de inicialização, conforme mostrado abaixo.

![Captura de tela da interface do usuário do Visual Studio com a opção IIS Express destacada e selecionada e a opção Azure Dev Spaces destacada.](media/get-started-netcore-visualstudio/LaunchSettings.png)

Na caixa de diálogo do Azure Dev Spaces, selecione suas *Assinatura* e *Cluster Kubernetes do Azure*. Deixe *Espaço* definido como *Padrão* e habilite a caixa de seleção *Publicamente Acessível*. Clique em *OK*.

![Captura de tela da caixa de diálogo Azure Dev Spaces.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Esse processo implanta o serviço no espaço de desenvolvimento *padrão* com uma URL publicamente acessível. Se você escolher um cluster que não foi configurado para trabalhar com Azure Dev Spaces, verá uma mensagem perguntando se deseja configurá-lo. Clique em *OK*.

![Captura de tela da caixa de diálogo Adicionar recurso do Azure Spaces.](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

A URL pública do serviço em execução no espaço de desenvolvimento *padrão* é exibida na janela *Saída*:

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

No exemplo acima, a URL pública é `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`. 

Selecione **Depurar** e, em seguida, **Iniciar Depuração**. Depois de alguns segundos, seu serviço será iniciado e o Visual Studio abrirá um navegador com a URL pública do serviço. Se o navegador não abrir automaticamente, navegue até a URL pública de seu serviço em um navegador e interaja com o serviço em execução no espaço de desenvolvimento.

Esse processo pode ter desabilitado o acesso público ao serviço. Para habilitar o acesso público, atualize o [valor de entrada em *values.yaml*][ingress-update].

## <a name="update-code"></a>Atualizar código

Se o Visual Studio ainda estiver conectado ao seu espaço de desenvolvimento, clique no botão parar. Altere a linha 20 em `Controllers/HomeController.cs` para:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Salve as alterações, selecione **Depurar** e, em seguida, **Iniciar Depuração**. Depois de alguns segundos, seu serviço será iniciado e o Visual Studio abrirá um navegador com a URL pública do serviço. Se um navegador não abrir automaticamente, navegue até a URL pública de seu serviço em um navegador e clique em *Sobre*. Observe que a mensagem atualizada é exibida.

Em vez de recompilar e reimplantar uma nova imagem de contêiner sempre que houver edições de código, o Azure Dev Spaces recompilará incrementalmente o código dentro do contêiner existente para fornecer um loop de edição/depuração mais rápido.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definir e usar pontos de interrupção para depuração

Se o Visual Studio ainda estiver conectado ao seu espaço de desenvolvimento, clique no botão parar. Abra `Controllers/HomeController.cs` e clique em algum lugar da linha 20 para colocar o cursor lá. Para definir um ponto de interrupção, pressione *F9* ou clique em *Depurar* e, em seguida, *Ativar/Desativar Pontos de Interrupção*. Para iniciar o serviço no modo de depuração no espaço de desenvolvimento, pressione *F5* ou clique em *Depurar* e, em seguida, *Iniciar Depuração*.

Abra o serviço em um navegador e observe que nenhuma mensagem foi exibida. Retorne ao Visual Studio e observe a linha 20 realçada. O ponto de interrupção definido colocou o serviço em pausa na linha 20. Para retomar o serviço, pressione *F5* ou clique em *Depurar* e, em seguida, *Continuar*. Volte para o navegador e observe que agora a mensagem foi exibida.

Durante a execução de seu serviço no Kubernetes com o depurador anexado, você tem acesso completo às informações de depuração como a pilha de chamadas, as variáveis locais e as informações de exceção.

Remova o ponto de interrupção, colocando o cursor na linha 20 em `Controllers/HomeController.cs` e pressionando *F9*.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

Navegue até o grupo de recursos no portal do Azure e clique em *Excluir grupo de recursos*. Como alternativa, você pode usar o comando [az aks delete](/cli/azure/aks#az-aks-delete):

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Trabalhando com vários contêineres e desenvolvimento em equipe](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
