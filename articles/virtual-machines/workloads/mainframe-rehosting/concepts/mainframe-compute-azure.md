---
title: Mova a computação de mainframe para máquinas virtuais do Azure
description: Os recursos de computação do Azure comparam-se favoravelmente à capacidade do mainframe para que você possa migrar e modernizar os aplicativos IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288923"
---
# <a name="move-mainframe-compute-to-azure"></a>Mover o computador de mainframe para o Azure

Os mainframes têm uma reputação de alta confiabilidade e disponibilidade e continuam a ser a espinha dorsal confiável de muitas empresas. Muitas vezes se pensa que eles têm escalabilidade quase ilimitada e poder de computação também. No entanto, algumas empresas superaram a capacidade dos maiores mainframes disponíveis. Se isso soar como você, o Azure oferece agilidade, alcance e economia de infra-estrutura.

Para executar cargas de trabalho de mainframe no Microsoft Azure, você precisa saber como os recursos de computação do seu mainframe se comparam ao Azure. Baseado em um mainframe IBM z14 (o modelo mais atual a partir desta redação), este artigo diz como obter resultados comparáveis no Azure.

Para começar, considere os ambientes lado a lado. A figura a seguir compara um ambiente de mainframe para executar aplicativos a um ambiente de hospedagem do Azure.

![Serviços do Azure e ambientes de emulação oferecem suporte comparável e agiliza a migração](media/mainframe-compute-azure.png)

O poder dos mainframes é frequentemente usado para sistemas de processamento de transações on-line (OLTP) que lidam com milhões de atualizações para milhares de usuários. Esses aplicativos geralmente usam software para processamento de transações, manuseio de tela e entrada de formulários. Eles podem usar um Sistema de Controle de Informações do Cliente (CICS), Sistema de Gerenciamento de Informações (IMS) ou Pacote de Interface de Transação (TIP).

Como a figura mostra, um emulador TPM no Azure pode lidar com cargas de trabalho CICS e IMS. Um emulador de sistema em lote no Azure desempenha o papel de Linguagem de Controle de Emprego (JCL). Os dados do mainframe são migrados para bancos de dados do Azure, como o Azure SQL Database. Os serviços do Azure ou outros softwares hospedados no Azure Virtual Machines podem ser usados para o gerenciamento do sistema.

## <a name="mainframe-compute-at-a-glance"></a>Computação de mainframe em um relance

No mainframe z14, os processadores são dispostos em até quatro *gavetas*. Uma *gaveta* é simplesmente um cluster de processadores e chipsets. Cada gaveta pode ter seis chips de processador central ativo (CP), e cada CP tem 10 chips de controlador de sistema (SC). Na terminologia Intel x86, há seis soquetes por gaveta, 10 núcleos por soquete e quatro gavetas. Esta arquitetura fornece o equivalente áspero de 24 soquetes e 240 núcleos, no máximo, para um z14.

O FAST Z14 CP tem uma velocidade de relógio de 5,2 GHz. Normalmente, um z14 é entregue com todos os CPs na caixa. Eles estão ativados conforme necessário. Um cliente é comumente cobrado por pelo menos quatro horas de tempo de computação por mês, apesar do uso real.

Um processador de mainframe pode ser configurado como um dos seguintes tipos:

- Processador de Propósito Geral (GP)
- Processador de informações integradado do sistema z (zIIP)
- Processador Integrated Facility for Linux (IFL)
- Processador de assistência do sistema (SAP)
- Processador ICF (Integrated Coupling Facility, instalação de acoplamento integrado)

## <a name="scaling-mainframe-compute-up-and-out"></a>Dimensionamento da computação do mainframe para cima e para fora

Os mainframes da IBM oferecem a capacidade de escalar até 240 núcleos (o tamanho atual z14 para um único sistema). Além disso, os mainframes da IBM podem ser dimensionados através de um recurso chamado Centro de Acoplamento (CF). O CF permite que vários sistemas de mainframe acessem os mesmos dados simultaneamente. Usando o CF, a tecnologia Sysplex paralelo do mainframe agrupa processadores de mainframe em clusters. Quando este guia foi escrito, o recurso Sysplex Paralelo suportava 32 agrupamentos de 64 processadores cada. Até 2.048 processadores podem ser agrupados desta forma para reduzir a capacidade computacional.

