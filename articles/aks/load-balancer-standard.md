---
title: Use um balanceador de carga SKU padrão no Azure Kubernetes Service (AKS)
description: Aprenda a usar um balanceador de carga com um SKU padrão para expor seus serviços com o Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252903"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Use um balanceador de carga SKU padrão no Azure Kubernetes Service (AKS)

Para fornecer acesso a aplicativos via Kubernetes serviços do tipo `LoadBalancer` no Azure Kubernetes Service (AKS), você pode usar um Azure Load Balancer. Um balanceador de carga em execução em AKS pode ser usado como um balanceador de carga interno ou externo. Um balanceador de carga interno torna um serviço Kubernetes acessível apenas para aplicativos em execução na mesma rede virtual que o cluster AKS. Um balanceador de carga externo recebe um ou mais IPs públicos para ingestão e torna um serviço Kubernetes acessível externamente usando os IPs públicos.

O Balanceador de carga do Azure está disponível em dois SKUs - *Básico* e *Padrão*. Por padrão, o *SKU padrão* é usado quando você cria um cluster AKS. O uso de um balanceador de carga *SKU padrão* fornece recursos e funcionalidades adicionais, como um tamanho maior de pool de back-end e zonas de disponibilidade. É importante que você entenda as diferenças entre balanceadores de carga *Padrão* e *Básico* antes de escolher qual usar. Depois de criar um cluster AKS, não é possível alterar o SKU do balanceador de carga para esse cluster. Para obter mais informações sobre as SKUs *Básicas* e *Padrão,* consulte [a comparação SKU do balanceador de carga Do Zure][azure-lb-comparison].

Este artigo assume uma compreensão básica dos conceitos kubernetes e azure Load Balancer. Para obter mais informações, consulte [os conceitos centrais da Kubernetes para o Azure Kubernetes Service (AKS)][kubernetes-concepts] e [o que é o Azure Load Balancer?][azure-lb].

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o CLI localmente, este artigo requer que você esteja executando a versão 2.0.81 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS com o *Balancer de* carga Padrão SKU Azure. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

