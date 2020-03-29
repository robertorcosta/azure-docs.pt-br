---
title: Habilite os espaços de desenvolvimento do Azure no AKS & instalar as ferramentas do lado do cliente
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Saiba como ativar o Azure Dev Spaces em um cluster AKS e instalar as ferramentas do lado do cliente.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898946"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Habilite os espaços de desenvolvimento do Azure em um cluster AKS e instale as ferramentas do lado do cliente

Este artigo mostra várias maneiras de habilitar espaços de desenvolvimento do Azure em um cluster AKS, bem como instalar as ferramentas do lado do cliente.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Habilite ou remova os espaços de dev do Azure usando o CLI

Antes de habilitar espaços de dev usando o CLI, você precisa:
* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita][az-portal-create-account].
* [O Azure CLI instalado][install-cli].
* [Um cluster AKS][create-aks-cli] em uma [região suportada][supported-regions].

Use o comando `use-dev-spaces` para habilitar o Dev Spaces no cluster do AKS e siga os prompts.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

O comando acima habilita o Dev Spaces no cluster *myAKSCluster* no grupo *myResourceGroup* e cria um espaço de desenvolvimento *padrão.*

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

O `use-dev-spaces` comando também instala o Azure Dev Spaces CLI.

Para remover o Azure Dev Spaces do `azds remove` seu cluster AKS, use o comando. Por exemplo: 

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

O comando acima remove os espaços de desenvolvimento do Azure do cluster *MyAKS* no *MyResourceGroup*. Quaisquer namespaces criados com o Azure Dev Spaces permanecerão junto com suas cargas de trabalho, mas novas cargas de trabalho nesses namespaces não serão instrumentadas com o Azure Dev Spaces. Além disso, se você reiniciar quaisquer pods existentes instrumentados com o Azure Dev Spaces, você poderá ver erros. Esses pods devem ser reimplantados sem a ferramenta Azure Dev Spaces. Para remover totalmente o Azure Dev Spaces do seu cluster, exclua todos os pods em todos os namespaces onde o Azure Dev Spaces foi ativado.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Habilite ou remova os Espaços de Dev do Azure usando o portal Azure

Antes de habilitar o Dev Spaces usando o portal Azure, você precisa:
* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita][az-portal-create-account].
* [Um cluster AKS][create-aks-portal] em uma [região suportada][supported-regions].

Para habilitar o Azure Dev Spaces usando o portal Azure:
1. Faça login no [portal Azure][az-portal].
1. Navegue até o seu cluster AKS.
1. Selecione o item do menu *Dev Spaces.*
1. Altere *Habilitar Dev Spaces* para *Sim* e clique em *Salvar*.

![Habilitar o Dev Spaces no portal do Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Ativar o Azure Dev Spaces usando o portal Azure **não** instala nenhuma ferramenta do lado do cliente para espaços de desenvolvimento do Azure.

Para remover os espaços do Azure Dev do cluster AKS, *altere os espaços de dev para* *Não* e clique *em Salvar*. Quaisquer namespaces criados com o Azure Dev Spaces permanecerão junto com suas cargas de trabalho, mas novas cargas de trabalho nesses namespaces não serão instrumentadas com o Azure Dev Spaces. Além disso, se você reiniciar quaisquer pods existentes instrumentados com o Azure Dev Spaces, você poderá ver erros. Esses pods devem ser reimplantados sem a ferramenta Azure Dev Spaces. Para remover totalmente o Azure Dev Spaces do seu cluster, exclua todos os pods em todos os namespaces onde o Azure Dev Spaces foi ativado.

## <a name="install-the-client-side-tools"></a>Instale as ferramentas do lado do cliente

Você pode usar as ferramentas do lado do cliente do Azure Dev Spaces para interagir com espaços de desenvolvimento em um cluster AKS da sua máquina local. Existem várias maneiras de instalar as ferramentas do lado do cliente:

* Em [Visual Studio Code,][vscode]instale a [extensão Azure Dev Spaces][vscode-extension].
* No [Visual Studio 2019,][visual-studio]instale a carga de trabalho do Azure Development.
* No Visual Studio 2017, instale a carga de trabalho de Desenvolvimento Web e [ferramentas do Visual Studio para Kubernetes][visual-studio-k8s-tools].
* Baixe e instale o [Windows,][cli-win] [Mac][cli-mac]ou [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