Um CF permite que os clusters de computação compartilhem dados com acesso direto. É usado para bloquear informações, informações de cache e a lista de recursos de dados compartilhados. Um Sysplex paralelo usando um ou mais CFs pode ser considerado como um cluster de computação em escala "compartilhado de tudo". Para obter mais informações sobre esses recursos, consulte [Parallel Sysplex no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) no site da IBM.

Os aplicativos podem usar esses recursos para fornecer desempenho de escala e alta disponibilidade. Para obter informações sobre como o CICS pode usar o Parallel Sysplex com CF, baixe o [IBM CICS e o Acoplamento: Além do](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) livro vermelho básico.

## <a name="azure-compute-at-a-glance"></a>Azure compute em um relance

Algumas pessoas pensam erroneamente que os servidores baseados na Intel não são tão poderosos quanto os mainframes. No entanto, os novos sistemas baseados em Intel têm tanta capacidade computacional quanto os mainframes. Esta seção descreve as opções de infra-estrutura do Azure como serviço (IaaS) para computação e armazenamento. O Azure também fornece opções de plataforma como serviço (PaaS), mas este artigo se concentra nas opções de IaaS que fornecem capacidade de mainframe comparável.

As Máquinas Virtuais do Azure fornecem poder computacional em uma variedade de tamanhos e tipos. No Azure, uma CPU virtual (vCPU) equivale aproximadamente a um núcleo em um mainframe.

Atualmente, a gama de tamanhos de MáquinaVirtual Azure fornece de 1 a 128 vCPUs. Os tipos de máquinas virtuais (VM) são otimizados para cargas de trabalho específicas. Por exemplo, a lista a seguir mostra os tipos de VM (corrente a partir desta escrita) e seus usos recomendados:

| Tamanho     | Tipo e descrição                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Série D | Propósito geral com 64 vCPU e velocidade de relógio de até 3,5 GHz                           |
| Série E | Memória otimizada com até 64 vCPUs                                                 |
| Série F | Computação otimizada com até 64 vCPUs e velocidade de relógio de 3,7 GHz                       |
| Série H | Otimizado para aplicações de computação de alto desempenho (HPC)                          |
| Série L | Armazenamento otimizado para aplicativos de alto desempenho apoiados por bancos de dados como noSQL |
| Série M | Maior vMs otimizadas para computação e memória com até 128 vCPUs                        |

