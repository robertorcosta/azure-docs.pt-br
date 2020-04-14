---
title: Use pools de nó do sistema no Azure Kubernetes Service (AKS)
description: Saiba como criar e gerenciar pools de nó do sistema no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: b567d9e618877463e1e659f368d35fbb787a4ef2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259061"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gerenciar pools de nó do sistema no Azure Kubernetes Service (AKS)

No Azure Kubernetes Service (AKS), nós da mesma configuração são agrupados em *piscinas de nós*. Os pools de nó contêm as VMs subjacentes que executam seus aplicativos. Pools de nó do sistema e pools de nó do usuário são dois modos de pool de nó diferentes para seus clusters AKS. Os pools de nó do sistema servem ao objetivo principal de hospedar pods críticos do sistema, como CoreDNS e tunnelfront. Os pools de nó do usuário servem ao objetivo principal de hospedar seus pods de aplicativos. No entanto, os pods de aplicativos podem ser agendados em pools de nó do sistema se você desejar ter apenas um pool em seu cluster AKS. Cada cluster AKS deve conter pelo menos um pool de nó do sistema com pelo menos um nó. 

> [!Important]
> Se você executar um único pool de nós de sistema para o seu cluster AKS em um ambiente de produção, recomendamos que você use pelo menos três nós para a piscina de nós.

## <a name="before-you-begin"></a>Antes de começar

* Você precisa da versão 2.3.1 ou posterior do Azure CLI instalada e configurada posteriormente. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que suportam pools de nó do sistema.

* Consulte [Cotas, restrições ao tamanho da máquina virtual e disponibilidade de região no Azure Kubernetes Service (AKS)][quotas-skus-regions].
* O cluster AKS deve ser construído com conjuntos de escala de máquinas virtuais como o tipo VM.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricas minúsculos e deve começar com uma letra minúscula. Para pools de nó Linux, o comprimento deve ser entre 1 e 12 caracteres. Para piscinas de nó do Windows, o comprimento deve ser entre 1 e 6 caracteres.

## <a name="system-and-user-node-pools"></a>Pools de nó de sistema e usuário

Os nós do nó do sistema têm cada um o rótulo **kubernetes.azure.com/mode: sistema**. Cada cluster AKS contém pelo menos um pool de nó do sistema. Os pools de nó do sistema têm as seguintes restrições:

* Os pools de sistemas osType devem ser Linux.
* Os pools de nó do usuário osType podem ser Linux ou Windows.
* Os pools do sistema devem conter pelo menos um nó, e os pools de nós do usuário podem conter zero ou mais nós.
* Os pools de nó do sistema requerem um VM SKU de pelo menos 2 vCPUs e 4GB de memória.
* Os pools de nó do sistema devem suportar pelo menos 30 pods, conforme descrito pela [fórmula de valor mínimo e máximo para pods][maximum-pods].
* As piscinas de nó spot requerem pools de nó do usuário.

Você pode fazer as seguintes operações com pools de nó:

* Altere um pool de nó do sistema para ser um pool de nó do usuário, desde que você tenha outro pool de nó do sistema para tomar seu lugar no cluster AKS.
* Altere um pool de nó de usuário para ser um pool de nó do sistema.
* Exclua pools de nó de usuário.
* Você pode excluir pools de nó do sistema, desde que você tenha outro pool de nó do sistema para tomar seu lugar no cluster AKS.
* Um cluster AKS pode ter vários pools de nó do sistema e requer pelo menos um pool de nó do sistema.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Crie um novo cluster AKS com um pool de nó do sistema

Quando você cria um novo cluster AKS, você cria automaticamente um pool de nó do sistema com um único nó. O pool de nó inicial é padrão para um modo de sistema de tipo. Quando você cria novas piscinas de nós com az aks nodepool add, esses pools de nós são pools de nó de usuário, a menos que você especifique explicitamente o parâmetro de modo.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *de Eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [az aks create][az-aks-create] para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um pool de sistemas contendo um nó. Para suas cargas de trabalho de produção, certifique-se de que você está usando pools de nó do sistema com pelo menos três nós. Essa operação pode levar vários minutos para ser concluída.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Adicione um pool de nó de sistema a um cluster AKS existente

Você pode adicionar um ou mais pools de nó de sistema aos clusters AKS existentes. O comando a seguir adiciona um pool de nó do sistema de tipo de modo com uma contagem padrão de três nados.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Mostrar detalhes para sua piscina de nó

Você pode verificar os detalhes do seu pool de nó com o seguinte comando.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Um modo de tipo **O sistema** é definido para pools de nó do sistema, e um modo de tipo **Usuário** é definido para pools de nó do usuário.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Atualizar pools de sistema e nó do usuário

Você pode alterar os modos para os pools de nó do sistema e do usuário. Você pode alterar um pool de nó do sistema para um pool de usuários somente se outro pool de nó do sistema já existir no cluster AKS.

Este comando altera um pool de nó do sistema para um pool de nó do usuário.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Este comando altera um pool de nó de usuário para um pool de nó do sistema.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Exclua um pool de nó do sistema

> [!Note]
> Para usar os pools de nó do sistema em clusters AKS antes da versão da API 2020-03-02, adicione um novo pool de nós do sistema e exclua o pool de nós padrão original.

Anteriormente, você não podia excluir o pool de nó do sistema, que era o pool de nó padrão inicial em um cluster AKS. Agora você tem a flexibilidade de excluir qualquer pool de nó de seus clusters. Uma vez que os clusters AKS requerem pelo menos um pool de nó do sistema, você deve ter pelo menos dois pools de nó do sistema no cluster AKS antes de poder excluir um deles.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar e gerenciar pools de nó do sistema em um cluster AKS. Para obter mais informações sobre como usar vários pools de nós, consulte [usar vários pools de nós][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30