---
title: Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)
description: Encontre respostas para algumas das perguntas comuns sobre o Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497758"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)

Este artigo aborda as perguntas frequentes sobre o AKS (Serviço de Kubernetes do Azure).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais regiões do Azure fornecem atualmente AKS?

Para obter uma lista completa das regiões disponíveis, consulte [regiões e disponibilidade da AKS][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>O AKS é compatível com o dimensionamento automático de nó?

Sim, a capacidade de dimensionar automaticamente os nós do agente horizontalmente no AKS está atualmente disponível na visualização. Consulte [Dimensionar automaticamente um cluster para atender às demandas de aplicativos no AKS][aks-cluster-autoscaler] para obter instruções. O autoscaling AKS é baseado no [autoscaler Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Posso implantar AKS em minha rede virtual existente?

Sim, você pode implantar um cluster AKS em uma rede virtual existente usando o [recurso avançado de rede][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Posso limitar quem tem acesso ao servidor aPI do Kubernetes?

Sim, você pode limitar o acesso ao servidor de API do Kubernetes usando [faixas ip autorizadas do Servidor API][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Posso tornar o servidor API do Kubernetes acessível apenas na minha rede virtual?

Não neste momento, mas isso está planejado. Você pode acompanhar o progresso no [repo AKS GitHub][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Posso ter diferentes tamanhos de VM em um único cluster?

Sim, você pode usar diferentes tamanhos de máquina virtual em seu cluster AKS criando [vários pools de nó][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS?

O Azure aplica automaticamente patches de segurança aos nós Linux em seu cluster em um horário noturno. No entanto, você é responsável por garantir que esses nós Linux sejam reiniciados conforme necessário. Você tem várias opções para reinicializar os nós:

- Manualmente, por meio do portal do Azure ou da CLI do Azure.
- Atualizando o cluster AKS. O cluster atualiza [os nós de cordão e drenagem][cordon-drain] automaticamente e, em seguida, traz um novo nó on-line com a imagem mais recente do Ubuntu e uma nova versão de patch ou uma versão menor do Kubernetes. Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade].
- Usando [Kured,](https://github.com/weaveworks/kured)um daemon de reinicialização de código aberto para Kubernetes. Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. Através do cluster, as reinicializações do sistema operacional são gerenciadas pelo mesmo [processo de isolamento e drenagem][cordon-drain] de um upgrade de cluster.

Para obter mais informações sobre como usar o kured, consulte [Aplicar atualizações de segurança e o kernel para nós do AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Os ádenos do Windows Server

Para os números do Windows Server (atualmente em pré-visualização no AKS), o Windows Update não é executado automaticamente e aplica as últimas atualizações. Em um cronograma regular em torno do ciclo de lançamento do Windows Update e do seu próprio processo de validação, você deve executar uma atualização no cluster e no pool de nó do Windows Server no cluster AKS. Este processo de atualização cria números que executam a imagem e patches mais recentes do Windows Server e, em seguida, remove os álos mais antigos. Para obter mais informações sobre este processo, consulte [Atualizar um pool de nós no AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com o AKS?

O AKS se baseia em vários recursos de infra-estrutura do Azure, incluindo conjuntos de escala de máquinas virtuais, redes virtuais e discos gerenciados. Isso permite que você aproveite muitos dos recursos principais da plataforma Azure dentro do ambiente Kubernetes gerenciado fornecido pela AKS. Por exemplo, a maioria dos tipos de máquinas virtuais do Azure pode ser usada diretamente com AKS e o Azure Reservations pode ser usado para receber descontos nesses recursos automaticamente.

Para habilitar essa arquitetura, cada implantação do AKS abrange dois grupos de recursos:

1. Você cria o primeiro grupo de recursos. Este grupo contém apenas o recurso de serviço Kubernetes. O provedor de recursos AKS cria automaticamente o segundo grupo de recursos durante a implantação. Um exemplo do segundo grupo de recursos é *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome deste segundo grupo de recursos, consulte a próxima seção.
1. O segundo grupo de recursos, conhecido como grupo de recursos de *nó,* contém todos os recursos de infra-estrutura associados ao cluster. Esses recursos incluem as máquinas virtuais do nó do Kubernetes, rede virtual e armazenamento. Por padrão, o grupo de recursos do nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. O AKS exclui automaticamente o recurso do nó sempre que o cluster é excluído, portanto, ele só deve ser usado para recursos que compartilham o ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso fornecer meu próprio nome para o grupo de recursos do nó AKS?

Sim. Por padrão, a AKS nomeará o grupo de recursos *do nó MC_resourcegroupname_clustername_location,* mas você também pode fornecer seu próprio nome.

Para especificar o nome do seu próprio grupo de recursos, instale a versão [aks-preview][aks-preview-cli] Azure CLI versão *0.3.2* ou posterior. Quando você cria um cluster AKS usando o comando [az aks create,][az-aks-create] use o parâmetro *--nó-recurso-grupo-grupo* e especifique um nome para o grupo de recursos. Se você [usar um modelo do Azure Resource Manager][aks-rm-template] para implantar um cluster AKS, você poderá definir o nome do grupo de recursos usando a propriedade *nodeResourceGroup.*

* O grupo de recursos secundários é criado automaticamente pelo provedor de recursos Do Azure em sua própria assinatura.
* Você só pode especificar um nome de grupo de recursos personalizado quando estiver criando o cluster.

Ao trabalhar com o grupo de recursos do nó, tenha em mente que não pode:

* Especifique um grupo de recursos existente para o grupo de recursos do nó.
* Especifique uma assinatura diferente para o grupo de recursos do nó.
* Alterar o nome do grupo de recursos do nó após a criação do cluster.
* Especifique nomes para os recursos gerenciados no grupo de recursos do nó.
* Modificar ou excluir tags de recursos gerenciados dentro do grupo de recursos do nó. (Veja informações adicionais na próxima seção.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar tags e outras propriedades dos recursos AKS no grupo de recursos do nó?

Se você modificar ou excluir tags criadas pelo Azure e outras propriedades de recurso no grupo de recursos do nó, você poderá obter resultados inesperados, como erros de dimensionamento e atualização. O AKS permite criar e modificar tags personalizadas. Você pode querer criar ou modificar tags personalizadas, por exemplo, para atribuir uma unidade de negócios ou centro de custos. Ao modificar os recursos o grupo de recursos de nó no cluster AKS, você quebra o objetivo de nível de serviço (SLO). Para obter mais informações, [consulte AKS oferece um contrato de nível de serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quais os controles de admissão de Kubernetes que o AKS suporta? Controladores de admissão podem ser adicionados ou removidos?

AKS suporta os seguintes [controles de admissão][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *Serviceaccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

Atualmente, você não pode modificar a lista de controladores de admissão no AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault é integrado com o AKS?

AKS não está atualmente nativamente integrado com o Azure Key Vault. No entanto, o [projeto Azure Key Vault FlexVolume for Kubernetes][keyvault-flexvolume] permite a integração direta dos pods Kubernetes aos segredos do Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server no AKS?

Sim, os contêineres do Windows Server estão disponíveis na pré-visualização. Para executar os contêineres do Windows Server no AKS, você cria um pool de nós que executa o Windows Server como sistema operacional convidado. Os contêineres do Windows Server podem usar apenas o Windows Server 2019. Para começar, consulte [Criar um cluster AKS com um pool de nó do Windows Server][aks-windows-cli].

O suporte ao Windows Server para pool de nós inclui algumas limitações que fazem parte do projeto Upstream Windows Server in Kubernetes. Para obter mais informações sobre essas limitações, consulte [os contêineres do Windows Server em limitações aKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>A AKS oferece um contrato de nível de serviço?

Em um contrato de nível de serviço (SLA), o provedor concorda em reembolsar o cliente pelo custo do serviço se o nível de serviço publicado não for cumprido. Como o AKS é gratuito, não há custo disponível para reembolso, portanto, a AKS não tem SLA formal. No entanto, a AKS busca manter a disponibilidade de pelo menos 99,5% para o servidor API kubernetes.

É importante reconhecer a distinção entre a disponibilidade de serviço aks que se refere ao tempo de atividade do plano de controle Kubernetes e a disponibilidade de sua carga de trabalho específica que está sendo executado em Máquinas Virtuais Azure. Embora o plano de controle possa estar indisponível se o plano de controle não estiver pronto, suas cargas de trabalho de cluster em execução em VMs Azure ainda podem funcionar. Dado que as VMs do Azure são recursos pagos, eles são apoiados por um SLA financeiro. Leia [aqui para mais detalhes](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) sobre o Azure VM SLA e como aumentar essa disponibilidade com recursos como Zonas de [Disponibilidade][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Por que não posso definir maxPods abaixo de 30?

No AKS, você `maxPods` pode definir o valor ao criar o cluster usando os modelos Azure CLI e Azure Resource Manager. No entanto, tanto a Kubenet quanto a Azure CNI exigem um *valor mínimo* (validado na hora da criação):

| Rede | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Como o AKS é um serviço gerenciado, implantamos e gerenciamos complementos e pods como parte do cluster. No passado, os usuários `maxPods` podiam definir um valor menor do que o valor que os pods gerenciados precisavam executar (por exemplo, 30). O AKS agora calcula o número mínimo de pods usando esta fórmula: (maxPods ou (maxPods * vm_count)) > pods adicionais gerenciados no mínimo.

Os usuários não podem `maxPods` substituir a validação mínima.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Posso aplicar descontos de reserva do Azure aos meus nós de agente AKS?

Os nós de agente AKS são cobrados como máquinas virtuais padrão do Azure, portanto, se você comprou [reservas do Azure][reservation-discounts] para o tamanho de VM que você está usando no AKS, esses descontos são aplicados automaticamente.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Posso mover/migrar meu cluster entre os inquilinos do Azure?

O `az aks update-credentials` comando pode ser usado para mover um cluster AKS entre os inquilinos do Azure. Siga as instruções em [Escolha atualizar ou criar um principal de serviço](https://docs.microsoft.com/azure/aks/update-credentials) e, em seguida, [atualize o cluster aks com novas credenciais](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Posso mover/migrar meu cluster entre assinaturas?

O movimento de clusters entre assinaturas não tem suporte.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Posso mover meus clusters AKS da assinatura atual do azure para outra? 

A movimentação do cluster AKS e seus recursos associados entre as assinaturas do Azure não são suportados.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Por que meu cluster está demorando tanto? 

A maioria dos clusters são excluídos mediante solicitação do usuário; em alguns casos, especialmente quando os clientes estão trazendo seu próprio Grupo de Recursos, ou fazer a exclusão de tarefas cross-RG pode levar tempo adicional ou falhar. Se você tiver um problema com exclusões, verifique duas vezes se você não tem bloqueios no RG, se quaisquer recursos fora do RG estão desassociados do RG, etc.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Se eu tiver pod / implantações no estado 'NodeLost' ou 'Desconhecido' ainda posso atualizar meu cluster?

Você pode, mas a AKS não recomenda isso. As atualizações devem ser realizadas idealmente quando o estado do cluster é conhecido e saudável.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Se eu tiver um cluster com um ou mais nós em um estado insalubre ou desligado, posso realizar um upgrade?

Não, por favor, exclua/remova quaisquer nomes em um estado com falha ou removidos do cluster antes de atualizar.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Eu corri uma exclusão de cluster, mas veja o erro`[Errno 11001] getaddrinfo failed` 

Mais comumente, isso é causado por usuários que têm um ou mais NSGs (Network Security Groups) ainda em uso e associados ao cluster.  Por favor, remova-os e tente excluir novamente.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Fiz um upgrade, mas agora meus pods estão em loops de colisão, e as sondas de prontidão falham?

Por favor, confirme que seu diretor de serviço não expirou.  Veja: [As credenciais de atualização do serviço AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [AKS .](https://docs.microsoft.com/azure/aks/update-credentials)

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Meu cluster estava funcionando, mas de repente não pode provisionar LoadBalancers, mount PVCs, etc.? 

Por favor, confirme que seu diretor de serviço não expirou.  Veja: [As credenciais de atualização do serviço AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) e [AKS .](https://docs.microsoft.com/azure/aks/update-credentials)

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Posso usar as APIs de conjunto de escala de máquina virtual para dimensionar manualmente?

Não, as operações de escala usando as APIs de conjunto de escala de máquina virtual não são suportadas. Use as APIs AKS ().`az aks scale`

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Posso usar conjuntos de escala de máquinavirtual para escalar manualmente para 0 nós?

Não, as operações de escala usando as APIs de conjunto de escala de máquina virtual não são suportadas.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Posso parar ou desalocar todas as minhas VMs?

Embora o AKS tenha mecanismos de resiliência para resistir a tal configuração e recuperar-se dela, esta não é uma configuração recomendada.

## <a name="can-i-use-custom-vm-extensions"></a>Posso usar extensões VM personalizadas?

Nenhum AKS é um serviço gerenciado, e a manipulação dos recursos do IaaS não é suportada. Para instalar componentes personalizados, etc. por favor, aproveite as APIs e mecanismos kubernetes. Por exemplo, aproveite Os Conjuntos de Daemonparas para instalar os componentes necessários.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
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

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