Para obter detalhes sobre as VMs disponíveis, consulte [a série Máquina Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Um mainframe z14 pode ter até 240 núcleos. No entanto, os mainframes z14 quase nunca usam todos os núcleos para um único aplicativo ou carga de trabalho. Em vez disso, um mainframe segrega cargas de trabalho em partições lógicas (LPARs), e os LPARs têm classificações — MIPS (Millions of Instructions Per Second) ou MSU (Million Service Unit). Ao determinar o tamanho comparável de VM necessário para executar uma carga de trabalho de mainframe no Azure, fator na classificação MIPS (ou MSU).

As estimativas gerais são as seguintes:

-   150 MIPS por vCPU

-   1.000 MIPS por processador

Para determinar o tamanho correto da VM para uma determinada carga de trabalho em um LPAR, primeiro otimize a VM para a carga de trabalho. Em seguida, determine o número de vCPUs necessários. Uma estimativa conservadora é de 150 MIPS por vCPU. Com base nessa estimativa, por exemplo, uma VM série F com 16 vCPUs poderia facilmente suportar uma carga de trabalho IBM Db2 proveniente de um LPAR com 2.400 MIPS.

## <a name="azure-compute-scale-up"></a>Scale-up de computação do Azure

As VMs da série M podem escalar até 128 vCPUs (no momento em que este artigo foi escrito). Usando a estimativa conservadora de 150 MIPS por vCPU, a Série M VM equivale a cerca de 19.000 MIPS. A regra geral para estimar MIPS para um mainframe é de 1.000 MIPS por processador. Um mainframe z14 pode ter até 24 processadores e fornecer cerca de 24.000 MIPS para um único sistema de mainframe.

O maior mainframe único z14 tem aproximadamente 5.000 MIPS a mais do que o maior VM disponível no Azure. No entanto, é importante comparar como as cargas de trabalho são implantadas. Se um sistema de mainframe tiver um aplicativo e um banco de dados relacional, eles normalmente serão implantados no mesmo mainframe físico — cada um em seu próprio LPAR. A mesma solução no Azure é frequentemente implantada usando uma VM para o aplicativo e uma VM separada e de tamanho adequado para o banco de dados.

Por exemplo, se um sistema de vCPU M64 suportar o aplicativo e um vCPU M96 for usado para o banco de dados, aproximadamente 150 vCPUs são necessários — ou cerca de 24.000 MIPS, como mostra a figura a seguir.

![Comparando implantações de carga de trabalho de 24.000 MIPS](media/mainframe-compute-mips.png)

A abordagem é migrar LPARs para VMs individuais. Em seguida, o Azure dimensiona facilmente até o tamanho necessário para a maioria dos aplicativos que são implantados em um único sistema de mainframe.

## <a name="azure-compute-scale-out"></a>Escala de computação do Azure

Uma das vantagens de uma solução baseada no Azure é a capacidade de dimensionar. O dimensionamento torna a capacidade de computação quase ilimitada disponível para um aplicativo. O Azure suporta vários métodos para dimensionar o poder de computação:

- **Balanceamento de carga através de um cluster.** Neste cenário, um aplicativo pode usar um [balanceador de carga](/azure/load-balancer/load-balancer-overview) ou gerenciador de recursos para espalhar a carga de trabalho entre várias VMs em um cluster. Se for necessário mais capacidade computacional, mais VMs serão adicionados ao cluster.

- **Conjuntos de escala de máquinas virtuais.** Neste cenário de estouro, um aplicativo pode dimensionar para recursos adicionais [de computação](/azure/virtual-machine-scale-sets/overview) com base no uso de VM. Quando a demanda cai, o número de VMs em um conjunto de escalas também pode diminuir, garantindo o uso eficiente do poder computacional.

- **Escala do PaaS.** Azure PaaS oferece recursos computacionais em escala. Por exemplo, [o Azure Service Fabric](/azure/service-fabric/service-fabric-overview) aloca recursos de computação para atender a aumentos no volume de solicitações.

- **Aglomerados kubernetes.** Os aplicativos no Azure podem usar [clusters Kubernetes](/azure/aks/concepts-clusters-workloads) para serviços de computação para recursos especificados. O Azure Kubernetes Service (AKS) é um serviço gerenciado que orquestra nódulos, piscinas e clusters kubernetes no Azure.

Para escolher o método certo para dimensionar recursos de computação, é importante entender como o Azure e os mainframes diferem. A chave é como ou se — os dados são compartilhados pelos recursos computacionais. No Azure, os dados (por padrão) não são normalmente compartilhados por várias VMs. Se o compartilhamento de dados for necessário por várias VMs em um cluster de computação em escala, os dados compartilhados devem residir em um recurso que suporta essa funcionalidade. No Azure, o compartilhamento de dados envolve o armazenamento, conforme a seção a seguir discute.

## <a name="azure-compute-optimization"></a>Otimização do azure compute

Você pode otimizar cada camada de processamento em uma arquitetura Azure. Use o tipo mais adequado de VMs e recursos para cada ambiente. A figura a seguir mostra um padrão potencial para implantar VMs no Azure para suportar um aplicativo CICS que usa Db2. No site primário, as VMs de produção, pré-produção e teste são implantadas com alta disponibilidade. O site secundário é feito para backup e recuperação de desastres.

Cada camada também pode fornecer serviços apropriados de recuperação de desastres. Por exemplo, VMs do banco de dados e produção podem requerer uma recuperação ativa ou passiva, enquanto as VMs de desenvolvimento e teste dão suporte a uma recuperação fria.

![Implantação altamente disponível que suporta recuperação de desastres](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Próximas etapas

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehospedagem do mainframe em máquinas virtuais do Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mova o armazenamento do mainframe para o Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e a Instalação de Acoplamento: Além do Básico](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criando usuários necessários para uma instalação de recurso db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Criar comando de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 solução de banco de dados clustered pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem do governo do Microsoft Azure para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
