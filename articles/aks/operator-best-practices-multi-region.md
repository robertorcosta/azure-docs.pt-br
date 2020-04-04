---
title: Alta disponibilidade e recuperação de desastres no Azure Kubernetes Service (AKS)
description: Aprenda as melhores práticas de um operador de cluster para obter o máximo de tempo de atividade para seus aplicativos, fornecendo alta disponibilidade e preparando-se para a recuperação de desastres no Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 6aff60cbc4a4cab557e6e202ea1181d5b20bbd20
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655878"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para continuidade dos negócios e recuperação de desastres no Serviço de Kubernetes do Azure (AKS)

À medida que você vai gerenciando clusters no Serviço de Kubernetes do Azure (AKS), o tempo de atividade do aplicativo vai ficando mais importante. Por padrão, o AKS fornece alta disponibilidade usando vários nós em um [VMSS (Virtual Machine Scale Set, conjunto de escala de máquina virtual).](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) Mas esses nós múltiplos não protegem seu sistema de uma falha na região. Para maximizar seu tempo de atividade, planeje com antecedência para manter a continuidade dos negócios e prepare-se para a recuperação de desastres.

Este artigo foca em como planejar a continuidade de negócios e a recuperação de desastres em AKS. Você aprenderá como:

> [!div class="checklist"]
> * Planeje clusters AKS em várias regiões.
> * Direcionar o tráfego em vários clusters usando o Azure Traffic Manager.
> * Use geo-replicação para registros de imagem do contêiner.
> * Planeje o estado de aplicação em vários clusters.
> * Replique o armazenamento em várias regiões.

## <a name="plan-for-multiregion-deployment"></a>Plano de implantação multirregião

**Práticas recomendadas**: Quando você implantar vários clusters AKS, escolha regiões onde o AKS está disponível e use regiões emparelhadas.

Um cluster do AKS é implantado em uma única região. Para proteger seu sistema contra falhas na região, implante seu aplicativo em vários clusters AKS em diferentes regiões. Quando você planeja onde implantar seu cluster AKS, considere:

* [**Disponibilidade da região AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Escolha regiões próximas aos seus usuários. A AKS continuamente se expande para novas regiões.
* [**Regiões emparelhadas do Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Para sua área geográfica, escolha duas regiões que estejam emparelhadas entre si. As regiões emparelhadas coordenam as atualizações da plataforma e priorizam os esforços de recuperação quando necessário.
* **Disponibilidade do serviço**: Decida se suas regiões emparelhadas devem ser quentes/quentes, quentes/quentes ou quentes/frias. Você deseja rodar ambas as regiões ao mesmo tempo, com uma região *pronta* para começar a servir o tráfego? Ou você quer que uma região tenha tempo para se preparar para servir o tráfego?

A disponibilidade da região aks e regiões emparelhadas são uma consideração conjunta. Implante seus clusters do AKS em regiões emparelhadas que são projetadas para gerenciar a recuperação de desastre de região juntas. Por exemplo, o AKS está disponível no Leste dos EUA e no Oeste dos EUA. Essas regiões estão emparelhadas. Escolha essas duas regiões quando estiver criando uma estratégia AKS BC/DR.

Quando você implantar seu aplicativo, adicione mais um passo ao seu pipeline de CI/CD para implantar nesses vários clusters AKS. Se você não atualizar seus pipelines de implantação, os aplicativos podem ser implantados em apenas uma de suas regiões e clusters AKS. O tráfego do cliente direcionado para uma região secundária não receberá as últimas atualizações de código.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de Tráfego do Azure para rotear o tráfego

**Práticas recomendadas**: O Azure Traffic Manager pode direcionar os clientes para o cluster e instância de aplicativo aks mais próximos. Para obter o melhor desempenho e redundância, direcione todo o tráfego de aplicativos através do Traffic Manager antes que ele vá para o cluster AKS.

Se você tiver vários clusters AKS em diferentes regiões, use o Traffic Manager para controlar como o tráfego flui para os aplicativos executados em cada cluster. O [Gerenciador de Tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/) é um balanceador de carga de tráfego com base em DNS que pode distribuir o tráfego de rede entre regiões. Use o Gerenciador de tráfego para rotear usuários com base no tempo de resposta do cluster ou com base na geografia.

![AKS com gerenciador de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Os clientes que possuem um único cluster AKS normalmente se conectam ao ip de serviço ou nome DNS de um determinado aplicativo. Em uma implantação multicluster, os clientes devem se conectar a um nome DNS do Gerenciador de Tráfego que aponta para os serviços em cada cluster AKS. Defina esses serviços usando os pontos finais do Traffic Manager. Cada ponto final é o *IP do balanceador de carga de serviço*. Use essa configuração para direcionar o tráfego de rede do ponto final do Gerenciador de Tráfego em uma região até o ponto final em uma região diferente.

![Roteamento geográfico através do Gerenciador de Tráfego](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Traffic Manager realiza as procurar dns e retorna o ponto final mais apropriado do usuário. Perfis aninhados podem priorizar um local principal. Por exemplo, os usuários geralmente devem se conectar à sua região geográfica mais próxima. Se essa região tiver um problema, o Traffic Manager direciona os usuários para uma região secundária. Essa abordagem garante que os clientes possam se conectar a uma instância de aplicativo, mesmo que sua região geográfica mais próxima não esteja disponível.

Para obter informações sobre como configurar pontos finais e roteamento, consulte [Configure o método de roteamento geográfico de tráfego usando o Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Roteamento do aplicativo de camada 7 com o serviço da porta frontal do Azure

O Gerenciador de Tráfego usa DNS (camada 3) para moldar o tráfego. [O Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fornece uma opção de roteamento HTTP/HTTPS (camada 7). Os recursos adicionais do Azure Front Door Service incluem terminação TLS, domínio personalizado, firewall de aplicativos web, regravação de URL e afinidade de sessão. Examine as necessidades de seu tráfego de aplicativo para entender qual é a solução mais adequada.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Interconectar regiões com peering de rede virtual global

Se os clusters precisarem conversar entre si, conectar ambas as redes virtuais entre si pode ser alcançado por meio [de peering de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Essa tecnologia interconecta redes virtuais entre si, fornecendo alta largura de banda em toda a rede backbone da Microsoft, mesmo em diferentes regiões geográficas.

Um pré-requisito para observar as redes virtuais onde os clusters AKS estão sendo executados é usar o Balanceador de Carga padrão em seu cluster AKS, para que os serviços Kubernetes sejam acessíveis em toda a rede virtual.

## <a name="enable-geo-replication-for-container-images"></a>Habilitar a replicação geográfica para imagens de contêiner

**Melhores práticas**: Armazene suas imagens de contêiner no Registro de Contêineres do Azure e reproduza geo-replicar o registro em cada região aks.

Para implantar e executar seus aplicativos no AKS, você precisará ter uma maneira de armazenar e efetuar pull das imagens de contêiner. O Registro de Contêineres integra-se ao AKS, para que possa armazenar com segurança suas imagens de contêiner ou gráficos Helm. O Container Registry suporta a replicação geomaster multimaster para replicar automaticamente suas imagens em regiões do Azure em todo o mundo. 

Para melhorar o desempenho e a disponibilidade, use a geo-replicação do Registro de Contêineres para criar um registro em cada região onde você tenha um cluster AKS. Cada cluster AKS então retira imagens de contêiner do registro local de contêineres na mesma região:

![Geo-replicação do Registro de Contêineres para imagens de contêineres](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando você usa a geo-replicação do Registro de Contêineres para retirar imagens da mesma região, os resultados são:

* **Mais rápido**: Você puxa imagens de conexões de rede de alta velocidade e baixa latência dentro da mesma região do Azure.
* **Mais confiável**: Se uma região não estiver disponível, o cluster AKS retira as imagens de um registro de contêiner disponível.
* **Mais barato**: Não há cobrança de saída de rede entre data centers.

A replicação geográfica é uma característica dos registros de contêineres *Premium* SKU. Para obter informações sobre como configurar a replicação geográfica, consulte [geo-replicação do Registro de Contêineres](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Remover o estado do serviço de dentro dos contêineres

**Melhores práticas**: Sempre que possível, não armazene o estado de serviço dentro do contêiner. Em vez disso, use uma plataforma Azure como um serviço (PaaS) que suporta a replicação multirregião.

*O estado de* serviço refere-se aos dados na memória ou no disco que um serviço requer para funcionar. O estado inclui as estruturas de dados e variáveis de membro que o serviço lê e grava. Dependendo de como o serviço é arquitetado, o estado também pode incluir arquivos ou outros recursos armazenados no disco. Por exemplo, o estado pode incluir os arquivos que um banco de dados usa para armazenar dados e registros de transações.

O estado pode ser externado ou localizado com o código que manipula o estado. Normalmente, você externaliza o estado usando um banco de dados ou outro armazenamento de dados que é executado em diferentes máquinas pela rede ou que fica sem processo na mesma máquina.

Contêineres e microserviços são mais resistentes quando os processos que correm dentro deles não retêm estado. Como os aplicativos quase sempre contêm algum estado, use uma solução PaaS como o Azure Database for MySQL, Azure Database for PostgreSQL ou Azure SQL Database.

Para criar aplicativos portáteis, consulte as seguintes diretrizes:

* [A metodologia do aplicativo de 12 fatores](https://12factor.net/)
* [Executar um aplicativo Web em várias regiões do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

**Práticas recomendadas**: Se você usar o Azure Storage, prepare e teste como migrar seu armazenamento da região primária para a região de backup.

Seus aplicativos podem usar o Azure Storage para seus dados. Como seus aplicativos estão espalhados por vários clusters AKS em diferentes regiões, você precisa manter o armazenamento sincronizado. Aqui estão duas maneiras comuns de replicar o armazenamento:

* Replicação assíncrona baseada em infraestrutura
* Replicação assíncrona baseada em aplicativo

### <a name="infrastructure-based-asynchronous-replication"></a>Replicação assíncrona baseada em infraestrutura

Seus aplicativos podem exigir armazenamento persistente mesmo depois que um pod é excluído. Em Kubernetes, você pode usar volumes persistentes para persistir o armazenamento de dados. Os volumes persistentes são montados em um vm de nó e, em seguida, expostos às cápsulas. Os volumes persistentes seguem as cápsulas mesmo que as cápsulas sejam movidas para um nó diferente dentro do mesmo cluster.

A estratégia de replicação que você usa depende da sua solução de armazenamento. Soluções comuns de armazenamento como [Gluster,](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/) [Ceph,](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/) [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) fornecem suas próprias orientações sobre recuperação e replicação de desastres.

A estratégia típica é fornecer um ponto de armazenamento comum onde os aplicativos podem gravar seus dados. Esses dados são replicados entre regiões e acessados localmente.

![Replicação assíncrona baseada em infraestrutura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se você usar discos gerenciados do Azure, poderá escolher soluções de replicação e DR como estas:

* [Velero no Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replicação assíncrona baseada em aplicativo

Atualmente, o Kubernetes não oferece nenhuma implementação nativa para replicação assíncrona baseada em aplicativos. Como os contêineres e kubernetes estão vagamente acoplados, qualquer abordagem tradicional de aplicação ou idioma deve funcionar. Normalmente, os próprios aplicativos replicam as solicitações de armazenamento, que são então escritas para o armazenamento de dados subjacente de cada cluster.

![Replicação assíncrona baseada em aplicativo](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Próximas etapas

Este artigo foca em considerações de continuidade de negócios e recuperação de desastres para clusters AKS. Para obter mais informações sobre operações de cluster no AKS, consulte estes artigos sobre as melhores práticas:

* [Multitnancy e isolamento de clusters][aks-best-practices-cluster-isolation]
* [Recursos básicos de Agendador Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
