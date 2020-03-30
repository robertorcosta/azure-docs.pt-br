---
title: Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como atualizar um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 4520297e83f96f95b10ecafd5af52a913dc5f450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621969"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)

Como parte do ciclo de vida de um cluster do AKS, muitas vezes você precisará atualizar para a versão mais recente do Kubernetes. É importante aplicar as versões mais recentes de segurança do Kubernetes ou atualizar para obter os recursos mais recentes. Este artigo mostra como atualizar os componentes mestres ou um único pool de nó padrão em um cluster AKS.

Para clusters AKS que usam vários grupos de nó ou nós do Windows Server (atualmente em visualização no AKS), consulte Atualizar um pool de nó [sinuoso][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo exige que você esteja executando a versão 2.0.65 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

> [!WARNING]
> Uma atualização de cluster AKS aciona um cordão e dreno de seus nódulos. Se você tiver uma cota de cálculo baixa disponível, a atualização pode falhar. Consulte [aumentar as cotas](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para obter mais informações.
> Se você estiver executando sua própria implantação de cluster autoscaler, desative-o (você pode dimensioná-lo para zero réplicas) durante a atualização, pois há uma chance de interferir no processo de atualização. O autoscaler gerenciado lida com isso automaticamente. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificação de atualizações disponíveis do cluster do AKS

Para verificar quais versões do Kubernetes estão disponíveis para seu cluster, use o comando [az aks get-upgrades][az-aks-get-upgrades]. O exemplo abaixo verifica atualizações disponíveis para o cluster nomeado *myAKSCluster* em um grupo de recursos nomeado *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Ao atualizar um cluster do AKS, as versões secundárias do Kubernetes não poderão ser ignoradas. Por exemplo, atualizações entre *1.12.x* -> *1.13.x* ou *1.13.x* -> *1.14.x* são permitidas, porém *1.12.x* -> *1.14.x* não é.
>
> Para atualizar, de *1.12.x* -> *1.14.x*, primeiro upgrade de *1.12.x* -> *1.13.x*, em seguida, atualize de *1.13.x* -> *1.14.x*.

O exemplo a seguir mostra que o cluster pode ser atualizado para as versões *1.13.9* e *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se não houver nenhum upgrade disponível, você receberá:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster AKS

Com uma lista de versões disponíveis para o cluster do AKS, use o comando [az aks upgrade][az-aks-upgrade] para atualizar. Durante o processo de atualização, o AKS adiciona um novo nó ao cluster que executa a versão kubernetes especificada, em seguida, [cuidadosamente, coriça e drena][kubernetes-drain] um dos álos antigos para minimizar a interrupção aos aplicativos em execução. Quando o novo nó é confirmado como pods de aplicativos em execução, o nó antigo é excluído. Este processo se repete até que todos os nós do cluster tenham sido atualizados.

O exemplo a seguir atualiza um cluster para a versão *1.13.10*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

O cluster demora alguns minutos para ser atualizado, dependendo de quantos nós que você tem. 

> [!NOTE]
> Há um tempo total permitido para uma atualização de cluster ser concluída. Este tempo é calculado tomando o produto de `10 minutes * total number of nodes in the cluster`. Por exemplo, em um cluster de 20 nós, as operações de upgrade devem ter sucesso em 200 minutos ou a AKS falhará na operação para evitar um estado de cluster irrecuperável. Para recuperar a falha de atualização, tente novamente a operação de atualização após o tempo de tempo ser atingido.

Para confirmar se a atualização teve êxito, use o comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora executa *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como atualizar um cluster do AKS existente. Para saber mais sobre como implantar e gerenciar clusters do AKS, confira os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
