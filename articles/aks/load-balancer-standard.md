---
title: Usar um balanceador de carga de SKU Standard
titleSuffix: Azure Kubernetes Service
description: Saiba como usar um balanceador de carga com um SKU Standard para expor seus serviços com o AKS (Serviço de Kubernetes do Azure).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 14e80f6348772af77c5a53b1d5e9111c4ae8ba9b
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402062"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Use um balanceador de carga de SKU Standard no AKS (Serviço de Kubernetes do Azure)

Para fornecer acesso a aplicativos por meio dos serviços de Kubernetes do tipo `LoadBalancer` no AKS (Serviço de Kubernetes do Azure), você pode usar um Azure Load Balancer. Um balanceador de carga em execução no AKS pode ser usado como um balanceador de carga interno ou externo. Um balanceador de carga interno torna um serviço do Kubernetes acessível somente a aplicativos em execução na mesma rede virtual que o cluster do AKS. Um balanceador de carga externo recebe um ou mais IPs públicos para entrada e torna um serviço de Kubernetes acessível externamente usando os IPs públicos.

O Balanceador de carga do Azure está disponível em dois SKUs - *Básico* e *Padrão*. Por padrão, o SKU *Standard* é usado quando você cria um cluster do AKS. O uso de um balanceador de carga de SKU *Standard* oferece recursos e funcionalidade adicionais, como um maior tamanho de pool de back-end e Zonas de Disponibilidade. É importante entender as diferenças entre balanceadores de carga *Standard* e *Básico* antes de escolher qual usar. Depois de criar um cluster do AKS, você não poderá alterar o SKU do balanceador de carga para esse cluster. Para obter mais informações sobre os SKUs *Básico* e *Standard*, confira [Comparação de SKU do Azure Load Balancer][azure-lb-comparison].

