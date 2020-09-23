---
title: Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)
description: Encontre respostas para algumas das perguntas mais comuns sobre o AKS (Serviço de Kubernetes do Azure).
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 4150f850263aed7b8aa4317028386dc285f06ade
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905337"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)

Este artigo aborda as perguntas frequentes sobre o AKS (Serviço de Kubernetes do Azure).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais regiões do Azure atualmente fornecem o AKS?

Para obter uma lista completa das regiões disponíveis, confira [Regiões e disponibilidade do AKS][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Posso distribuir um cluster do AKS entre regiões?

Não. Os clusters do AKS são recursos regionais e não podem abranger regiões. Confira [Melhores práticas de continuidade dos negócios e recuperação de desastres][bcdr-bestpractices] para obter diretrizes sobre como criar uma arquitetura que inclui várias regiões.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Posso distribuir um cluster do AKS entre zonas de disponibilidade?

Sim. Você pode implantar um cluster do AKS em uma ou mais [zonas de disponibilidade][availability-zones] nas [regiões que dão suporte a elas][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor de API do Kubernetes?

Sim. Há duas opções para limitar o acesso ao servidor de API:

- Usar [intervalos de IP autorizados do servidor de API][api-server-authorized-ip-ranges] se você quiser manter um ponto de extremidade público para o servidor de API, mas quiser restringir o acesso a um conjunto de intervalos de IP confiáveis.
- Usar [um cluster particular][private-clusters] se quiser limitar o servidor de API para ser acessível *somente* na sua rede virtual.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Posso ter diferentes tamanhos de VM em um só cluster?

Sim, você pode usar diferentes tamanhos de máquina virtual no seu cluster do AKS criando [vários pools de nós][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS?

O Azure aplica automaticamente os patches de segurança aos nós do Linux no cluster de acordo com uma agenda noturna. No entanto, você é responsável por verificar se esses nós do Linux são reiniciados conforme necessário. Você tem várias opções para reinicializar os nós:

- Manualmente, por meio do portal do Azure ou da CLI do Azure.
- Atualizando o cluster AKS. O cluster atualiza automaticamente [nós de cordon e frenagem][cordon-drain] e coloca um novo nó online com a última imagem do Ubuntu e uma nova versão de patch ou uma versão secundária do Kubernetes. Para obter mais informações, confira [Atualizar um cluster do AKS][aks-upgrade].
- Usando o [Kured](https://github.com/weaveworks/kured), um daemon de reinicialização de software livre para o Kubernetes. O Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora cada nó para verificar se há um arquivo que indica que uma reinicialização é necessária. No cluster todo, as reinicializações do sistema operacional são gerenciadas pelo mesmo [processo de cordon e drenagem][cordon-drain] como uma atualização do cluster.

Para obter mais informações sobre como usar o Kured, confira [Aplicar atualizações de segurança e de kernel aos nós do AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Nós do Windows Server

Para os nós do Windows Server, o Windows Update não executa automaticamente e aplica as atualizações mais recentes. Regularmente, de acordo com o ciclo de lançamento do Windows Update e o próprio processo de validação, você deverá executar uma atualização no cluster e nos pools de nó do Windows Server no cluster do AKS. Esse processo de atualização cria nós que executam a imagem e os patches mais recentes do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre esse processo, confira [Atualizar um pool de nós no AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com o AKS?

O AKS baseia-se em vários recursos da infraestrutura do Azure, incluindo conjuntos de dimensionamento de máquinas virtuais, redes virtuais e discos gerenciados. Isso permite que você aproveite muitas das funcionalidades básicas da plataforma Azure no ambiente do Kubernetes gerenciado fornecido pelo AKS. Por exemplo, a maioria dos tipos de máquinas virtuais do Azure pode ser usada diretamente com o AKS e as Reservas do Azure podem ser usadas para receber descontos nesses recursos automaticamente.

Para habilitar essa arquitetura, cada implantação do AKS abrange dois grupos de recursos:

1. Você cria o primeiro grupo de recursos. Esse grupo contém apenas o recurso do Serviço de Kubernetes. O provedor de recursos do AKS cria automaticamente o segundo grupo de recursos durante a implantação. Um exemplo do segundo grupo de recursos é *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome desse segundo grupo de recursos, confira a próxima seção.
1. O segundo grupo de recursos, conhecido como o *grupo de recursos do nó*, contém todos os recursos de infraestrutura associados ao cluster. Esses recursos incluem as máquinas virtuais do nó do Kubernetes, rede virtual e armazenamento. Por padrão, o grupo de recursos do nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. O AKS exclui automaticamente o recurso do nó sempre que o cluster é excluído e, portanto, ele só deve ser usado para os recursos que compartilham o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso fornecer um nome personalizado para o grupo de recursos do nó no AKS?

Sim. Por padrão, o AKS nomeará o grupo de recursos do nó *MC_resourcegroupname_clustername_location*, mas você também poderá fornecer um nome personalizado.

Para especificar um nome do grupo de recursos, instale a extensão da CLI do Azure [aks-preview][aks-preview-cli] versão *0.3.2* ou posterior. Ao criar um cluster do AKS por meio do comando [az aks create][az-aks-create], use o parâmetro *--node-resource-group* e especifique um nome para o grupo de recursos. Se você [usar um modelo do Azure Resource Manager][aks-rm-template] para implantar um cluster do AKS, defina o nome do grupo de recursos usando a propriedade *nodeResourceGroup*.

* O grupo de recursos secundário é criado automaticamente pelo provedor de recursos do Azure na própria assinatura.
* Você poderá especificar um nome de grupo de recursos personalizado somente quando estiver criando o cluster.

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não é possível:

* Especificar um grupo de recursos existente para o grupo de recursos do nó.
* Especificar uma assinatura diferente para o grupo de recursos do nó.
* Alterar o nome do grupo de recursos do nó depois que o cluster for criado.
* Especificar nomes para os recursos gerenciados dentro do grupo de recursos do nó.
* Modificar ou excluir marcas criadas pelo Azure de recursos gerenciados dentro do grupo de recursos do nó. (Confira as informações adicionais na próxima seção.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar marcas e outras propriedades dos recursos do AKS no grupo de recursos do nó?

Se você modificar ou excluir as marcas criadas pelo Azure e outras propriedades de recursos no grupo de recursos do nó, poderá obter resultados inesperados, como erros de dimensionamento e atualização. O AKS permite criar e modificar marcas personalizadas criadas por usuários finais, e você pode adicionar essas marcas ao [criar um pool de nós](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). O ideal é criar ou modificar marcas personalizadas, por exemplo, para atribuir uma unidade de negócios ou um centro de custo. Isso também pode ser obtido com a criação de políticas do Azure com um escopo no grupo de recursos gerenciado.

No entanto, a modificação de **marcas criadas pelo Azure** em recursos no grupo de recursos de nó no cluster AKs é uma ação sem suporte que interrompe o Slo (objetivo de nível de serviço). Para obter mais informações, confira [O AKS oferece um SLA?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quais os controles de admissão de Kubernetes que o AKS suporta? Controladores de admissão podem ser adicionados ou removidos?

O AKS dá suporte aos seguintes [controladores de admissão][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

No momento, não é possível modificar a lista de controladores de admissão no AKS.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Posso usar webhooks do controlador de admissão no AKS?

Sim, você pode usar webhooks do controlador de admissão no AKS. Recomendamos que você exclua os namespaces internos do AKS que estão marcados com o **rótulo do plano de controle.** Por exemplo, adicionando o código abaixo à configuração do webhook:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Os webhooks do controlador de admissão podem afetar os namespaces internos do AKS e do kube-system?

Para proteger a estabilidade do sistema e impedir que os controladores de admissão personalizados afetem os serviços internos no namespace do kube-system, o AKS tem uma **Imposição de Admissões**, que exclui automaticamente os namespaces internos do kube-system e do AKS. Esse serviço verifica se os controladores de admissão personalizados não afetam os serviços em execução no kube-system.

Se você tiver um caso de uso crítico para ter algo implantado no kube-system (não recomendado), que precisa ser coberto pelo webhook de admissão personalizado, adicione o rótulo ou a anotação abaixo para que a Imposição de Admissões o ignore.

Rótulo: ```"admissions.enforcer/disabled": "true"``` ou Anotação: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault é integrado com o AKS?

Atualmente, o AKS não está integrado nativamente ao Azure Key Vault. No entanto, o [provedor do Azure Key Vault para o repositório de segredos do CSI][csi-driver] permite a integração direta dos pods do Kubernetes aos segredos do Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server no AKS?

Sim, os contêineres do Windows Server estão disponíveis no AKS. Para executar os contêineres do Windows Server no AKS, crie um pool de nós que executa o Windows Server como o SO convidado. Os contêineres do Windows Server podem usar apenas o Windows Server 2019. Para começar a usá-lo, confira [Criar um cluster do AKS com um pool de nós do Windows Server][aks-windows-cli].

O suporte do Windows Server para pools de nós inclui algumas limitações que fazem parte do Windows Server upstream no projeto do Kubernetes. Para obter mais informações sobre essas limitações, confira [Limitações de contêineres do Windows Server no AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um SLA?

O AKS fornece garantias de SLA como um recurso complementar opcional com um [SLA de tempo de atividade][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar descontos de reserva do Azure aos meus nós de agente do AKS?

Os nós de agente do AKS são cobrados como máquinas virtuais padrão do Azure; portanto, se você comprou [reservas do Azure][reservation-discounts] para o tamanho de VM que está usando no AKS, esses descontos são aplicados automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Posso mover/migrar meu cluster entre locatários do Azure?

No momento, não há suporte para a movimentação do cluster AKS entre locatários.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Posso mover/migrar meu cluster entre assinaturas?

No momento, não há suporte para a movimentação de clusters entre assinaturas.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Posso mover meus clusters do AKS da assinatura atual do Azure para outra? 

Não há suporte para a movimentação do cluster AKS e seus recursos associados entre assinaturas do Azure.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Posso mover meu cluster AKS ou recursos de infraestrutura AKS para outros grupos de recursos ou renomeá-los?

Não há suporte para mover ou renomear o cluster AKS e seus recursos associados.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Por que a exclusão do meu cluster demora tanto? 

A maioria dos clusters é excluída mediante solicitação do usuário; em alguns casos, especialmente quando os clientes trazem o próprio grupo de recursos ou fazem a exclusão de tarefas entre grupos de recursos, isso pode levar mais tempo ou falhar. Se você tiver um problema com as exclusões, verifique se não tem bloqueios no grupo de recursos, se todos os recursos fora do grupo de recursos estão desassociados dele etc.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se eu tiver um pod/implantações no estado 'NodeLost' ou 'Unknown', ainda poderei atualizar o meu cluster?

Você pode, mas o AKS não recomenda fazer isso. O ideal é que as atualizações sejam executadas quando o estado do cluster é conhecido e íntegro.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se eu tiver um cluster com um ou mais nós em um estado não íntegro ou desligado, poderei executar uma atualização?

Não, exclua/remova todos os nós que estão em um estado com falha ou que, de outro modo, foram removidos do cluster antes da atualização.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Executei uma exclusão de cluster, mas o erro `[Errno 11001] getaddrinfo failed` é exibido 

Normalmente, isso é causado pelo fato de os usuários terem um ou mais NSGs (grupos de segurança de rede) ainda em uso e associados ao cluster.  Remova-os e tente excluir o cluster novamente.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Executei uma atualização, mas agora meus pods estão em loops de falha e as investigações de preparação falham.

Confirme se a entidade de serviço não expirou.  Confira: [Entidade de serviço do AKS](./kubernetes-service-principal.md) e [Credenciais de atualização do AKS](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>Meu cluster estava funcionando, mas, de repente, não pode provisionar balanceadores de carga, montar PVCs etc. 

Confirme se a entidade de serviço não expirou.  Confira: [Entidade de serviço do AKS](./kubernetes-service-principal.md) e [Credenciais de atualização do AKS](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Posso dimensionar meu cluster AKS para zero?
Você pode [interromper completamente um cluster AKS em execução](start-stop-cluster.md), economizando nos respectivos custos de computação. Além disso, você também pode optar por [dimensionar ou dimensionar automaticamente todos ou `User` pools de nós específicos](scale-cluster.md#scale-user-node-pools-to-0) para 0, mantendo apenas a configuração de cluster necessária.
Não é possível dimensionar diretamente os [pools de nós do sistema](use-system-pools.md) para 0.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Posso usar as APIs do conjunto de dimensionamento de máquinas virtuais para executar uma operação de escala manual?

Não. Não há suporte para operações de escala por meio das APIs do conjunto de dimensionamento de máquinas virtuais. Use as APIs do AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Posso usar conjuntos de dimensionamento de máquinas virtuais para executar uma operação de escala manual para 0 nó?

Não. Não há suporte para operações de escala por meio das APIs do conjunto de dimensionamento de máquinas virtuais.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Posso interromper ou desalocar todas as minhas VMs?

Embora o AKS tenha mecanismos de resiliência para tolerar uma configuração como essa e se recuperar dela, essa não é uma configuração recomendada.

## <a name="can-i-use-custom-vm-extensions"></a>Posso usar extensões de VM personalizadas?

Não. O AKS é um serviço gerenciado e não há suporte para o processamento dos recursos de IaaS. Para instalar componentes personalizados etc., aproveite as APIs e os mecanismos do Kubernetes. Por exemplo, aproveite os DaemonSets para instalar os componentes necessários.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>O AKS armazena dados de clientes fora da região do cluster?

O recurso para habilitar o armazenamento de dados do cliente em uma única região está disponível no momento apenas na região do Sudeste Asiático (Cingapura) da área geográfica do Pacífico Asiático. Para todas as outras regiões, os dados do cliente são armazenados na Área geográfica.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
