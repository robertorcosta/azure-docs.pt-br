---
title: (PRETERIDO) Tutorial do Serviço de Contêiner do Azure – implantar cluster
description: Tutorial do Serviço de Contêiner do Azure – implantar cluster
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b8821f3bb3d48786697cbc4137baf530856774fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274001"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(PRETERIDO) Implantar um cluster Kubernetes no Serviço de Contêiner do Azure

> [!TIP]
> Para a versão atualizada deste tutorial que usa o Serviço de Kubernetes do Azure, confira [Tutorial: Implantar um cluster do AKS (Serviço de Kubernetes do Azure)](../../aks/tutorial-kubernetes-deploy-cluster.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes fornece uma plataforma distribuída para aplicativos em contêineres. Com o Serviço de Contêiner do Azure, o provisionamento de um cluster Kubernetes pronto para produção é simples e rápido. Neste tutorial, parte 3 de 7, um cluster Kubernetes do Serviço de Contêiner do Azure é implantado. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantação de um cluster ACS Kubernetes
> * Instalação da CLI Kubernetes (kubectl)
> * Configuração de kubectl

Nos tutoriais subsequentes, o aplicativo Azure Vote é implantado no cluster, dimensionado e atualizado, e o Log Analytics é configurado para monitorar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem de contêiner foi criada e carregada em uma instância do Registro de Contêiner do Azure. Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Criar cluster Kubernetes

Crie um cluster Kubernetes no Serviço de Contêiner do Azure com o comando [az acs create](/cli/azure/acs#az-acs-create). 

O exemplo a seguir cria um cluster denominado `myK8sCluster` em um Grupo de recursos denominado `myResourceGroup`. Este Grupo de recursos foi criado no [tutorial anterior](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

Em alguns casos, como em uma avaliação limitada, uma assinatura do Azure terá acesso limitado aos recursos do Azure. Se a implantação falhar devido à limitação nos núcleos disponíveis, reduza a contagem de agentes padrão, adicionando `--agent-count 1` ao comando [az acs create](/cli/azure/acs#az-acs-create). 

Após alguns minutos, a implantação é concluída e retorna as informações formatadas em JSON sobre a implantação do ACS.

## <a name="install-the-kubectl-cli"></a>Instalar a CLI kubectl

Para se conectar ao cluster Kubernetes do computador cliente, use [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comando do Kubernetes. 

Se você estiver usando o Azure Cloud Shell, o kubectl já estará instalado. Se você deseja instalá-lo localmente, você pode usar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Se estiver executando em Linux ou macOS, você precisará executar com sudo. No Windows, certifique-se de que shell foi executado como administrador.

```azurecli-interactive
az acs kubernetes install-cli 
```

No Windows, a instalação padrão é *c:\program files (x86)\kubectl.exe*. Talvez seja necessário adicionar esse arquivo para o caminho do Windows. 

## <a name="connect-with-kubectl"></a>Conectar-se com kubectl

Para configurar o kubectl e se conectar ao cluster Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes).

```azurecli-interactive
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Para verificar a conexão ao seu cluster, execute o comando [kubectl get nodes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```console
kubectl get nodes
```

Saída:

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Ao concluir o tutorial, você tem um cluster ACS Kubernetes pronto para cargas de trabalho. Em tutoriais subsequentes, um aplicativo de multicontêiner é implantado nesse cluster, escalado horizontalmente, atualizado e monitorado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um cluster Kubernetes do Serviço de Contêiner do Azure foi implantado. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantação de um cluster ACS Kubernetes
> * Instalação da CLI Kubernetes (kubectl)
> * Configuração de kubectl

Avance para o próximo tutorial para saber mais sobre como executar o aplicativo no cluster.

> [!div class="nextstepaction"]
> [Implantar o aplicativo no Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