Este artigo pressupõe uma compreensão básica dos conceitos do Kubernetes e do Azure Load Balancer. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts] e [O que é Azure Load Balancer?][azure-lb].

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que esteja em execução a CLI do Azure versão 2.0.81 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster do AKS com o Azure Load Balancer de SKU *Standard*. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A entidade de serviço de cluster do AKS também precisará de permissão para gerenciar recursos de rede se você usar uma sub-rede ou um grupo de recursos existente. Em geral, atribua a função *Colaborador de rede* à sua entidade de serviço nos recursos delegados. Em vez de uma entidade de serviço, você também pode usar a identidade gerenciada atribuída ao sistema para permissões. Para obter mais informações, confira [Usar identidades gerenciadas](use-managed-identity.md). Para obter mais informações sobre permissões, confira [Delegar acesso do AKS a outros recursos do Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Migrar de um Load Balancer de SKU Básico para SKU Standard

Se você tiver um cluster existente com o Load Balancer de SKU Básico, haverá diferenças comportamentais importantes a serem observadas ao migrar para usar um cluster com o Load Balancer de SKU Standard.

Por exemplo, fazer implantações azuis/verdes para migrar clusters é uma prática comum considerando que o tipo `load-balancer-sku` de um cluster só pode ser definido no momento da criação do cluster. No entanto, Load Balancers de *SKU Básico* usam os Endereços IP do *SKU Básico* que não são compatíveis com os Load Balancers de *SKU Standard* porque exigem Endereços IP de *SKU Standard*. Ao migrar clusters para atualizar SKUs do Load Balancer, será necessário um novo endereço IP com um SKU de Endereço IP compatível.

Para ver mais considerações sobre como migrar clusters, acesse [nossa documentação sobre considerações sobre migração](aks-migration.md) para ver uma lista de tópicos importantes a serem considerados durante a migração. As limitações abaixo também são importantes diferenças comportamentais a serem observadas ao usar Load Balancers de SKU Standard no AKS.

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters do AKS que dão suporte a um balanceador de carga com o SKU *Standard*:

* Pelo menos um IP público ou prefixo de IP é necessário para permitir o tráfego de saída do cluster do AKS. O IP público ou o prefixo de IP também é necessário para manter a conectividade entre o painel de controle e os nós do agente, bem como para manter a compatibilidade com as versões anteriores do AKS. Você tem as seguintes opções para especificar IPs públicos ou prefixos de IP com um balanceador de carga de SKU *Standard*:
    * Fornecer seus IPs públicos.
    * Forneça seus prefixos de IP público.
    * Especifique um número até 100 para permitir que o cluster do AKS crie esse tanto de IPs públicos de SKU *Standard* no mesmo grupo de recursos criado que o cluster do AKS, que geralmente é chamado de *MC_* no início. O AKS atribui o IP público ao balanceador de carga de SKU *Standard*. Por padrão, um IP público será criado automaticamente no mesmo grupo de recursos que o cluster AKS se nenhum IP público, prefixo de IP público ou número de IPs for especificado. Você também deve permitir endereços públicos e evitar a criação de qualquer Azure Policy que cause banimento à criação do IP.
* Ao usar o SKU *Standard* para um balanceador de carga, você deve usar a versão do Kubernetes *1.13 ou mais recente*.
* Definir o SKU do balanceador de carga só pode ser feito ao criar um cluster do AKS. Você não poderá alterar o SKU do balanceador de carga depois que um cluster do AKS for criado.
* Você só pode usar um tipo de SKU do balanceador de carga (Básico ou Standard) em um cluster.
* Os Balanceadores de Carga de SKU *Standard* dão suporte apenas a Endereços IP de SKU *Standard*.

## <a name="use-the-standard-sku-load-balancer"></a>Usar o balanceador de carga de SKU *Standard*

Quando você cria um cluster do AKS, por padrão, o balanceador de carga de SKU *Standard* é usado quando você executa serviços nesse cluster. Por exemplo, [o guia de início rápido usando a CLI do Azure][aks-quickstart-cli] implanta um aplicativo de exemplo que usa o balanceador de carga de SKU *Standard*.

> [!IMPORTANT]
> Os endereços IP públicos podem ser evitados com a personalização de uma UDR (rota definida pelo usuário). A especificação de um tipo de saída do cluster do AKS como UDR pode ignorar o provisionamento de IP e a configuração do pool de back-end para o Azure Load Balancer criado pelo AKS. Confira [definir o `outboundType` de um cluster como 'userDefinedRouting'](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Configurar o balanceador de carga para ser interno

Você também pode configurar o balanceador de carga para ser interno e não expor um IP público. Para configurar o balanceador de carga como interno, adicione `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` como uma anotação ao serviço *LoadBalancer*. Você pode ver um exemplo de manifesto yaml, bem como mais detalhes sobre um balanceador de carga interno [aqui][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Dimensionar o número de IPs públicos gerenciados

Ao usar um balanceador de carga de SKU *Standard* com IPs públicos de saída gerenciados, criados por padrão, você pode dimensionar o número de IPs públicos de saída gerenciados usando o parâmetro *load-balancer-managed-ip-count*.

Para atualizar um cluster existente, execute o comando a seguir. Esse parâmetro também pode ser definido no momento de criação do cluster para ter vários IPs públicos de saída gerenciados.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos de saída gerenciados como *2* para o cluster *myAKSCluster* no *myResourceGroup*. 

Você também pode usar o parâmetro *load-balancer-managed-ip-count* para definir o número inicial de IPs públicos de saída gerenciados ao criar o cluster acrescentando o parâmetro `--load-balancer-managed-outbound-ip-count` e configurando-o como o valor desejado. O número padrão de IPs públicos de saída gerenciados é 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Fornecer seus IPs públicos ou prefixos para saída

Ao usar um balanceador de carga de SKU *Standard*, o cluster do AKS cria automaticamente um IP público no mesmo grupo de recursos criado para o cluster do AKS e atribui o IP público ao balanceador de carga de SKU *Standard*. Como alternativa, você pode atribuir seu IP público no momento da criação do cluster ou atualizar as propriedades do balanceador de carga de um cluster existente.

Ao trazer vários endereços IP ou prefixos, você pode configurar vários serviços de backup ao definir o endereço IP por trás de um objeto de balanceador de carga. O ponto de extremidade de saída de nós específicos dependerá do serviço ao qual eles estão associados.

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>Pré-requisitos para traga seus endereços IP ou prefixos de IP
1. Você deve usar IPs públicos de SKU *Standard* para saída com seu balanceador de carga de SKU *Standard*. Você pode verificar o SKU dos seus IPs públicos usando o comando [az network public-ip show][az-network-public-ip-show]:

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. Os IPs públicos e prefixos de IP devem estar na mesma região e devem fazer parte da mesma assinatura que seu cluster do AKS.
 1. Os IPs públicos e prefixos de IP não podem ser IPs criados pelo AKS como um IP gerenciado. Verifique se os IPs especificados como IPs personalizados foram criados manualmente e não são o serviço AKS.
 1. Os IPs públicos e prefixos de IP não podem ser usados por outro recurso ou serviço.

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>Definir seu IP público ou prefixos em um cluster existente

Use o comando [az network public-ip show][az-network-public-ip-show] para listar as IDs dos seus IPs públicos.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

O comando acima mostra a ID para o IP público *myPublicIP* no grupo de recursos *myResourceGroup*.

Use o comando *az aks update* com o parâmetro *load-balancer-outbound-ips* para atualizar o cluster com seus IPs públicos.

O exemplo a seguir usa o parâmetro *load-balancer-outbound-ips* com as IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Você também pode usar prefixos de IP público para saída com seu balanceador de carga de SKU *Standard*. O seguinte exemplo usa o comando [az network public-ip prefix show][az-network-public-ip-prefix-show] para listar as IDs dos seus prefixos de IP público:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

O comando acima mostra a ID para o prefixo de IP público *myPublicIPPrefix* no grupo de recursos *myResourceGroup*.

O exemplo a seguir usa o parâmetro *load-balancer-outbound-ip-prefixes* com as IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definir seu IP público ou prefixos de IP no momento da criação do cluster

Talvez seja interessante trazer seus endereços IP ou prefixos de IP para saída no momento da criação do cluster para dar suporte a cenários como a adição de pontos de extremidade de saída à lista de permissões. Acrescente os mesmos parâmetros mostrados acima à etapa de criação do cluster para definir seus IPs públicos e prefixos de IP no início do ciclo de vida de um cluster.

Use o comando *az aks create* com o parâmetro *load-balancer-outbound-ips* para criar um cluster com seus IPs públicos no início.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Use o comando *az aks create* com o parâmetro *load-balancer-outbound-ip-prefixes* para criar um cluster com seus prefixos de IP públicos no início.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>Configurar portas de saída e tempo limite de ociosidade

> [!WARNING]
> A seção a seguir destina-se a cenários avançados de rede em maior escala ou à solução de problemas de esgotamento de SNAT com as configurações padrão. Você deve ter um inventário preciso de cota disponível para VMs e endereços IP antes de alterar *AllocatedOutboundPorts* ou *IdleTimeoutInMinutes* do valor padrão a fim de manter clusters íntegros.
> 
> A alteração dos valores para *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* pode alterar significativamente o comportamento da regra de saída para o balanceador de carga. Examine as [regras de saída do Load Balancer][azure-lb-outbound-rules-overview], as [regras de saída do balanceador de carga][azure-lb-outbound-rules] e as [conexões de saída no Azure][azure-lb-outbound-connections] antes de atualizar esses valores para entender totalmente o impacto de suas alterações.

As portas alocadas de saída e os tempos limite são usados para [SNAT][azure-lb-outbound-connections]. Por padrão, o balanceador de carga de SKU *Standard* usa a [atribuição automática do número de portas de saída com base no tamanho do pool de back-end][azure-lb-outbound-preallocatedports] e um tempo limite de ociosidade de 30 minutos para cada porta. Para ver esses valores, use [az network lb outbound-rule list][az-network-lb-outbound-rule-list] para mostrar a regra de saída para o balanceador de carga:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Os comandos anteriores listarão a regra de saída para o balanceador de carga, por exemplo:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

A saída de exemplo mostra o valor padrão para *AllocatedOutboundPorts* e *IdleTimeoutInMinutes*. Um valor de 0 para *AllocatedOutboundPorts* define o número de portas de saída usando atribuição automática para o número de portas de saída com base no tamanho do pool de back-end. Por exemplo, se o cluster tiver 50 nós ou menos, serão alocadas 1024 portas para cada nó.

Considere alterar a configuração de *allocatedOutboundPorts* ou *IdleTimeoutInMinutes* se você espera enfrentar o esgotamento de SNAT com base na configuração padrão acima. Cada endereço IP adicional permite 64 mil portas adicionais para alocação; no entanto, o Standard Load Balancer do Azure não aumenta automaticamente as portas por nó quando mais endereços IP são adicionados. Você pode alterar esses valores definindo os parâmetros *load-balancer-outbound-ports* e *load-balancer-idle-timeout*. Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Você deve [calcular a cota necessária][calculate-required-quota] antes de personalizar *allocatedOutboundPorts* para evitar problemas de conectividade ou dimensionamento. O valor especificado para *allocatedOutboundPorts* também deve ser um múltiplo de 8.

Você também pode usar os parâmetros *load-balancer-outbound-ports* e *load-balancer-idle-timeout* ao criar um cluster, mas também deve especificar *load-balancer-managed-outbound-ip-count*, *load-balancer-outbound-ips* ou *load-balancer-outbound-ip-prefixes* também.  Por exemplo:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Ao alterar os parâmetros *load-balancer-outbound-ports* e *load-balancer-idle-timeout* do padrão deles, isso afeta o comportamento do perfil do balanceador de carga, que afeta todo o cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Cota necessária para personalizar allocatedOutboundPorts
Você deve ter uma capacidade de IP de saída suficiente com base no número de suas VMs de nó e portas de saída alocadas desejadas. Para validar que você tem capacidade de IP de saída suficiente, use a seguinte fórmula: 
 
*outboundIPs* \* 64.000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Por exemplo, se você tiver três *nodeVMs* e 50 mil *desiredAllocatedOutboundPorts*, precisará ter pelo menos três *outboundIPs*. É recomendável incorporar a capacidade de IP de saída adicional além da que você precisa. Além disso, você precisa considerar o dimensionador automático do cluster e a possibilidade de atualizações do pool de nós ao calcular a capacidade do IP de saída. Para o dimensionador automático do cluster, examine a contagem de nós atual e a contagem máxima de nós e use o valor mais alto. Para atualizar, considere uma VM de nó adicional para cada pool de nós que permite a atualização.
 
Ao definir *IdleTimeoutInMinutes* como um valor diferente do padrão de 30 minutos, considere por quanto tempo suas cargas de trabalho precisarão de uma conexão de saída. Considere também que o valor do tempo limite padrão para um balanceador de carga de SKU *Standard* usado fora do AKS é de 4 minutos. Um valor *IdleTimeoutInMinutes* que reflete com mais precisão sua carga de trabalho do AKS específica pode ajudar a diminuir o esgotamento de SNAT causado pela associação de conexões que não são mais usadas.

## <a name="restrict-access-to-specific-ip-ranges"></a>Restringir o acesso a intervalos de IP específicos

Por padrão, o NSG (Grupo de Segurança de Rede) associado à rede virtual do balanceador de carga tem uma regra para permitir todo o tráfego externo de saída. Você pode atualizar essa regra para somente permitir intervalos de IP específicos para o tráfego de entrada. O seguinte manifesto usa *loadBalancerSourceRanges* para especificar um novo intervalo de IP para o tráfego externo de entrada:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

O exemplo acima atualiza a regra para permitir somente o tráfego externo de entrada do intervalo *MY_EXTERNAL_IP_RANGE*. Mais informações sobre como usar esse método para restringir o acesso ao serviço de balanceador de carga estão disponíveis na [documentação do Kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços do Kubernetes na [documentação dos serviços do Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
