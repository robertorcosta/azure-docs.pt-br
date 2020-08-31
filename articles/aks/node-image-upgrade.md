---
title: Atualizar imagens de nó do AKS (serviço kubernetes do Azure)
description: Saiba como atualizar as imagens em nós de cluster AKS e pools de nós.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 744e62f8a2207cff400a96069fc6ea82866f6e2d
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055678"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Atualização de imagem de nó do AKS (serviço kubernetes do Azure)

O AKS dá suporte à atualização das imagens em um nó para que você esteja atualizado com as atualizações mais recentes do sistema operacional e do tempo de execução. O AKS fornece uma nova imagem por semana com as atualizações mais recentes, portanto, é benéfico atualizar as imagens do nó regularmente para os recursos mais recentes, incluindo patches do Linux ou do Windows. Este artigo mostra como atualizar imagens de nó de cluster AKS, bem como atualizar imagens de pool de nós sem Atualizar a versão do kubernetes.

Se você estiver interessado em aprender sobre as imagens mais recentes fornecidas pelo AKS, consulte as [notas de versão do AKS](https://github.com/Azure/AKS/releases) para obter mais detalhes.

Para obter informações sobre como atualizar a versão do kubernetes para seu cluster, consulte [atualizar um cluster AKs][upgrade-cluster].

## <a name="limitations"></a>Limitações

* O cluster AKS deve usar conjuntos de dimensionamento de máquinas virtuais para os nós.

## <a name="install-the-aks-cli-extension"></a>Instalar a extensão da CLI do AKS

Antes da versão do próximo núcleo da CLI ser lançada, você precisa da extensão da CLI *AKs-Preview* para usar a atualização de imagem de nó. Use o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Atualizar todos os nós em todos os pools de nós

A atualização da imagem do nó é feita com `az aks upgrade` . Para atualizar a imagem do nó, use o seguinte comando:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Durante a atualização, verifique o status das imagens de nó com o seguinte `kubectl` comando para obter os rótulos e filtrar as informações da imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Quando a atualização for concluída, use `az aks show` para obter os detalhes do pool de nós atualizados. A imagem do nó atual é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Atualizar um pool de nós específico

A atualização da imagem em um pool de nós é semelhante à atualização da imagem em um cluster.

Para atualizar a imagem do sistema operacional do pool de nós sem executar uma atualização do cluster kubernetes, use a `--node-image-only` opção no exemplo a seguir:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Durante a atualização, verifique o status das imagens de nó com o seguinte `kubectl` comando para obter os rótulos e filtrar as informações da imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Quando a atualização for concluída, use `az aks nodepool show` para obter os detalhes do pool de nós atualizados. A imagem do nó atual é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Atualizar imagens de nó com surtos de nó

Para acelerar o processo de atualização de imagem de nó, você pode atualizar suas imagens de nó usando um valor de surto de nó personalizável. Por padrão, o AKS usa um nó adicional para configurar atualizações.

Se você quiser aumentar a velocidade das atualizações, use o `--max-surge` valor para configurar o número de nós a serem usados para atualizações para que eles sejam concluídos mais rapidamente. Para saber mais sobre as compensações de várias `--max-surge` configurações, confira [Personalizar atualização de surtos de nó][max-surge].

O comando a seguir define o valor máximo de surto para executar uma atualização de imagem de nó:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Durante a atualização, verifique o status das imagens de nó com o seguinte `kubectl` comando para obter os rótulos e filtrar as informações da imagem do nó atual:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Use `az aks nodepool show` para obter os detalhes do pool de nós atualizado. A imagem do nó atual é mostrada na `nodeImageVersion` propriedade.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Próximas etapas

- Consulte as [notas de versão do AKS](https://github.com/Azure/AKS/releases) para obter informações sobre as imagens de nó mais recentes.
- Saiba como atualizar a versão do kubernetes com a [atualização de um cluster do AKS][upgrade-cluster].
- [Aplicar atualizações de segurança e kernel a nós do Linux no serviço kubernetes do Azure (AKS)][security-update]
- Saiba mais sobre vários pools de nós e como atualizar pools de nós com [criar e gerenciar vários pools de nós][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