O principal principal de serviço de cluster da AKS também precisa de permissão para gerenciar recursos de rede se você usar uma sub-rede ou grupo de recursos existente. Em geral, atribua a função *de contribuinte* da Rede ao seu principal de serviço nos recursos delegados. Para obter mais informações sobre permissões, consulte [o acesso do Delegate AKS a outros recursos do Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Movendo-se de um balanceador de carga SKU básico para sku padrão

Se você tiver um cluster existente com o Basic SKU Load Balancer, existem diferenças comportamentais importantes a serem observadas ao migrar para usar um cluster com o Balanceador de Carga SKU Padrão.

Por exemplo, fazer implantações azul/verde para migrar clusters é uma prática comum, dado que o `load-balancer-sku` tipo de cluster só pode ser definido no tempo de criação de cluster. No entanto, os Balanceadores básicos de carga *SKU* usam endereços IP *SKU básicos* que não são compatíveis com balanceadores de carga *SKU padrão,* pois exigem endereços IP *SKU padrão.* Ao migrar clusters para atualizar SKUs do Balancer de carga, um novo endereço IP com um SKU de endereço IP compatível será necessário.

Para obter mais considerações sobre como migrar clusters, visite [nossa documentação sobre considerações de migração](aks-migration.md) para ver uma lista de tópicos importantes a serem considerados ao migrar. As limitações abaixo também são importantes diferenças comportamentais a serem observadas ao usar balanceadores de carga SKU padrão em AKS.

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que suportam um balanceador de carga com o *SKU padrão:*

* Pelo menos um prefixo IP ou IP público é necessário para permitir o tráfego de saída do cluster AKS. O prefixo ip público ou IP também é necessário para manter a conectividade entre os nós do plano de controle e do agente, bem como para manter a compatibilidade com as versões anteriores do AKS. Você tem as seguintes opções para especificar IPs públicos ou prefixos IP com um balanceador de carga *SKU padrão:*
    * Forneça seus próprios IPs públicos.
    * Forneça seus próprios prefixos IP públicos.
    * Especifique um número de até 100 para permitir que o cluster AKS crie tantos IPs públicos *Padrão* SKU no mesmo grupo de recursos criado como o cluster AKS, que geralmente é nomeado com *MC_* no início. A AKS atribui o IP público ao *balanceador de* carga Padrão SKU. Por padrão, um IP público será criado automaticamente no mesmo grupo de recursos do cluster AKS, se nenhum IP público, prefixo IP público ou número de IPs for especificado. Você também deve permitir endereços públicos e evitar criar qualquer Política Do Azure que proíba a criação de IP.
* Ao usar o *Standard* SKU para um balanceador de carga, você deve usar kubernetes versão *1.13 ou superior*.
* A definição do Balanceador de carga SKU só pode ser feita quando você cria um cluster AKS. Não é possível alterar o SKU do balanceador de carga após a criação de um cluster AKS.
* Você só pode usar um tipo de balanceador de carga SKU (Basic ou Standard) em um único cluster.
* *Padrão* Os balanceadores de carga SKU só suportam endereços IP *Padrão* SKU.

## <a name="use-the-standard-sku-load-balancer"></a>Use o *balanceador de* carga Padrão SKU

Quando você cria um cluster AKS, por padrão, o *balanceador de* carga Padrão SKU é usado quando você executa serviços nesse cluster. Por exemplo, [o quickstart usando o Azure CLI][aks-quickstart-cli] implanta um aplicativo de exemplo que usa o balanceador de carga *SKU* padrão.

> [!IMPORTANT]
> Os endereços IP públicos podem ser evitados personalizando uma rota definida pelo usuário (UDR). Especificar o tipo de saída de um cluster AKS, pois o UDR pode pular o provisionamento de IP e a configuração do pool de backend para o balanceador de carga aks criado pela AKS. Consulte [a configuração `outboundType` de um cluster para 'userDefinedRouting'](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Configure o balanceador de carga para ser interno

Você também pode configurar o balanceador de carga para ser interno e não expor um IP público. Para configurar o balanceador de `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` carga como interno, adicione como uma anotação ao serviço *LoadBalancer.* Você pode ver um manifesto de inhame exemplo, bem como mais detalhes sobre um balanceador de carga interna [aqui][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Dimensione o número de IPs públicos gerenciados

Ao usar um balanceador de carga *SKU padrão* com IPs públicos de saída gerenciados, que são criados por padrão, você pode dimensionar o número de IPs públicos de saída gerenciados usando o parâmetro *de contagem ip gerenciada pelo balanceador de carga.*

Para atualizar um cluster existente, execute o seguinte comando. Esse parâmetro também pode ser definido no cluster create-time para ter vários IPs públicos de saída gerenciados.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

O exemplo acima define o número de IPs públicos de saída gerenciados para *2* para o cluster *myAKSCluster* no *myResourceGroup*. 

Você também pode usar o parâmetro *de contagem ip-ip gerenciado pelo balanceador de carga* para definir o `--load-balancer-managed-outbound-ip-count` número inicial de IPs públicos de saída gerenciados ao criar seu cluster, anexando o parâmetro e definindo-o para o valor desejado. O número padrão de IPs públicos de saída gerenciados é 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Forneça seus próprios IPs públicos ou prefixos para egresso

Ao usar um balanceador de carga *SKU padrão,* o cluster AKS cria automaticamente um IP público no mesmo grupo de recursos criado para o cluster AKS e atribui o IP público ao balanceador de carga *Standard* SKU. Alternativamente, você pode atribuir seu próprio IP público no momento da criação do cluster ou atualizar as propriedades do balanceador de carga de um cluster existente.

Ao trazer vários endereços IP ou prefixos, você é capaz de definir vários serviços de backup ao definir o endereço IP atrás de um único objeto balanceador de carga. O ponto final de saída de nós específicos dependerá do serviço com o qual eles estão associados.

> [!IMPORTANT]
> Você deve usar *IPs* públicos Padrão SKU para saída com seu *Balanceador de* carga Standard SKU. Você pode verificar o SKU de seus IPs públicos usando o comando [az network public-ip show:][az-network-public-ip-show]
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Use o comando [az network public-ip show][az-network-public-ip-show] para listar os IDs de seus IPs públicos.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

O comando acima mostra o ID do IP público *myPublicIP* no grupo de recursos *myResourceGroup.*

Use o comando *az aks update* com o parâmetro *load-balancer-outbound-ips* para atualizar seu cluster com seus IPs públicos.

O exemplo a seguir usa o parâmetro *load-balancer-outbound-ips* com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Você também pode usar prefixos IP públicos para saída com o balanceador de carga *SKU* padrão. O exemplo a seguir usa o comando [az network public-ip prefix show para][az-network-public-ip-prefix-show] listar os IDs de seus prefixos IP públicos:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

O comando acima mostra o ID do prefixo IP público *myPublicIPPrefix* no grupo de recursos *myResourceGroup.*

O exemplo a seguir usa o parâmetro *load-balancer-outbound-ip-prefixes* com os IDs do comando anterior.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Os iPs públicos e prefixos IP devem estar na mesma região e parte da mesma assinatura do seu cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Defina seu próprio IP público ou prefixos no tempo de criação de cluster

Você pode querer trazer seus próprios endereços IP ou prefixos IP para egresso na hora de criação de clusterpara suportar cenários como pontos finais de egressos de whitelisting. Anexar os mesmos parâmetros mostrados acima à etapa de criação de cluster para definir seus próprios IPs públicos e prefixos IP no início do ciclo de vida de um cluster.

Use o comando *az aks create* com o parâmetro *load-balancer-outbound-ips* para criar um novo cluster com seus IPs públicos no início.

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

Use o comando *az aks create* com o parâmetro *load-balancer-outbound-ip-prefixes* para criar um novo cluster com seus prefixos IP públicos no início.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Configurar portas de saída e tempo limite ocioso

> [!WARNING]
> A seção a seguir destina-se a cenários avançados de rede de maior escala ou para resolver problemas de exaustão do SNAT com as configurações padrão. Você deve ter um inventário preciso da cota disponível para VMs e endereços IP antes de alterar *AllocatedOutboundPorts* ou *IdleTimeoutInMinutes* de seu valor padrão, a fim de manter clusters saudáveis.
> 
> Alterar os valores de *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* pode alterar significativamente o comportamento da regra de saída para o balanceador de carga. Revise as regras de saída do [Balancer de carga,][azure-lb-outbound-rules-overview] [as regras de saída do Balancer][azure-lb-outbound-rules]de carga e as [conexões de saída no Azure][azure-lb-outbound-connections] antes de atualizar esses valores para entender completamente o impacto de suas alterações.

As portas alocadas de saída e seus tempos limite ociosos são usados para [o SNAT][azure-lb-outbound-connections]. Por padrão, o balanceador de carga *SKU padrão* usa [atribuição automática para o número de portas de saída com base no tamanho do pool de backend][azure-lb-outbound-preallocatedports] e um tempo limite ocioso de 30 minutos para cada porta. Para ver esses valores, use [a lista de regras de saída da rede az lb][az-network-lb-outbound-rule-list] para mostrar a regra de saída para o balanceador de carga:

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

A saída de exemplo mostra o valor padrão de *AllocatedOutboundPorts* e *IdleTimeoutOutInMinutes*. Um valor de 0 para *AllocatedOutboundPorts* define o número de portas de saída usando atribuição automática para o número de portas de saída com base no tamanho do pool de backend. Por exemplo, se o cluster tiver 50 ou menos nós, 1024 portas para cada nó serão alocadas.

Considere alterar a configuração de *BoundboundPorts alocado* ou *IdleTimeoutInMinutes* se você espera enfrentar a exaustão do SNAT com base na configuração padrão acima. Cada endereço IP adicional permite 64.000 portas adicionais para alocação, no entanto, o Azure Standard Load Balancer não aumenta automaticamente as portas por nó quando mais endereços IP são adicionados. Você pode alterar esses valores definindo os *parâmetros de saída do balanceador de carga* e do *balanceador de carga- ocioso.* Por exemplo: 

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Você deve [calcular sua cota necessária][calculate-required-quota] antes de personalizar o *OutboundPorts alocado* para evitar problemas de conectividade ou de dimensionamento. O valor especificado para *alocadoOutboundPorts* também deve ser um múltiplo de 8.

Você também pode usar os parâmetros de *saída-balancer de carga* e de tempo limite *de carga-balancer-ocioso* ao criar um cluster, mas também deve especificar os ip-ip-ip-ip gerenciados pelo *balanceador de carga,* *os ip-ip-balanceadores de carga*ou os prefixos de *saída-balancer ou de load-balancer-out-ip-.*  Por exemplo: 

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

Ao alterar os parâmetros de saída do *balanceador de carga* e do *tempo de tempo de ociosidade do balanceador* de carga a partir de seu padrão, isso afeta o comportamento do perfil do balanceador de carga, que afeta todo o cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Cota necessária para personalização alocadaOutboundPorts
Você deve ter capacidade IP de saída suficiente com base no número de vMs de nó e portas de saída alocadas desejadas. Para validar você tem capacidade IP de saída suficiente, use a seguinte fórmula: 
 
*ideboundIPs* \* 64.000 \> *nodeVMs* \* *desejadosAllocatedOutboundPorts*.
 
Por exemplo, se você tiver 3 *nodeVMs*e 50.000 *portas outboundalocadas desejadas,* você precisa ter pelo menos 3 *IPs de saída*. Recomenda-se que você incorpore capacidade IP de saída adicional ao que você precisa. Além disso, você deve explicar o cluster autoscaler e a possibilidade de upgrades de pool de nó ao calcular a capacidade ip de saída. Para o cluster autoscaler, revise a contagem de nós atuais e a contagem máxima de nó e use o valor mais alto. Para atualização, contabilize um vm de nó adicional para cada pool de nó que permita a atualização.
 
Ao definir *IdleTimeoutInMinutes* para um valor diferente do padrão de 30 minutos, considere quanto tempo suas cargas de trabalho precisarão de uma conexão de saída. Considere também que o valor de tempo padrão para um balanceador de carga *SKU padrão* usado fora do AKS é de 4 minutos. Um valor *IdleTimeoutInMinutes* que reflete com mais precisão sua carga de trabalho específica do AKS pode ajudar a diminuir a exaustão do SNAT causada pela vinculação que não está mais sendo usada.

## <a name="restrict-access-to-specific-ip-ranges"></a>Restringir o acesso a faixas de IP específicas

O Grupo de Segurança de Rede (NSG) associado à rede virtual para o balanceador de carga, por padrão, tem uma regra para permitir todo o tráfego externo de entrada. Você pode atualizar esta regra para permitir apenas faixas IP específicas para tráfego de entrada. O manifesto a seguir usa *loadBalancerSourceRanges* para especificar um novo intervalo IP para tráfego externo de entrada:

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

O exemplo acima atualiza a regra para permitir apenas tráfego externo de entrada a partir da faixa *de MY_EXTERNAL_IP_RANGE.* Mais informações sobre o uso deste método para restringir o acesso ao serviço balanceador de carga estão disponíveis na [documentação kubernetes][kubernetes-cloud-provider-firewall].

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
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
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
