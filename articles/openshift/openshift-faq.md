---
title: Perguntas frequentes para Azure Red Hat OpenShift
description: Aqui estão respostas para perguntas comuns sobre o Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619495"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift FAQ

Este artigo aborda perguntas frequentes (PERGUNTAS FREQUENTES) sobre o Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Quais regiões do Azure são suportadas?

Consulte [os recursos suportados](supported-resources.md#azure-regions) para uma lista de regiões globais onde o Azure Red Hat OpenShift é suportado.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Posso implantar um cluster em uma rede virtual existente?

Não. Mas você pode conectar um cluster Azure Red Hat OpenShift a um VNET existente através do peering. Consulte [Conecte a rede virtual de um cluster a uma rede virtual existente](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) para obter detalhes.

## <a name="what-cluster-operations-are-available"></a>Que operações de cluster estão disponíveis?

Você só pode aumentar ou diminuir o número de nódulos computacionais. Não são permitidas outras `Microsoft.ContainerService/openShiftManagedClusters` modificações no recurso após a criação. O número máximo de nódulos computacionais é limitado a 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Que tamanhos de máquina virtual posso usar?

Consulte os tamanhos das [máquinas virtuais Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) para obter uma lista de tamanhos de máquinas virtuais que você pode usar com um cluster Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Os dados do meu cluster estão criptografados?

Por padrão, há criptografia em repouso. A plataforma azure Storage criptografa automaticamente seus dados antes de perminá-los e descriptografa os dados antes da recuperação. Consulte [a criptografia do serviço de armazenamento Azure para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) para obter detalhes.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Posso usar o Prometheus/Grafana para monitorar minhas aplicações?

Sim, você pode implantar o Prometheus em seu namespace e monitorar aplicativos em seu namespace.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Posso usar O Prometeu/Grafana para monitorar métricas relacionadas à saúde e capacidade do cluster?

Não, não no momento.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>O registro docker está disponível externamente para que eu possa usar ferramentas como Jenkins?

No entanto, `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` o registro Docker está disponível no entanto, uma forte garantia de durabilidade do armazenamento não é fornecida. Você também pode usar [o Registro de Contêineres do Azure](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>A rede de cross-namespace é suportada?

Os admins de projeto individuais e de clientes podem personalizar a rede `NetworkPolicy` de espaço entre nomes (inclusive negando-a) em uma base por projeto usando objetos.

## <a name="can-an-admin-manage-users-and-quotas"></a>Um admin pode gerenciar usuários e cotas?

Sim. Um administrador do Azure Red Hat OpenShift pode gerenciar usuários e cotas, além de acessar todos os projetos criados pelo usuário.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Posso restringir um cluster a apenas certos usuários do Azure AD?

Sim. Você pode restringir quais usuários do Azure AD podem fazer login em um cluster configurando o aplicativo Azure AD. Para obter detalhes, [consulte Como restringir seu aplicativo a um conjunto de usuários](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Posso restringir os usuários de criar projetos?

Sim. Faça login no cluster como administrador do Azure Red Hat OpenShift e execute este comando:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Para obter mais informações, consulte a documentação openShift sobre [a desativação do autoprovisionamento](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Um cluster pode ter nós computacionais em várias regiões do Azure?

Não. Todos os nós em um cluster Azure Red Hat OpenShift devem ser originários da mesma região do Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Os nós de mestre e infra-estrutura são abstratos como estão com o Azure Kubernetes Service (AKS)?

Não. Todos os recursos, incluindo o mestre do cluster, são executados na assinatura do cliente. Esses tipos de recursos são colocados em um grupo de recursos somente leitura.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>O Open Service Broker for Azure (OSBA) é suportado?

Sim. Você pode usar os OSBA com OZure Red Hat OpenShift. Consulte [Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) para obter mais informações.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Estou tentando espiar uma rede virtual em `Failed to get vnet CIDR` uma assinatura diferente, mas recebendo erros.

Na assinatura que possui a rede virtual, certifique-se de registrar `Microsoft.ContainerService` provedor com`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>O que é o processo de manutenção do Azure Red Hat OpenShift (ARO) ?

Existem três tipos de manutenção para ARO: upgrades, backup e restauração de dados etcd e manutenção iniciada por provedores de nuvem.

+ As atualizações incluem upgrades de software e CVEs. A remediação da CVE ocorre na inicialização por meio da execução `yum update` e prevê uma mitigação imediata.  Paralelamente, uma nova compilação de imagem será criada para futuras criações de clusters.

+ Backup e gerenciamento de dados etcd é um processo automatizado que pode exigir tempo de inatividade do cluster, dependendo da ação. Se o banco de dados etcd estiver sendo restaurado a partir de um backup, haverá tempo de inatividade. Fazemos backup etcd de hora em hora e retemos as últimas 6 horas de backups.

+ A manutenção iniciada pelo provedor de nuvem inclui paralisações de rede, armazenamento e regionais. A manutenção depende do provedor de nuvem e depende de atualizações fornecidas pelo provedor.

## <a name="what-is-the-general-upgrade-process"></a>Qual é o processo de atualização geral?

Executar um upgrade deve ser um processo seguro para ser executado e não deve interromper os serviços de cluster. O SRE pode acionar o processo de atualização quando novas versões estiverem disponíveis ou cvEs estiverem pendentes.

As atualizações disponíveis são testadas em um ambiente de estágio e depois aplicadas a clusters de produção. Quando aplicado, um novo nó é adicionado temporariamente e os nós são atualizados de forma rotativa para que os pods mantenham a contagem de réplicas. Seguir as melhores práticas ajuda a garantir o mínimo ou o tempo de inatividade.

Dependendo da gravidade da atualização ou atualização pendente, o processo pode diferir na forma de que as atualizações possam ser aplicadas rapidamente para mitigar a exposição do serviço a um CVE. Uma nova imagem será construída assíncronamente, testada e implantada como um upgrade de cluster. Fora isso, não há diferença entre emergência e manutenção planejada. A manutenção planejada não é pré-agendada com o cliente.

As notificações podem ser enviadas via ICM e e-mail se a comunicação ao cliente for necessária.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>E as janelas de manutenção de emergência versus planejadas?

Não distinguemos entre os dois tipos de manutenção. Nossas equipes estão disponíveis 24/7/365 e não usam janelas de manutenção programadas tradicionais "fora do horário".

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Como o sistema operacional host e o software OpenShift serão atualizados?

O sistema operacional host e o software OpenShift são atualizados através do nosso processo geral de atualização e construção de imagens.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Qual é o processo para reiniciar o nó atualizado?

Isso deve ser tratado como parte de um upgrade.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Os dados são armazenados em etcd criptografados no ARO?

Não é criptografado no nível etcd. A opção de atilá-lo não tem suporte no momento. O OpenShift suporta esse recurso, mas os esforços de engenharia são necessários para fazê-lo no roteiro. Os dados são criptografados no nível do disco. Consulte ['Criptografar dados na camada de dados'](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) para obter mais informações.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Os registros de VMs subjacentes podem ser transmitidos para um sistema de análise de registros de clientes?

Syslog, registros de docker, diário e dmesg são tratados pelo serviço gerenciado e não são expostos aos clientes.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Como um cliente pode ter acesso a métricas como CPU/memória no nível do nó para agir para dimensionar, depurar problemas, etc. Eu não posso `kubectl top` correr em um cluster ARO.

Os clientes podem acessar as métricas de CPU/Memória no nível do nó usando o comando `oc adm top nodes` ou `kubectl top nodes` com a função de cluster de administrador do cliente.  Os clientes também podem acessar `pods` as métricas de CPU/Memória com o comando `oc adm top pods` ou`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>O que é a configuração padrão do agendador de pod para ARO?

O ARO usa o agendador padrão que é enviado no OpenShift. Existem alguns mecanismos adicionais que não são suportados no ARO. Consulte a [documentação do agendador padrão](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) e [a documentação do agendador mestre](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) para obter mais detalhes.

O agendamento avançado/personalizado não tem suporte no momento. Consulte a [documentação de Agendamento](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) para obter mais detalhes.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Se aumentarmos a implantação, como os domínios de falha do Azure mapeiam a colocação de pods para garantir que todos os pods de um serviço não seja eliminadopor uma falha em um único domínio de falha?

Existem, por padrão, cinco domínios de falha ao usar conjuntos de escala de máquina virtual no Azure. Cada instância da máquina virtual em um conjunto de escalas será colocada em um desses domínios de falha. Isso garante que os aplicativos implantados nos nós de computação em um cluster serão colocados em domínios de falha separados.

Consulte [Escolher o número certo de domínios de falha para definir a escala da máquina virtual](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) para obter mais detalhes.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Existe uma maneira de gerenciar a colocação do pod?

Os clientes têm a capacidade de obter nós e visualizar rótulos como o anúncio do cliente.  Isso fornecerá uma maneira de direcionar qualquer VM no conjunto de escalas.

Deve-se ter cuidado ao usar etiquetas específicas:

- O nome do host não deve ser usado. O nome do host é girado frequentemente com atualizações e atualizações e é garantido que será trocado.

- Se o cliente tiver uma solicitação de rótulos específicos ou uma estratégia de implantação, isso poderia ser realizado, mas exigiria esforços de engenharia e não é suportado hoje.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Qual é o número máximo de pods em um cluster ARO?Qual é o número máximo de pods por nó em ARO?

 O Azure Red Hat OpenShift 3.11 tem um limite de 50 pods por nó, com [aro tendo um limite de nó de 20 cálculos,](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)de modo que limita o número máximo de pods suportados em um cluster ARO para 50 *20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Podemos especificar faixas de IP para implantação no VNET privado, evitando confrontos com outros VNETs corporativos uma vez que espiaram?

O Azure Red Hat OpenShift suporta peering VNET e permite que o cliente forneça um VNET para peer e um CIDR VNET no qual a rede OpenShift funcionará.

O VNET criado pela ARO será protegido e não aceitará alterações de configuração. O VNET que é peered é controlado pelo cliente e reside em sua assinatura.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>O cluster reside em uma assinatura de cliente? 

O Aplicativo Gerenciado do Azure vive em um grupo de recursos bloqueado com a assinatura do cliente. O cliente pode visualizar objetos nesse RG, mas não modificar.

## <a name="is-the-sdn-module-configurable"></a>O módulo SDN é configurável?

O SDN é uma política de rede openshift-ovs e não é configurável.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Quais direitos UNIX (em IaaS) estão disponíveis para Nós de Mestrado/Infra/App?

Não se aplica a esta oferta. O acesso ao nó é proibido.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Quais direitos OCP nós temos? Administrador de clusters? Admin do projeto?

Para obter detalhes, consulte a [visão geral](https://docs.openshift.com/aro/admin_guide/index.html)da administração do cluster Azure Red Hat OpenShift .

## <a name="which-kind-of-federation-with-ldap"></a>Que tipo de federação com o LDAP?

Isso seria alcançado através da integração azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Existe algum elemento na ARO compartilhado com outros clientes? Ou tudo é independente?

Cada cluster Azure Red Hat OpenShift é dedicado a um determinado cliente e vive dentro da assinatura do cliente. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Podemos escolher qualquer solução de armazenamento persistente, como OCS? 

Duas classes de armazenamento estão disponíveis para selecionar em: Azure Disk e Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Como um cluster é atualizado (incluindo majors e menores devido a vulnerabilidades)?

Veja [Qual é o processo de atualização geral?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Qual balanceador de carga azure é usado pela ARO?É padrão ou básico e é configurável?

O ARO usa o Standard Azure Load Balancer e não é configurável.

## <a name="can-aro-use-netapp-based-storage"></a>O ARO pode usar o armazenamento baseado no NetApp?

No momento, as únicas opções de armazenamento suportadas são as classes de armazenamento azure Disk e Azure File. 


