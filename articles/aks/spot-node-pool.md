---
title: Pré-visualização - Adicione um pool de nó spot a um cluster Azure Kubernetes Service (AKS)
description: Aprenda a adicionar um pool de nó spot a um cluster Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622052"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Pré-visualização - Adicione um pool de nó spot a um cluster Azure Kubernetes Service (AKS)

Uma piscina de nó spot é um pool de nó apoiado por um [conjunto de escala de máquina virtual spot][vmss-spot]. O uso de VMs spot para nós com seu cluster AKS permite que você aproveite a capacidade não utilizada no Azure com uma economia significativa de custos. A quantidade de capacidade disponível não utilizada variará de acordo com muitos fatores, incluindo tamanho do nó, região e hora do dia.

Ao implantar um pool de nós spot, o Azure alocará os nós spot se houver capacidade disponível. Mas não há SLA para os nódulos. Um conjunto de escala spot que apoia o pool de nó spot é implantado em um único domínio de falha e não oferece garantias de alta disponibilidade. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará os nódulos spot.

Os nós spot são ótimos para cargas de trabalho que podem lidar com interrupções, rescisões antecipadas ou despejos. Por exemplo, cargas de trabalho, como trabalhos de processamento em lote, ambientes de desenvolvimento e testes e grandes cargas de trabalho computacionais podem ser bons candidatos a serem agendados em um pool de nós no ponto.

Neste artigo, você adiciona um pool de nó de ponto secundário a um cluster Azure Kubernetes Service (AKS) existente.

Este artigo assume uma compreensão básica dos conceitos kubernetes e azure Load Balancer. Para obter mais informações, confira [Principais conceitos do Kubernetes para o AKS (Serviço de Kubernetes do Azure)][kubernetes-concepts].

Esse recurso está atualmente na visualização.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Quando você cria um cluster para usar um pool de nó spot, esse cluster também deve usar conjuntos de escala de máquina virtual para pools de nó e o *balanceador de* carga Padrão SKU. Você também deve adicionar um pool de nó adicional depois de criar seu cluster para usar uma piscina de nó spot. A adição de um pool adicional de nó é coberta em uma etapa posterior, mas primeiro você precisa ativar um recurso de visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são self-service, opt-in. Eles são fornecidos para coletar feedbacks e bugs de nossa comunidade. Na pré-visualização, esses recursos não são feitos para uso de produção. Os recursos na pré-visualização pública são suportados pelo "melhor esforço". A assistência das equipes de suporte técnico da AKS está disponível apenas durante o horário comercial do Pacífico (PST). Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte da AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Registre o recurso de visualização spotpoolpreview

Para criar um cluster AKS que usa um pool de nó spot, você deve ativar o sinalizador de recurso *spotpoolpreview* em sua assinatura. Esse recurso fornece o conjunto mais recente de aprimoramentos de serviço ao configurar um cluster.

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, você não pode atualmente desregistrar esse recurso. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS criados na assinatura. Não habilite recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar feedback.

