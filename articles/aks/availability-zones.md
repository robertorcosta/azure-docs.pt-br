---
title: Use zonas de disponibilidade no Azure Kubernetes Service (AKS)
description: Saiba como criar um cluster que distribui árfinais entre zonas de disponibilidade no Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596803"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Crie um cluster Azure Kubernetes Service (AKS) que usa zonas de disponibilidade

Um cluster Azure Kubernetes Service (AKS) distribui recursos como os ádeis e o armazenamento em seções lógicas da infra-estrutura de computação azure subjacente. Este modelo de implantação garante que os números sejam executados em domínios separados de atualização e falha em um único data center do Azure. Os clusters AKS implantados com esse comportamento padrão fornecem um alto nível de disponibilidade para proteger contra uma falha de hardware ou evento de manutenção planejado.

Para fornecer um nível mais alto de disponibilidade para seus aplicativos, os clusters AKS podem ser distribuídos em zonas de disponibilidade. Essas zonas são datacenters fisicamente separados dentro de uma determinada região. Quando os componentes do cluster são distribuídos em várias zonas, o cluster AKS é capaz de tolerar uma falha em uma dessas zonas. Seus aplicativos e operações de gerenciamento continuam disponíveis mesmo que um data center inteiro tenha um problema.

Este artigo mostra como criar um cluster AKS e distribuir os componentes do nó entre as regiões de disponibilidade.

## <a name="before-you-begin"></a>Antes de começar

Você precisa da versão 2.0.76 do Azure CLI ou posteriormente instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitações e disponibilidade da região

Atualmente, os clusters AKS podem ser criados usando zonas de disponibilidade nas seguintes regiões:

* Centro dos EUA
* Leste dos EUA 2
* Leste dos EUA
* França Central
* Leste do Japão
* Norte da Europa
* Sudeste Asiático
* Sul do Reino Unido
* Europa Ocidental
* Oeste dos EUA 2

As seguintes limitações se aplicam quando você cria um cluster AKS usando zonas de disponibilidade:

* Você só pode habilitar zonas de disponibilidade quando o cluster for criado.
* As configurações da zona de disponibilidade não podem ser atualizadas após a criação do cluster. Você também não pode atualizar um cluster de zona não disponibilidade existente para usar zonas de disponibilidade.
* Você não pode desativar as zonas de disponibilidade de um cluster AKS depois de criado.
* O tamanho do nó (VM SKU) selecionado deve estar disponível em todas as zonas de disponibilidade.
* Clusters com zonas de disponibilidade habilitadas exigem o uso de Balanceadores de carga padrão do Azure para distribuição entre regiões.
* Você deve usar kubernetes versão 1.13.5 ou superior para implantar Balanceadores de carga padrão.

