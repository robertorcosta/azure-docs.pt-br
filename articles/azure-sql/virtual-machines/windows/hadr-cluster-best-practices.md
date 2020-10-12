---
title: Práticas recomendadas de configuração de cluster
description: Saiba mais sobre as configurações de cluster com suporte ao configurar a alta disponibilidade e a recuperação de desastres (HADR) para SQL Server em máquinas virtuais do Azure, como quorum com suporte ou opções de roteamento de conexão.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: e98bfbf58c179fe9df0d99e0522e5747d220ae52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317014"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Práticas recomendadas de configuração de cluster (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Um cluster é usado para a alta disponibilidade e a recuperação de desastres (HADR) com SQL Server em VMs (máquinas virtuais) do Azure. 

Este artigo fornece as práticas recomendadas de configuração de cluster para [FCIs (instâncias de cluster de failover)](failover-cluster-instance-overview.md) e [grupos de disponibilidade](availability-group-overview.md) ao usá-las com SQL Server em VMs do Azure. 


## <a name="networking"></a>Rede

Use uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física, o que torna as NICs e sub-redes adicionais desnecessárias em um cluster de convidado da máquina virtual do Azure. O relatório de validação de cluster avisará que os nós podem ser acessados apenas em uma rede. Você pode ignorar esse aviso em clusters de failover de convidados da máquina virtual do Azure.

## <a name="quorum"></a>Quorum

Embora um cluster de dois nós funcione sem um [recurso de quorum](/windows-server/storage/storage-spaces/understand-quorum), os clientes são estritamente obrigados a usar um recurso de quorum para ter suporte de produção. A validação de cluster não passará nenhum cluster sem um recurso de quorum. 

Tecnicamente, um cluster de três nós pode sobreviver a uma única perda de nó (até dois nós) sem um recurso de quorum. Mas, depois que o cluster estiver em dois nós, há um risco de que os recursos clusterizados fiquem offline no caso de uma falha de comunicação ou perda de nó para evitar um cenário de divisão-cérebro.

Configurar um recurso de quorum permitirá que o cluster continue online apenas com um nó online.

A tabela a seguir lista as opções de quorum disponíveis na ordem recomendada para usar com uma VM do Azure, com a testemunha de disco sendo a opção preferida: 


||[Testemunha de disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Testemunha da nuvem](/windows-server/failover-clustering/deploy-cloud-witness)  |[Testemunha de compartilhamento de arquivos](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**SO com suporte**| Todos |Windows Server 2016 e posterior| Todos|




### <a name="disk-witness"></a>Testemunha de disco

Uma testemunha de disco é um pequeno disco clusterizado no grupo de armazenamento disponível do cluster. Esse disco é altamente disponível e pode fazer failover entre nós. Ele contém uma cópia do banco de dados do cluster, com um tamanho padrão que geralmente é menor que 1 GB. A testemunha de disco é a opção de quorum preferencial para qualquer cluster que usa discos compartilhados do Azure (ou qualquer solução de disco compartilhado, como SCSI compartilhado, iSCSI ou SAN de canal de fibra).  Um volume compartilhado clusterizado não pode ser usado como uma testemunha de disco.

Configure um disco compartilhado do Azure como a testemunha de disco. 

Para começar, consulte [Configurar uma testemunha de disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Sistema operacional com suporte**: todos   


### <a name="cloud-witness"></a>Testemunha da nuvem

Uma testemunha de nuvem é um tipo de testemunha de quorum de cluster de failover que usa Microsoft Azure para fornecer um voto no quorum de cluster. O tamanho padrão é de cerca de 1 MB e contém apenas o carimbo de data/hora. Uma testemunha em nuvem é ideal para implantações em vários sites, zonas múltiplas e várias regiões.

Para começar, consulte [Configurar uma testemunha de nuvem](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**Sistema operacional com suporte**: Windows Server 2016 e posterior   


### <a name="file-share-witness"></a>Testemunha de compartilhamento de arquivos

Uma testemunha de compartilhamento de arquivos é um compartilhamento de arquivos SMB que normalmente é configurado em um servidor de arquivos que executa o Windows Server. Ele mantém informações de clustering em um arquivo testemunha. log, mas não armazena uma cópia do banco de dados do cluster. No Azure, você pode configurar um compartilhamento de [arquivos do Azure](../../../storage/files/storage-how-to-create-file-share.md) para usar como a testemunha de compartilhamento de arquivos ou pode usar um compartilhamento de arquivos em uma máquina virtual separada.

Se você for usar um compartilhamento de arquivos do Azure, poderá montá-lo com o mesmo processo usado para [montar o compartilhamento de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Para começar, consulte [Configurar uma testemunha de compartilhamento de arquivos](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Sistema operacional com suporte**: Windows Server 2012 e posterior   

## <a name="connectivity"></a>Conectividade

Em um ambiente de rede local tradicional, um SQL Server instância de cluster de failover parece ser uma única instância do SQL Server em execução em um único computador. Como a instância de cluster de failover faz failover do nó para o nó, o VNN (nome da rede virtual) da instância do fornece um ponto de conexão unificado e permite que os aplicativos se conectem à instância de SQL Server sem saber qual nó está ativo no momento. Quando ocorre um failover, o nome da rede virtual é registrado no novo nó ativo depois que ele é iniciado. Esse processo é transparente para o cliente ou aplicativo que está se conectando ao SQL Server, e isso minimiza o tempo de inatividade que o cliente ou o aplicativo enfrenta durante uma falha. 

Use um VNN com Azure Load Balancer ou um DNN (nome de rede distribuída) para rotear o tráfego para o VNN da instância de cluster de failover com SQL Server em VMs do Azure. O recurso DNN está disponível no momento somente para o SQL Server 2019 CU2 e posterior em uma máquina virtual do Windows Server 2016 (ou posterior). 

A tabela a seguir compara a suporte à conexão HADR: 

| |**VNN (nome da rede virtual)**  |**DNN (nome da rede distribuída)**  |
|---------|---------|---------|
|**Versão mínima do SO**| Todos | Todos |
|**Versão mínima do SQL Server** |Todos |SQL Server 2019 CU2|
|**Solução HADR com suporte** | Instância de cluster de failover <br/> grupo de disponibilidade | Instância de cluster de failover|


### <a name="virtual-network-name-vnn"></a>VNN (nome da rede virtual)

Como o ponto de acesso IP virtual funciona de modo diferente no Azure, você precisa configurar [Azure Load Balancer](../../../load-balancer/index.yml) para rotear o tráfego para o endereço IP dos nós FCI. Em máquinas virtuais do Azure, um balanceador de carga mantém o endereço IP para o VNN em que os recursos clusterizados SQL Server dependem. O balanceador de carga distribui os fluxos de entrada que chegam ao front-end e, em seguida, roteia esse tráfego para as instâncias definidas pelo pool de back-end. Você configura o fluxo de tráfego usando regras de balanceamento de carga e investigações de integridade. Com SQL Server FCI, as instâncias de pool de back-end são as máquinas virtuais do Azure em execução SQL Server. 

Há um pequeno atraso de failover quando você está usando o balanceador de carga, pois a investigação de integridade realiza verificações ativas a cada 10 segundos por padrão. 

Para começar, saiba como [configurar Azure Load Balancer para um FCI](hadr-vnn-azure-load-balancer-configure.md). 

**Sistema operacional com suporte**: todos   
**Versão do SQL com suporte**: todos   
**Solução HADR com suporte**: instância de cluster de failover e grupo de disponibilidade   


### <a name="distributed-network-name-dnn"></a>DNN (nome da rede distribuída)

O nome de rede distribuída é um novo recurso do Azure para SQL Server 2019 CU2. O DNN fornece uma maneira alternativa para os clientes SQL Server se conectarem à instância de cluster de failover do SQL Server sem usar um balanceador de carga. 

Quando um recurso DNN é criado, o cluster associa o nome DNS com os endereços IP de todos os nós no cluster. O cliente SQL tentará se conectar a cada endereço IP nessa lista para localizar o nó no qual a instância de cluster de failover está em execução no momento. Você pode acelerar esse processo especificando `MultiSubnetFailover=True` na cadeia de conexão. Essa configuração informa ao provedor para testar todos os endereços IP em paralelo, para que o cliente possa se conectar ao FCI instantaneamente. 

Um nome de rede distribuído é recomendado em um balanceador de carga quando possível porque: 
- A solução de ponta a ponta é mais robusta, pois você não precisa mais manter o recurso de balanceador de carga. 
- A eliminação das investigações do balanceador de carga minimiza a duração do failover. 
- O DNN simplifica o provisionamento e o gerenciamento da instância de cluster de failover com SQL Server em VMs do Azure. 

A maioria dos recursos de SQL Server funciona de forma transparente com FCI. Nesses casos, você pode simplesmente substituir o nome DNS VNN existente pelo nome DNS DNN ou definir o valor de DNN com o nome DNS VNN existente. No entanto, alguns componentes do lado do servidor exigem um alias de rede que mapeia o nome do VNN para o nome do DNN. Casos específicos podem exigir o uso explícito do nome DNS DNN, como quando você está definindo determinadas URLs em uma configuração do lado do servidor. 

Para começar, saiba como [configurar um recurso DNN para um FCI](hadr-distributed-network-name-dnn-configure.md). 

**Sistema operacional com suporte**: Windows Server 2016 e posterior   
**Versão do SQL com suporte**: SQL Server 2019 e posterior   
**Solução HADR com suporte**: somente instância de cluster de failover


## <a name="limitations"></a>Limitações

Considere as seguintes limitações quando estiver trabalhando com FCI ou grupos de disponibilidade e SQL Server em máquinas virtuais do Azure. 

### <a name="msdtc"></a>MSDTC 

As máquinas virtuais do Azure dão suporte ao Microsoft Coordenador de Transações Distribuídas (MSDTC) no Windows Server 2019 com armazenamento em CSV (volumes compartilhados clusterizados) e no [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) ou em VMs SQL Server que estão usando discos compartilhados do Azure. 

Em máquinas virtuais do Azure, o MSDTC não tem suporte para o Windows Server 2016 ou anterior com volumes compartilhados clusterizados porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. No Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver disponível. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.


## <a name="next-steps"></a>Próximas etapas

Depois de determinar as práticas recomendadas apropriadas para sua solução, comece [preparando sua VM SQL Server para o FCI](failover-cluster-instance-prepare-vm.md). Você também pode criar seu grupo de disponibilidade usando o [CLI do Azure](availability-group-az-cli-configure.md)ou os [modelos de início rápido do Azure](availability-group-quickstart-template-configure.md). 

