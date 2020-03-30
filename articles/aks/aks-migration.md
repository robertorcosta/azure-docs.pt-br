---
title: Migrar para o Azure Kubernetes Service (AKS)
description: Migrar para o Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624807"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrar para o Azure Kubernetes Service (AKS)

Este artigo ajuda você a planejar e executar uma migração bem-sucedida para o Azure Kubernetes Service (AKS). Para ajudá-lo a tomar decisões importantes, este guia fornece detalhes para a configuração recomendada atual para AKS. Este artigo não abrange todos os cenários e, se for o caso, o artigo contém links para informações mais detalhadas para o planejamento de uma migração bem-sucedida.

Este documento pode ser usado para ajudar a apoiar os seguintes cenários:

* Migração de um cluster AKS apoiado por [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) para [conjuntos de escala de máquinavirtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrando um cluster AKS para usar um [balanceador de carga SKU padrão](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migração do [Azure Container Service (ACS) - aposentando-se em 31 de janeiro de 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) para a AKS
* Migração do [motor AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) para AKS
* Migração de clusters Kubernetes não baseados em Azure para AKS

Ao migrar, certifique-se de que a versão target do Kubernetes esteja dentro da janela suportada para AKS. Se usar uma versão mais antiga, ela pode não estar dentro do alcance suportado e exigir versões de atualização para ser suportada pelo AKS. Consulte [as versões do Kubernetes suportadas pela AKS](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) para obter mais informações.

Se você está migrando para uma versão mais recente do Kubernetes, revise a [versão do Kubernetes e a política de suporte à distorção de versão](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Várias ferramentas de código aberto podem ajudar na sua migração, dependendo do seu cenário:

* [Velero](https://velero.io/) (Requer Kubernetes 1.7+)
* [Extensão azure Kube CLI](https://github.com/yaron2/azure-kube-cli)
* [Reshifter](https://github.com/mhausenblas/reshifter)

Neste artigo, resumiremos os detalhes da migração para:

> [!div class="checklist"]
> * AKS com balanceador de carga padrão e conjuntos de escala de máquina virtual
> * Serviços Azure anexados existentes
> * Garantir cotas válidas
> * Alta disponibilidade e continuidade de negócios
> * Considerações para aplicações apátridas
> * Considerações para aplicações estaduais
> * Implantação da configuração de cluster

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS com balanceador de carga padrão e conjuntos de escala de máquina virtual

O AKS é um serviço gerenciado que oferece recursos exclusivos com menor sobrecarga de gerenciamento. Como resultado de ser um serviço gerenciado, você deve selecionar a partir de um conjunto de [regiões](https://docs.microsoft.com/azure/aks/quotas-skus-regions) que a AKS suporta. A transição do cluster existente para o AKS pode exigir a modificação de seus aplicativos existentes para que eles permaneçam saudáveis no plano de controle gerenciado da AKS.

Recomendamos o uso de clusters AKS apoiados por [Conjuntos de Escala de Máquinavirtual](https://docs.microsoft.com/azure/virtual-machine-scale-sets) e o [Balanceador de Carga Padrão Do Azure](https://docs.microsoft.com/azure/aks/load-balancer-standard) para garantir que você obtenha recursos como vários pools de nós, [Zonas de disponibilidade,](https://docs.microsoft.com/azure/availability-zones/az-overview) [faixas IP autorizadas,](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges) [Cluster Autoscaler,](https://docs.microsoft.com/azure/aks/cluster-autoscaler) [Azure Policy for AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)e outros novos recursos à medida que são [lançados.](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)

Os clusters AKS apoiados por [virtual-disponibilidade de máquinas não](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) têm suporte para muitos desses recursos.

O exemplo a seguir cria um cluster AKS com um pool de nó único apoiado por um conjunto de escala de máquina virtual. Ele usa um balanceador de carga padrão. Ele também habilita o cluster autoscaler no pool de nó para o cluster e define um mínimo de *1* e máximo de *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Serviços Azure anexados existentes

Ao migrar clusters, você pode ter anexado serviços externos do Azure. Estes não requerem recreação de recursos, mas exigirão a atualização de conexões de clusters anteriores a novos para manter a funcionalidade.

* Registro de Contêiner do Azure
* Log Analytics
* Application Insights
* Gerenciador de Tráfego
* Conta de Armazenamento
* Bancos de dados externos

## <a name="ensure-valid-quotas"></a>Garantir cotas válidas

Como as máquinas virtuais serão implantadas na sua assinatura durante a migração, você precisa verificar se suas cotas e limites são suficientes para esses recursos. Você pode precisar solicitar um aumento na [cota de vCPU](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Você pode precisar solicitar um aumento das [cotas de rede](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) para garantir que você não esgote os IPs. Consulte [as faixas de rede e IP para AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) para obter informações adicionais.

Para obter mais informações, consulte [os limites de assinatura e serviço do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Para verificar suas cotas atuais, no portal Azure, acesse a [lâmina de assinaturas,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)selecione sua assinatura e selecione **Use + quotas**.

## <a name="high-availability-and-business-continuity"></a>Alta disponibilidade e continuidade de negócios

Se o aplicativo não conseguir lidar com o tempo de inatividade, você precisará seguir as práticas recomendadas para cenários de migração de alta disponibilidade.  As práticas recomendadas para planejamento complexo de continuidade de negócios, recuperação de desastres e maximização do tempo de atividade estão além do escopo deste documento.  Leia mais sobre [as melhores práticas para continuidade de negócios e recuperação de desastres no Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) para saber mais.

Para aplicativos complexos, geralmente a migração ocorre ao longo do tempo em vez de uma só vez. Isso significa que os ambientes antigos e novos podem precisar se comunicar pela rede. Os aplicativos que `ClusterIP` anteriormente usavam serviços para se `LoadBalancer` comunicar podem precisar ser expostos como tipo e serem protegidos adequadamente.

Para concluir a migração, você vai querer apontar os clientes para os novos serviços que estão sendo executados no AKS. Recomendamos que você redirecione o tráfego atualizando o DNS para apontar para o Balanceador de Carga que fica na frente do seu cluster AKS.

[O Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) pode direcionar os clientes para o cluster e instância de aplicativo do Kubernetes desejado.  O Traffic Manager é um balanceador de carga de tráfego baseado em DNS que pode distribuir o tráfego de rede entre as regiões.  Para obter o melhor desempenho e redundância, direcione todo o tráfego de aplicativos através do Traffic Manager antes que ele vá para o cluster AKS.  Em uma implantação multicluster, os clientes devem se conectar a um nome DNS do Gerenciador de Tráfego que aponta para os serviços em cada cluster AKS. Defina esses serviços usando os pontos finais do Traffic Manager. Cada ponto final é o *IP do balanceador de carga de serviço*. Use essa configuração para direcionar o tráfego de rede do ponto final do Gerenciador de Tráfego em uma região até o ponto final em uma região diferente.

![AKS com gerenciador de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[O Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) é outra opção para roteamento de tráfego para clusters AKS.  O Azure Front Door Service permite que você defina, gerencie e monitore o roteamento global para seu tráfego da Web otimizando para melhor desempenho e failover global instantâneo para ter alta disponibilidade. 

### <a name="considerations-for-stateless-applications"></a>Considerações para aplicações apátridas

A migração de aplicativos sem monitoração de estado é o caso mais simples. Aplique suas definições de recursos (YAML ou Helm) ao novo cluster, certifique-se de que tudo funcione como esperado e redirecione o tráfego para ativar seu novo cluster.

### <a name="considerations-for-stateful-applications"></a>Considerações para aplicações estaduais

Planeje cuidadosamente a migração de aplicativos estaduais para evitar perda de dados ou tempo de inatividade inesperado.

Se você usar arquivos Azure, você pode montar o compartilhamento de arquivos como um volume no novo cluster:
* [Montagem de arquivos Azure estáticos como um volume](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Se você usar discos gerenciados do Azure, você só poderá montar o disco se não estiver conectado a qualquer VM:
* [Montagem do disco Azure estático como um volume](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Se nenhuma dessas abordagens funcionar, você pode usar opções de backup e restauração:
* [Velero no Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Arquivos do Azure

Ao contrário dos discos, os Arquivos do Azure podem ser montados em vários hosts simultaneamente. No seu cluster AKS, o Azure e o Kubernetes não impedem que você crie um pod que seu cluster ACS ainda usa. Para evitar perda de dados e comportamento inesperado, certifique-se de que os clusters não escrevam para os mesmos arquivos ao mesmo tempo.

Se o aplicativo puder hospedar várias réplicas que apontam para o mesmo compartilhamento de arquivos, siga as etapas de migração apátridas e implante suas definições de YAML para o novo cluster. Caso contrário, uma abordagem de migração possível envolve as seguintes etapas:

* Validar que seu aplicativo está funcionando corretamente.
* Aponte seu tráfego ao vivo para o seu novo cluster AKS.
* Desconecte o antigo cluster.

Se você quiser começar com um compartilhamento vazio e fazer uma [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) cópia dos dados de origem, você pode usar os comandos para migrar seus dados.


#### <a name="migrating-persistent-volumes"></a>Volumes persistentes migratórios

Se você estiver migrando volumes persistentes existentes para AKS, você geralmente seguirá estas etapas:

* Quiesce escreve para o aplicativo. (Esta etapa é opcional e requer tempo de inatividade.)
* Tire fotos dos discos.
* Crie novos discos gerenciados a partir dos snapshots.
* Crie volumes persistentes em AKS.
* Atualize as especificações do pod para [usar os volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez do PersistentVolumeClaims (provisionamento estático).
* Implante seu aplicativo para AKS.
* Validar que seu aplicativo está funcionando corretamente.
* Aponte seu tráfego ao vivo para o seu novo cluster AKS.

> [!IMPORTANT]
> Se você optar por não quiesce gravações, você precisará replicar dados para a nova implantação. Caso contrário, você perderá os dados que foram gravados depois que você tirou os instantâneos do disco.

Algumas ferramentas de código aberto podem ajudá-lo a criar discos gerenciados e migrar volumes entre clusters Kubernetes:

* [A extensão do Azure CLI Disk Copy](https://github.com/noelbundick/azure-cli-disk-copy-extension) copia e converte discos entre grupos de recursos e regiões do Azure.
* [A extensão CLI do Azure Kube](https://github.com/yaron2/azure-kube-cli) enumera os volumes do ACS Kubernetes e os migra para um cluster AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Implantação da configuração de cluster

Recomendamos que você use o pipeline de Integração Contínua (CI) e Entrega Contínua (CD) existente para implantar uma configuração de boa forma para a AKS. Você pode usar o Azure Pipelines para [construir e implantar seus aplicativos no AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Clone suas tarefas de `kubeconfig` implantação existentes e garanta que isso aponte para o novo cluster AKS.

Se isso não for possível, exporte definições de recursos do cluster Kubernetes existente e, em seguida, aplique-as em AKS. É possível usar `kubectl` para exportar objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Movendo recursos existentes para outra região

Você pode querer mover seu cluster AKS para uma [região diferente suportada pela AKS][region-availability]. Recomendamos que você crie um novo cluster na outra região e implante seus recursos e aplicativos para o seu novo cluster. Além disso, se você tiver algum serviço como [OZure Dev Spaces][azure-dev-spaces] em execução no seu cluster AKS, você também precisará instalar e configurar esses serviços em seu cluster na nova região.


Neste artigo, resumimos os detalhes da migração para:

> [!div class="checklist"]
> * AKS com balanceador de carga padrão e conjuntos de escala de máquina virtual
> * Serviços Azure anexados existentes
> * Garantir cotas válidas
> * Alta disponibilidade e continuidade de negócios
> * Considerações para aplicações apátridas
> * Considerações para aplicações estaduais
> * Implantação da configuração de cluster


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/