Registre o sinalizador de recurso *spotpoolpreview* usando o comando [az feature register][az-feature-register] como mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft.ContainerService* usando o comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS que usa um pool de nó spot, você precisa da versão CLI de *visualização aks* 0.4.32 ou superior. Instale a extensão *aks-preview* Azure CLI usando o comando [az extension add][az-extension-add] e verifique se há atualizações disponíveis usando o comando [az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS com um pool de nó spot:

* Um pool de nó sumido não pode ser o pool de nó padrão do cluster. Uma piscina de nó spot só pode ser usada para uma piscina secundária.
* Você não pode atualizar uma piscina de nó local, uma vez que as piscinas de nó local não podem garantir cordão e drenagem. Você deve substituir o pool de nó local existente por um novo para fazer operações, como atualizar a versão kubernetes. Para substituir uma piscina de nó spot, crie uma nova piscina de nó spot com uma versão diferente do Kubernetes, espere até que seu status esteja *pronto*e, em seguida, remova a piscina de nó antigo.
* O plano de controle e as piscinas de nó não podem ser atualizados ao mesmo tempo. Você deve atualizá-los separadamente ou remover a piscina de nó local para atualizar o plano de controle e as piscinas de nó restantes ao mesmo tempo.
* Um pool de nó sumário deve usar conjuntos de escala de máquina virtual.
* Não é possível alterar ScaleSetPriority ou SpotMaxPrice após a criação.
* Ao definir spotMaxPrice, o valor deve ser -1 ou um valor positivo com até cinco casas decimais.
* Uma piscina de nó spot terá o rótulo *kubernetes.azure.com/scalesetpriority:spot*, o *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*de manchas , e as cápsulas do sistema terão anti-afinidade.
* Você deve adicionar uma [tolerância correspondente][spot-toleration] para agendar cargas de trabalho em um pool de nó sumido.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Adicione uma piscina de nó spot a um cluster AKS

Você deve adicionar um pool de nó spot a um cluster existente que tenha vários pools de nó ativados. Mais detalhes sobre a criação de um cluster AKS com vários pools de nós estão disponíveis [aqui][use-multiple-node-pools].

Crie uma piscina de nó usando o [az aks nodepool add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Por padrão, você cria um pool de nó com uma *prioridade* do *Regular* em seu cluster AKS quando você cria um cluster com vários pools de nó. O comando acima adiciona um pool de nó auxiliar a um cluster AKS existente com *prioridade* de *Spot*. A *prioridade* do *Spot* faz da piscina de nó uma piscina de nó local. O parâmetro *de política de despejo* é definido como *Excluir* no exemplo acima, que é o valor padrão. Quando você define a [política de despejo][eviction-policy] como *Excluir,* os nós no conjunto de escala subjacente do pool de nós são excluídos quando são despejados. Você também pode definir a política de despejo para *Deallocate*. Quando você define a política de despejo para *Deallocate,* os nós no conjunto de escala subjacente são definidos para o estado desalocado após o despejo. Nós na contagem de estado desalocado parada contra sua cota de cálculo e podem causar problemas com o dimensionamento ou atualização de cluster. Os valores *de prioridade* e *política de despejo* só podem ser definidos durante a criação do pool de nó. Esses valores não podem ser atualizados mais tarde.

O comando também permite que o [cluster autoscaler][cluster-autoscaler], que é recomendado para usar com piscinas de nó spot. Com base nas cargas de trabalho em execução em seu cluster, o cluster autoscaler escala e reduz o número de nódulos no pool de nós. Para pools de nós spot, o cluster autoscaler aumentará o número de nós após um despejo se ainda forem necessários nós adicionais. Se você alterar o número máximo de nós que um pool `maxCount` de nó pode ter, você também precisa ajustar o valor associado ao cluster autoscaler. Se você não usar um cluster autoscaler, após o despejo, o pool spot eventualmente diminuirá para zero e exigirá uma operação manual para receber quaisquer nódulos de ponto adicionais.

> [!Important]
> Apenas agende cargas de trabalho em pools de nó sumidos que possam lidar com interrupções, como trabalhos de processamento em lote e ambientes de teste. Recomenda-se que você [configure manchas e tolerâncias][taints-tolerations] no seu pool de nós para garantir que apenas cargas de trabalho que possam lidar com despejos de nós sejam agendadas em uma piscina de nós noponto. Por exemplo, o comando acima ny default adiciona uma mancha de *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* para que apenas pods com uma tolerância correspondente sejam agendados neste nó.

## <a name="verify-the-spot-node-pool"></a>Verifique a piscina de nó local

Para verificar se o pool de nó foi adicionado como uma piscina de nó spot:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Confirmar *escalaSetPriority* é *Spot*.

Para agendar um pod para ser executado em um nó spot, adicione uma tolerância que corresponda à mancha aplicada ao seu nó local. O exemplo a seguir mostra uma parte de um arquivo de inhame que define uma tolerância que corresponde a uma *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* mancha usada na etapa anterior.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Quando um pod com essa tolerância é implantado, os Kubernetes podem agendar com sucesso o pod nos nós com a mancha aplicada.

## <a name="max-price-for-a-spot-pool"></a>Preço máximo para uma piscina spot
[Os preços para instâncias spot são variáveis,][pricing-spot]com base na região e no SKU. Para obter mais informações, consulte preços para [Linux][pricing-linux] e [Windows][pricing-windows].

Com preços variáveis, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor *0,98765* seria um preço máximo de US $ 0,98765 USD por hora. Se você definir o preço máximo para *-1*, a instância não será despejada com base no preço. O preço, por exemplo, será o preço atual para spot ou o preço para uma instância padrão, o que for menor, desde que haja capacidade e cota disponíveis.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como adicionar um pool de nó spot a um cluster AKS. Para obter mais informações sobre como controlar pods em pools de nós, consulte [As melhores práticas para recursos avançados do agendador no AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md