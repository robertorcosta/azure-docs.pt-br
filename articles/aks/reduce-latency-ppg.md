---
title: Usar grupos de posicionamento de proximidade para reduzir a latência de clusters do AKS (serviço kubernetes do Azure)
description: Saiba como usar grupos de posicionamento de proximidade para reduzir a latência para suas cargas de trabalho de cluster AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: 095746b9cf3cada9cebf7d169078eff9eb64a52d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444260"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>Reduzir a latência com grupos de posicionamento de proximidade (visualização)

> [!Note]
> Quando você usa grupos de posicionamento de proximidade com AKS, a colocação se aplica somente aos nós de agente. O nó para o nó e o Pod hospedado correspondente à latência de Pod foram aprimorados. A colocalização não afeta o posicionamento do plano de controle de um cluster.

Ao implantar seu aplicativo no Azure, a difusão de instâncias de máquina virtual (VM) em regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Alguns aplicativos como jogos, simulações de engenharia e HFT (transações de alta frequência) exigem baixa latência e tarefas que são concluídas rapidamente. Para cenários de HPC (computação de alto desempenho) como esses, considere o uso de [grupos de posicionamento de proximidade](../virtual-machines/linux/co-location.md#proximity-placement-groups) para os pools de nós do cluster.

## <a name="limitations"></a>Limitações

* O grupo de posicionamento de proximidade abrange uma única zona de disponibilidade.
* Não há suporte atual para clusters AKS que usam conjuntos de disponibilidade de máquina virtual.
* Não é possível modificar os pools de nós existentes para usar um grupo de posicionamento de proximidade.

> [!IMPORTANT]
> As versões prévias do recurso AKS estão disponíveis em uma base de autoatendimento e aceitação. As versões prévias são fornecidas "no estado em que se encontram" e "conforme disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As versões prévias do AKS são parcialmente cobertas pelo suporte ao cliente em uma base de melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter mais informações, consulte os seguintes artigos:
>
> - [Políticas de suporte do AKS](support-policies.md)
> - [Perguntas frequentes sobre o suporte do Azure.](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Você deve ter os seguintes recursos instalados:

- A extensão 0.4.53 AKs-Preview

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>Configurar o recurso de visualização para grupos de posicionamento de proximidade

> [!IMPORTANT]
> Quando você usa grupos de posicionamento de proximidade com AKS, a colocação se aplica somente aos nós de agente. O nó para o nó e o Pod hospedado correspondente à latência de Pod foram aprimorados. A colocalização não afeta o posicionamento do plano de controle de um cluster.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

Pode levar vários minutos para o registro. Use o comando abaixo para verificar se o recurso está registrado:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

Durante a visualização, você precisa da extensão da CLI de *AKs* para usar grupos de posicionamento de proximidade. Use o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Pools de nós e grupos de posicionamento de proximidade

O primeiro recurso que você implanta com um grupo de posicionamento de proximidade é anexado a um data center específico. Recursos adicionais implantados com o mesmo grupo de posicionamento de proximidade são colocados na mesma data center. Depois que todos os recursos que usam o grupo de posicionamento de proximidade tiverem sido interrompidos (desalocados) ou excluídos, eles não serão mais anexados.

* Muitos pools de nós podem ser associados a um único grupo de posicionamento de proximidade.
* Um pool de nós só pode ser associado a um único grupo de posicionamento de proximidade.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Criar um novo cluster AKS com um grupo de posicionamento de proximidade

O exemplo a seguir usa o comando [AZ Group Create][az-group-create] para criar um grupo de recursos chamado *MyResource* Group na região *centralus* . Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [AZ AKs Create][az-aks-create] . 

A rede acelerada melhora muito o desempenho de rede das máquinas virtuais. Idealmente, use grupos de posicionamento de proximidade em conjunto com a rede acelerada. Por padrão, o AKS usa rede acelerada em [instâncias de máquinas virtuais com suporte](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), que incluem a maioria das máquinas virtuais do Azure com dois ou mais vCPUs.

Crie um novo cluster AKS com um grupo de posicionamento de proximidade:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Execute o comando a seguir e armazene a ID que é retornada:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

O comando produz a saída, que inclui o valor de *ID* que você precisa para os próximos comandos da CLI:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Use a ID de recurso do grupo de posicionamento de proximidade para o valor *myPPGResourceID* no comando abaixo:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial node pool
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Adicionar um grupo de posicionamento de proximidade a um cluster existente

Você pode adicionar um grupo de posicionamento de proximidade a um cluster existente criando um novo pool de nós. Opcionalmente, você pode migrar as cargas de trabalho existentes para o novo pool de nós e, em seguida, excluir o pool de nós original.

Use o mesmo grupo de posicionamento de proximidade que você criou anteriormente, e isso garantirá que os nós de agente em ambos os pools de nós no cluster AKS estejam localizados fisicamente na mesma data center.

Use a ID do recurso do grupo de posicionamento de proximidade que você criou anteriormente e adicione um novo pool de nós com o [`az aks nodepool add`][az-aks-nodepool-add] comando:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Limpar

Para excluir o cluster, use o [`az group delete`][az-group-delete] comando para excluir o grupo de recursos AKs:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [grupos de posicionamento de proximidade][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete

