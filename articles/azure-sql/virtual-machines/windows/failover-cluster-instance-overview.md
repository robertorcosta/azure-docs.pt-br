---
title: Instâncias de cluster de failover
description: Saiba mais sobre FCIs (instâncias de cluster de failover) com SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 8a5374bf15798fd7e53f0d93e69f2f40a2d57b94
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533811"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Instâncias de cluster de failover com SQL Server em máquinas virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo apresenta diferenças de recursos quando você está trabalhando com FCI (instâncias de cluster de failover) para SQL Server em VMs (máquinas virtuais) do Azure. 

## <a name="overview"></a>Visão geral

SQL Server em VMs do Azure usa a funcionalidade WSFC (Windows Server failover clustering) para fornecer alta disponibilidade local por meio de redundância no nível de instância de servidor: uma instância de cluster de failover. Um FCI é uma única instância do SQL Server que é instalado nos nós do WSFC (ou simplesmente do cluster) e, possivelmente, em várias sub-redes. Na rede, um FCI parece ser uma instância do SQL Server em execução em um único computador. Mas o FCI fornece failover de um nó WSFC para outro se o nó atual ficar indisponível.

O restante do artigo se concentra nas diferenças de instâncias de cluster de failover quando elas são usadas com SQL Server em VMs do Azure. Para saber mais sobre a tecnologia de clustering de failover, consulte: 

- [Tecnologias de cluster do Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instâncias de cluster de failover](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Instâncias de cluster de failover com SQL Server em máquinas virtuais do Azure dão suporte ao uso de uma testemunha de disco, uma testemunha de nuvem ou uma testemunha de compartilhamento de arquivos para quorum de cluster.

Para saber mais, confira [práticas recomendadas de quorum com SQL Server VMs no Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Armazenamento

Em ambientes clusterizados tradicionais locais, um cluster de failover do Windows usa uma SAN (rede de área de armazenamento) que é acessível por ambos os nós como o armazenamento compartilhado. SQL Server arquivos são hospedados no armazenamento compartilhado, e somente o nó ativo pode acessar os arquivos ao mesmo tempo. 

SQL Server em VMs do Azure oferece várias opções como uma solução de armazenamento compartilhado para uma implantação de instâncias de cluster de failover SQL Server: 

||[Discos compartilhados do Azure](../../../virtual-machines/windows/disks-shared.md)|[Compartilhamentos de arquivos Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[S2D (Espaços de Armazenamento Diretos)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Versão mínima do SO**| Tudo |Windows Server 2012|Windows Server 2016|
|**Versão mínima do SQL Server**|Tudo|SQL Server 2012|SQL Server 2016|
|**Disponibilidade de VM com suporte** |Conjuntos de disponibilidade com grupos de posicionamento de proximidade |Conjuntos de disponibilidade e zonas de disponibilidade|Conjuntos de disponibilidade |
|**Dá suporte a FileStream**|Sim|Não|Sim |
|**Cache de blob do Azure**|Não|Não|Sim|

O restante desta seção lista os benefícios e as limitações de cada opção de armazenamento disponível para SQL Server em VMs do Azure. 

### <a name="azure-shared-disks"></a>Discos compartilhados do Azure

Os [discos compartilhados do Azure](../../../virtual-machines/windows/disks-shared.md) são um recurso dos [Azure Managed disks](../../../virtual-machines/windows/managed-disks-overview.md). O Windows Server failover clustering dá suporte ao uso de discos compartilhados do Azure com uma instância de cluster de failover. 

**Sistema operacional com suporte**: todos   
**Versão do SQL com suporte**: todos     

**Benefícios**: 
- Útil para aplicativos que procuram migrar para o Azure mantendo sua arquitetura de alta disponibilidade e de recuperação de desastres (HADR) como está. 
- O pode migrar aplicativos clusterizados para o Azure como ocorre devido ao suporte de reservas persistentes de SCSI (RP). 
- Dá suporte ao Azure SSD Premium compartilhado para todas as versões do SQL Server e do Azure ultra Armazenamento em Disco compartilhado para SQL Server 2019. 
- Pode usar um único disco compartilhado ou distribuir vários discos compartilhados para criar um pool de armazenamento compartilhado. 
- Dá suporte a FileStream.


**Limitações**: 
- As máquinas virtuais devem ser colocadas no mesmo conjunto de disponibilidade e grupo de posicionamento de proximidade.
- Não há suporte para zonas de disponibilidade.
- Não há suporte para o cache de disco SSD Premium.
 
Para começar, consulte [SQL Server instância de cluster de failover com discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Espaços de Armazenamento Direct

O [espaços de armazenamento diretos](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) é um recurso do Windows Server que tem suporte com clustering de failover em máquinas virtuais do Azure. Ele fornece uma SAN virtual baseada em software.

**Sistema operacional com suporte**: Windows Server 2016 e posterior   
**Versão do SQL com suporte**: SQL Server 2016 e posterior   


**Benefícios** 
- Largura de banda de rede suficiente permite uma solução de armazenamento compartilhado robusta e altamente funcional. 
- Dá suporte ao cache de blob do Azure, portanto, as leituras podem ser servidas localmente do cache. (As atualizações são replicadas simultaneamente para ambos os nós.) 
- Dá suporte a FileStream. 

**Limitações**
- Disponível somente para o Windows Server 2016 e posterior. 
- Não há suporte para zonas de disponibilidade.
- Requer a mesma capacidade de disco anexada a ambas as máquinas virtuais. 
- Alta largura de banda de rede é necessária para alcançar alto desempenho devido à replicação em disco em andamento. 
- Exige um tamanho de VM maior e um pagamento duplo para armazenamento, pois o armazenamento é anexado a cada VM. 

Para começar, consulte [SQL Server instância de cluster de failover com espaços de armazenamento diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 

### <a name="premium-file-share"></a>Compartilhamento de arquivos Premium

Os [compartilhamentos de arquivos Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) são um recurso dos [arquivos do Azure](../../../storage/files/index.yml). Os compartilhamentos de arquivos Premium têm suporte de SSD e têm baixa latência consistentemente. Eles têm suporte total para uso com instâncias de cluster de failover para o SQL Server 2012 ou posterior no Windows Server 2012 ou posterior. Os compartilhamentos de arquivos Premium proporcionam maior flexibilidade, pois você pode redimensionar e dimensionar um compartilhamento de arquivos sem nenhum tempo de inatividade.

**Sistema operacional com suporte**: Windows Server 2012 e posterior   
**Versão do SQL com suporte**: SQL Server 2012 e posterior   

**Benefícios** 
- Somente a solução de armazenamento compartilhado para máquinas virtuais se espalha em várias zonas de disponibilidade. 
- Sistema de arquivos totalmente gerenciado com latências de dígito único e desempenho de e/s intermitente. 

**Limitações**
- Disponível somente para o Windows Server 2012 e posterior. 
- Não há suporte para FileStream. 


Para começar, consulte [SQL Server instância de cluster de failover com compartilhamento de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Parceiro

Há soluções de clustering de parceiros com armazenamento com suporte. 

**Sistema operacional com suporte**: todos   
**Versão do SQL com suporte**: todos   

Um exemplo usa o SIOS datakeeper como o armazenamento. Para obter mais informações, consulte a entrada de blog [clustering de failover e sios Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI e ExpressRoute

Você também pode expor um armazenamento de bloco compartilhado de destino iSCSI por meio do Azure ExpressRoute. 

**Sistema operacional com suporte**: todos   
**Versão do SQL com suporte**: todos   

Por exemplo, o NPS (Armazenamento Privado do NetApp) expõe um destino iSCSI por meio do ExpressRoute com o Equinix para VMs do Azure.

Para soluções de armazenamento compartilhado e replicação de dados de parceiros da Microsoft, entre em contato com o fornecedor para obter quaisquer problemas relacionados ao acesso a dados no failover.

## <a name="connectivity"></a>Conectividade

Instâncias de cluster de failover com SQL Server em máquinas virtuais do Azure usam um [DNN (nome de rede distribuída)](hadr-distributed-network-name-dnn-configure.md) ou um [VNN (nome de rede Virtual) com Azure Load Balancer](hadr-vnn-azure-load-balancer-configure.md) para rotear o tráfego para a instância de SQL Server, independentemente de qual nó possui atualmente os recursos clusterizados. Há considerações adicionais ao usar determinados recursos e o DNN com um SQL Server FCI. Consulte [interoperabilidade do DNN com SQL Server FCI](failover-cluster-instance-dnn-interoperability.md) para saber mais. 

Para obter mais detalhes sobre as opções de conectividade de cluster, consulte [rotear conexões HADR para SQL Server em VMs do Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitações

Considere as seguintes limitações para instâncias de cluster de failover com SQL Server em máquinas virtuais do Azure. 

### <a name="lightweight-resource-provider"></a>Provedor de recursos leves   
Neste momento, SQL Server instâncias de cluster de failover em máquinas virtuais do Azure têm suporte apenas com o [modo de gerenciamento leve](sql-vm-resource-provider-register.md#management-modes) da [extensão SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Para alterar do modo de extensão completo para leve, exclua o recurso de **máquina virtual do SQL** para as VMs correspondentes e registre-as com o provedor de recursos de VM do SQL no modo leve. Quando você estiver excluindo o recurso de **máquina virtual do SQL** usando o portal do Azure, desmarque a caixa de seleção ao lado da máquina virtual correta. 

A extensão completa dá suporte a recursos como backup automatizado, aplicação de patch e gerenciamento avançado do portal. Esses recursos não funcionarão para VMs SQL Server depois que o agente for reinstalado no modo de gerenciamento leve.

### <a name="msdtc"></a>MSDTC   
As máquinas virtuais do Azure dão suporte ao MSDTC no Windows Server 2019 com armazenamento em CSV (volumes compartilhados clusterizados) e [Standard Load Balancer do Azure](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais do Azure, o MSDTC não tem suporte para o Windows Server 2016 ou anterior porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. No Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver disponível. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.


## <a name="next-steps"></a>Próximas etapas

Examine as [práticas recomendadas de configuração de cluster](hadr-cluster-best-practices.md)e, em seguida, você pode [preparar sua VM SQL Server para FCI](failover-cluster-instance-prepare-vm.md). 

Para obter mais informações, consulte: 

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instâncias de cluster de failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