Os clusters AKS que usam zonas de disponibilidade devem usar o SKU *padrão* de balanceador de carga do Azure, que é o valor padrão para o tipo balanceador de carga. Este tipo de balanceador de carga só pode ser definido no tempo de criação de cluster. Para obter mais informações e as limitações do balanceador de carga padrão, consulte [as limitações padrão de Balanceador de carga do Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitações dos discos azure

Os volumes que usam discos gerenciados pelo Azure não são atualmente recursos zonais. Os pods reagendados em uma região diferente da região original não podem reanexar seus discos(s) anteriores. Recomenda-se executar cargas de trabalho apátridas que não requerem armazenamento persistente que possa se deparar com problemas zonais.

Se você tiver que executar cargas de trabalho estaduais, use manchas e tolerâncias nas especificações do pod para dizer ao agendador Kubernetes para criar pods na mesma zona que seus discos. Alternativamente, use o armazenamento baseado em rede, como arquivos Azure, que podem ser anexados a pods à medida que são programados entre zonas.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Visão geral das zonas de disponibilidade para clusters AKS

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege seus aplicativos e dados contra falhas no data center. As zonas são locais físicos únicos dentro de uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das zonas de disponibilidade dentro de uma região protege os aplicativos e os dados contra falhas do datacenter. Os serviços redundantes de zona replicam seus aplicativos e dados em regiões de disponibilidade para proteger contra pontos únicos de falha.

Para obter mais informações, consulte [Quais são as zonas de disponibilidade no Azure?][az-overview].

Os clusters AKS que são implantados usando zonas de disponibilidade podem distribuir nós em várias regiões dentro de uma única região. Por exemplo, um cluster na região *leste dos EUA 2* pode criar nós nas três zonas de disponibilidade no *Leste dos EUA 2*. Essa distribuição dos recursos de cluster AKS melhora a disponibilidade de cluster, pois eles são resistentes à falha de uma região específica.

![Distribuição de nó AKS em zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Em uma paralisação de zona, os nós podem ser rebalanceados manualmente ou usando o cluster autoscaler. Se uma única região ficar indisponível, seus aplicativos continuarão a ser executados.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Crie um cluster AKS entre as zonas de disponibilidade

Quando você cria um cluster usando o comando `--zones` [az aks create,][az-aks-create] o parâmetro define em quais nós de agente de zonas são implantados. Os componentes do plano de controle AKS para o cluster também estão `--zones` espalhados por regiões na configuração mais alta disponível quando você define o parâmetro no momento da criação do cluster.

Se você não definir nenhuma zona para o pool de agentes padrão ao criar um cluster AKS, os componentes do plano de controle AKS para o cluster não usarão zonas de disponibilidade. Você pode adicionar piscinas adicionais de nó usando o comando `--zones` [az aks nodepool add][az-aks-nodepool-add] e especificar para esses novos nados, no entanto os componentes do plano de controle permanecem sem a consciência da zona de disponibilidade. Você não pode mudar a consciência da zona para um pool de nó ou os componentes do plano de controle AKS uma vez que eles são implantados.

O exemplo a seguir cria um cluster AKS chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. Um total de *3* nós são criados - um agente na zona *1*, um em *2*, e depois um em *3*. Os componentes do plano de controle AKS também são distribuídos entre regiões na configuração mais alta disponível, uma vez que são definidos como parte do processo de criação de cluster.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Leva alguns minutos para o cluster do AKS ser criado.

## <a name="verify-node-distribution-across-zones"></a>Verifique a distribuição do nó entre as regiões

Quando o cluster estiver pronto, liste os nós do agente na escala definida para ver em que zona de disponibilidade eles estão implantados.

Primeiro, obtenha as credenciais de cluster AKS usando o comando [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, use o comando [kubectl describe][kubectl-describe] para listar os nós no cluster. Filtrar o valor *failure-domain.beta.kubernetes.io/zone* como mostrado no exemplo a seguir:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

O exemplo de saída a seguir mostra os três nós distribuídos entre a região especificada e zonas de disponibilidade, como *eastus2-1* para a primeira zona de disponibilidade e *eastus2-2* para a segunda zona de disponibilidade:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

À medida que você adiciona nomes adicionais a um pool de agentes, a plataforma Azure distribui automaticamente as VMs subjacentes entre as regiões de disponibilidade especificadas.

Note que nas versões kubernetes mais recentes (1.17.0 e `topology.kubernetes.io/zone` posteriores), a AKS está usando o rótulo mais novo, além do depreciado `failure-domain.beta.kubernetes.io/zone`.

## <a name="verify-pod-distribution-across-zones"></a>Verificar a distribuição de pods entre as regiões

Como documentado em [Rótulos Bem Conhecidos, Anotações e Manchas,][kubectl-well_known_labels]o Kubernetes usa o `failure-domain.beta.kubernetes.io/zone` rótulo para distribuir automaticamente pods em um controlador de replicação ou serviço entre as diferentes regiões disponíveis. Para testar isso, você pode escalar seu cluster de 3 a 5 nós, para verificar a propagação correta do pod:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Quando a operação de escala for concluída `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` após alguns minutos, o comando deve dar uma saída semelhante a esta amostra:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Como você pode ver, agora temos dois nós adicionais nas zonas 1 e 2. Você pode implantar um aplicativo composto por três réplicas. Usaremos o NGINX como exemplo:

```console
kubectl run nginx --image=nginx --replicas=3
```

Se você verificar se os nós estão sendo executados, você verá que os pods estão sendo executados nos pods correspondentes a três zonas de disponibilidade diferentes. Por exemplo, `kubectl describe pod | grep -e "^Name:" -e "^Node:"` com o comando você obteria uma saída semelhante a esta:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Como você pode ver na saída anterior, o primeiro pod está sendo executado `eastus2-1`no nó 0, que está localizado na zona de disponibilidade . A segunda cápsula está sendo executado no `eastus2-3`nó 2, que corresponde a `eastus2-2`, e o terceiro no nó 4, em . Sem qualquer configuração adicional, o Kubernetes está espalhando os pods corretamente em todas as três zonas de disponibilidade.

## <a name="next-steps"></a>Próximas etapas

Este artigo detalhou como criar um cluster AKS que usa zonas de disponibilidade. Para obter mais considerações sobre clusters altamente disponíveis, consulte [As melhores práticas para continuidade de negócios e recuperação de desastres em AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
