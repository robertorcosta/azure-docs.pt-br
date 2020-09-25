---
title: Intervalos de IP autorizados do servidor de API no serviço kubernetes do Azure (AKS)
description: Saiba como proteger seu cluster usando um intervalo de endereços IP para acessar o servidor de API no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 5dbe5061253fb18222a476a88a1ec94a5ce4b0fa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299656"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteger o acesso ao servidor de API usando intervalos de endereços IP autorizados no serviço de kubernetes do Azure (AKS)

No kubernetes, o servidor de API recebe solicitações para executar ações no cluster, como para criar recursos ou dimensionar o número de nós. O servidor de API é a maneira central de interagir com e gerenciar um cluster. Para melhorar a segurança do cluster e minimizar os ataques, o servidor de API só deve ser acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra como usar intervalos de endereços IP autorizados do servidor de API para limitar quais endereços IP e CIDR podem acessar o plano de controle.

## <a name="before-you-begin"></a>Antes de começar

Este artigo mostra como criar um cluster AKS usando o CLI do Azure.

Você precisará da CLI do Azure versão 2.0.76 ou posterior instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

### <a name="limitations"></a>Limitações

O recurso de intervalos de IP autorizados do servidor de API tem as seguintes limitações:
- Em clusters criados após os intervalos de endereços IP autorizados do servidor de API movidos para fora da visualização em outubro de 2019, os intervalos de endereços IP autorizados do servidor de API só têm suporte no balanceador de carga SKU *padrão* . Os clusters existentes com o balanceador de carga de SKU *básico* e os intervalos de endereços IP autorizados do servidor de API configurados continuarão funcionando como estão, mas não poderão ser migrados para um balanceador de carga de SKU *padrão* . Esses clusters existentes também continuarão a funcionar se sua versão ou plano de controle do kubernetes forem atualizados. Os intervalos de endereços IP autorizados do servidor de API não têm suporte para clusters privados.
- Este recurso não é compatível com clusters que usam o [recurso IP público por pools de nós de nó versão prévia](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview).

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Visão geral dos intervalos de IP autorizados do servidor de API

O servidor de API do kubernetes é como as APIs subjacentes do kubernetes são expostas. Esse componente fornece a interação para ferramentas de gerenciamento, tais como `kubectl` ou o painel do Kubernetes. O AKS fornece um plano de controle de cluster de locatário único, com um servidor de API dedicado. Por padrão, o servidor de API recebe um endereço IP público e você deve controlar o acesso usando o RBAC (controle de acesso baseado em função).

Para proteger o acesso ao servidor do plano de controle AKS/API publicamente acessível, você pode habilitar e usar intervalos de IP autorizados. Esses intervalos de IP autorizados permitem que os intervalos de endereços IP definidos se comuniquem com o servidor de API. Uma solicitação feita ao servidor de API de um endereço IP que não faz parte desses intervalos de IP autorizado é bloqueada. Continue a usar o RBAC para autorizar os usuários e as ações que eles solicitam.

Para obter mais informações sobre o servidor de API e outros componentes de cluster, consulte [kubernetes Core Concepts for AKs][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Criar um cluster AKS com intervalos de IP autorizados do servidor de API habilitado

Crie um cluster usando [AZ AKs Create][az-aks-create] e especifique o *`--api-server-authorized-ip-ranges`* parâmetro para fornecer uma lista de intervalos de endereços IP autorizados. Esses intervalos de endereços IP geralmente são intervalos de endereços usados por suas redes locais ou IPs públicos. Quando você especificar um intervalo CIDR, comece com o primeiro endereço IP no intervalo. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP no intervalo, como *137.117.106.88/29*.

> [!IMPORTANT]
> Por padrão, o cluster usa o [balanceador de carga SKU padrão][standard-sku-lb] que você pode usar para configurar o gateway de saída. Quando você habilita intervalos de IP autorizados do servidor de API durante a criação do cluster, o IP público do cluster também é permitido, por padrão, além dos intervalos que você especificar. Se você especificar *""* ou nenhum valor para *`--api-server-authorized-ip-ranges`* , os intervalos de IP autorizados do servidor de API serão desabilitados. Observe que, se você estiver usando o PowerShell, use *`--api-server-authorized-ip-ranges=""`* (com sinal de igual) para evitar quaisquer problemas de análise.

O exemplo a seguir cria um cluster de nó único chamado *myAKSCluster* no grupo de recursos chamado *MyResource* Group com intervalos de IP autorizados do servidor de API habilitados. Os intervalos de endereços IP permitidos são *73.140.245.0/24*:

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
> Você deve adicionar esses intervalos a uma lista de permissões:
> - O endereço IP público do firewall
> - Qualquer intervalo que represente as redes das quais você administrará o cluster
> - Se estiver usando Azure Dev Spaces em seu cluster AKS, você precisará permitir [intervalos adicionais com base em sua região][dev-spaces-ranges].
>
> O limite superior para o número de intervalos de IP que você pode especificar é 200.
>
> As regras podem levar até 2min para serem propagadas. Aguarde até esse momento ao testar a conexão.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Especificar os IPs de saída para o balanceador de carga SKU padrão

Ao criar um cluster AKS, se você especificar os endereços IP de saída ou prefixos para o cluster, esses endereços ou prefixos também serão permitidos. Por exemplo:

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

No exemplo acima, todos os IPs fornecidos no parâmetro *`--load-balancer-outbound-ip-prefixes`* são permitidos junto com os IPs no *`--api-server-authorized-ip-ranges`* parâmetro.

Em vez disso, você pode especificar o *`--load-balancer-outbound-ip-prefixes`* parâmetro para permitir prefixos de IP de balanceador de carga de saída.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Permitir somente o IP público de saída do balanceador de carga SKU padrão

Quando você habilita intervalos de IP autorizados do servidor de API durante a criação do cluster, o IP público de saída para o balanceador de carga SKU padrão para o cluster também é permitido por padrão, além dos intervalos que você especificar. Para permitir apenas o IP público de saída do balanceador de carga SKU padrão, use *0.0.0.0/32* ao especificar o *`--api-server-authorized-ip-ranges`* parâmetro.

No exemplo a seguir, somente o IP público de saída do balanceador de carga SKU padrão é permitido e você só pode acessar o servidor de API a partir dos nós no cluster.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Atualizar intervalos de IP autorizados do servidor de API de um cluster

Para atualizar os intervalos de IP autorizados do servidor de API em um cluster existente, use o comando [AZ AKs Update][az-aks-update] e use os *`--api-server-authorized-ip-ranges`* parâmetros,--Load-Balancer-Outbound-IP-Prefixes *, *`--load-balancer-outbound-ips`* ou--Load-Balancer-IP-Prefixes* .

O exemplo a seguir atualiza os intervalos de IP autorizados do servidor de API no cluster chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group. O intervalo de endereços IP a ser autorizado é *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Você também pode usar *0.0.0.0/32* ao especificar o *`--api-server-authorized-ip-ranges`* parâmetro para permitir apenas o IP público do balanceador de carga SKU padrão.

## <a name="disable-authorized-ip-ranges"></a>Desabilitar intervalos de IP autorizados

Para desabilitar intervalos IP autorizados, use [AZ AKs Update][az-aks-update] e especifique um intervalo vazio para desabilitar intervalos de IP autorizados do servidor de API. Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você habilitou intervalos de IP autorizados do servidor de API. Essa abordagem é uma parte de como você pode executar um cluster AKS seguro.

Para obter mais informações, consulte [conceitos de segurança para aplicativos e clusters no AKs][concepts-security] e [as práticas recomendadas para segurança de cluster e atualizações no AKs][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
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
