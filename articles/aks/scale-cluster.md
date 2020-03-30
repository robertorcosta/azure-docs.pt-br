---
title: Dimensionar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como dimensionar o número de nós em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368410"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Dimensionar a contagem de nós em um cluster do AKS (Serviço de Kubernetes do Azure)

Se as necessidades de recurso para seus aplicativos mudarem, você poderá dimensionar manualmente um cluster do AKS para executar um número diferente de nós. Ao reduzir, os nós são cuidadosamente [isolados e drenados][kubernetes-drain] para minimizar a interrupção aos aplicativos em execução. Quando você escala, o AKS espera até `Ready` que os nós sejam marcados pelo cluster Kubernetes antes que as cápsulas sejam programadas neles.

## <a name="scale-the-cluster-nodes"></a>Escalar os nós de cluster

Primeiro, obtenha o *nome* do seu pool de nó usando o comando [az aks show.][az-aks-show] O exemplo a seguir obtém o nome do pool de nó para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A saída de exemplo a seguir mostra que o *nome* é *nodepool1*:

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Use o comando [az aks scale][az-aks-scale] para dimensionar os nódulos de cluster. O exemplo a seguir escala um cluster chamado *myAKSCluster* para um único nó. Forneça seu próprio *--nodepool-name* do comando anterior, como *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A saída de exemplo a seguir mostra que o cluster foi dimensionado com êxito para um nó, conforme mostrado na seção *agentPoolProfiles*:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você escalou manualmente um cluster AKS para aumentar ou diminuir o número de nódulos. Você também pode usar o [cluster autoscaler][cluster-autoscaler] para dimensionar automaticamente seu cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
