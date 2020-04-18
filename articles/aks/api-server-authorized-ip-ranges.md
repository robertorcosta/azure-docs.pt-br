---
title: API servidor autorizou faixas de IP no Azure Kubernetes Service (AKS)
description: Saiba como proteger seu cluster usando um intervalo de endereços IP para acesso ao servidor de API no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 570d842409fc019d24446e091f83402f4c288d7c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640048"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Acesso seguro ao servidor API usando faixas de endereçoIP autorizadas no Azure Kubernetes Service (AKS)

No Kubernetes, o servidor de API recebe solicitações para executar ações no cluster, como criar recursos ou dimensionar o número de nós. O servidor de API é a maneira central de interagir e gerenciar um cluster. Para melhorar a segurança do cluster e minimizar os ataques, o servidor de API só deve ser acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra como usar faixas de endereçoIP autorizadas para o servidor DePI para limitar quais endereços IP e CIDRs podem acessar o plano de controle.

> [!IMPORTANT]
> Em novos clusters, os intervalos de endereços IP autorizados pelo servidor API são suportados apenas no balanceador de carga *SKU* padrão. Os clusters existentes com o *balanceador de* carga Basic SKU e os intervalos de endereçoip autorizados do servidor API configurados continuarão funcionando como está, mas não poderão ser migrados para um balanceador de carga *SKU padrão.* Esses clusters existentes também continuarão a funcionar se a versão do Kubernetes ou o plano de controle foratualizado.

## <a name="before-you-begin"></a>Antes de começar

Os intervalos IP autorizados do servidor API só funcionam para novos clusters AKS que você cria. Este artigo mostra como criar um cluster AKS usando o Azure CLI.

Você precisa da versão 2.0.76 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Visão geral das faixas ip autorizadas pelo servidor API

O servidor API do Kubernetes é como as APIs Kubernetes subjacentes são expostas. Esse componente fornece a interação para ferramentas de gerenciamento, tais como `kubectl` ou o painel do Kubernetes. O AKS fornece um mestre de cluster de um único inquilino, com um servidor de API dedicado. Por padrão, o servidor de API é atribuído a um endereço IP público e você deve controlar o acesso usando controles de acesso baseados em função (RBAC).

Para garantir o acesso ao plano de controle AKS / servidor aPI de outra forma acessível ao público, você pode habilitar e usar faixas IP autorizadas. Essas faixas de IP autorizadas só permitem que os intervalos de endereços IP definidos se comuniquem com o servidor de API. Uma solicitação feita ao servidor de API a partir de um endereço IP que não faz parte dessas faixas de IP autorizadas é bloqueada. Continue a usar o RBAC para autorizar os usuários e as ações que eles solicitam.

Para obter mais informações sobre o servidor API e outros componentes de cluster, consulte [os conceitos centrais do Kubernetes para AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Crie um cluster AKS com intervalos IP autorizados para servidor de API habilitados

Os intervalos IP autorizados do servidor API só funcionam para novos clusters AKS. Crie um cluster usando o [az aks criar][az-aks-create] e especificar o parâmetro *--api-server-authorized-ip-ranges* para fornecer uma lista de intervalos de endereços IP autorizados. Esses intervalos de endereços IP geralmente são faixas de endereços usadas por suas redes locais ou IPs públicos. Quando você especificar um intervalo CIDR, comece com o primeiro endereço IP no intervalo. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP no intervalo, como *137.117.106.88/29*.

> [!IMPORTANT]
> Por padrão, seu cluster usa o [balanceador de carga Padrão SKU][standard-sku-lb] que você pode usar para configurar o gateway de saída. Quando você habilita intervalos IP autorizados para servidor de API durante a criação de clusters, o IP público para o cluster também é permitido por padrão, além das faixas especificadas. Se você especificar *""* ou nenhum valor para *--api-server-authorized-ip-ranges,* os intervalos ip autorizados do servidor API serão desativados. Observe que, se você estiver usando o PowerShell, use *--api-server-autorizado-ip-ranges=""* (com sinal igual) para evitar quaisquer problemas de análise.

O exemplo a seguir cria um cluster de nó único chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup* com intervalos de IP autorizados pelo servidor API ativados. Os intervalos de endereços IP permitidos são *73.140.245.0/24*:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Você deve adicionar esses intervalos a uma lista de permitir:
> - O endereço IP público do firewall
> - Qualquer intervalo que represente redes que você administrará o cluster a partir de
> - Se você estiver usando o Azure Dev Spaces em seu cluster AKS, você terá que permitir [faixas adicionais com base na sua região][dev-spaces-ranges].

> O limite superior para o número de faixas de IP que você pode especificar é 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Especifique os IPs de saída para o balanceador de carga SKU padrão

Ao criar um cluster AKS, se você especificar os endereços IP ou prefixos de saída para o cluster, esses endereços ou prefixos também serão permitidos. Por exemplo:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

No exemplo acima, todos os IPs fornecidos no parâmetro *--load-balancer-out-ip-prefixos* são permitidos juntamente com os IPs no parâmetro *--api-server-autorizado-ip-ranges.*

Alternativamente, você pode especificar o parâmetro *--load-balancer-outbound-ip-prefixes* para permitir prefixos IP do balanceador de carga de saída.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Permitir apenas o IP público de saída do balanceador de carga SKU padrão

Quando você habilita intervalos IP autorizados para servidor de API durante a criação de clusters, o IP público de saída para o balanceador de carga SKU padrão para o cluster também é permitido por padrão, além das faixas especificadas. Para permitir apenas o IP público de saída do balanceador de carga SKU padrão, use *0.0.0.0/32* ao especificar o parâmetro *--api-server-autorizado-ip-ranges.*

No exemplo a seguir, apenas o IP público de saída do balanceador de carga SKU padrão é permitido, e você só pode acessar o servidor de API a partir dos nós dentro do cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Atualize as faixas de IP autorizadas por um servidor de API de um cluster

Para atualizar os intervalos ip autorizados do servidor API em um cluster existente, use o comando [de atualização az aks][az-aks-update] e use os *parâmetros --api-server-authorized-ip-ip-ranges*, *--load-balancer-out-ip-prefixes*, *--load-balancer-outbound-ips*ou *--load-balancer-outbound-ip-prefixes.*

O exemplo a seguir atualiza os intervalos de IP autorizados pelo servidor API no cluster chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. O intervalo de endereços IP para autorizar é *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Você também pode usar *0.0.0.0/32* ao especificar o parâmetro *--api-server-authorized-ip-ranges* para permitir apenas o IP público do balanceador de carga SKU padrão.

## <a name="disable-authorized-ip-ranges"></a>Desativar faixas de IP autorizadas

Para desativar os intervalos de IP autorizados, use [a atualização az aks][az-aks-update] e especifique um intervalo vazio para desativar os intervalos IP autorizados do servidor API. Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você habilitou faixas de IP autorizadas pelo servidor API. Esta abordagem é uma parte de como você pode executar um cluster AKS seguro.

Para obter mais informações, consulte [conceitos de segurança para aplicativos e clusters em AKS][concepts-security] e práticas [recomendadas para segurança de clusters e upgrades em AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
