---
title: Configurar grupo de disponibilidade em diferentes regiões
description: Este artigo explica como configurar um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure com uma réplica em uma região diferente.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8ca871a6f525d4e68ce70060e6faddbcfc8e1f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060124"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Configure um grupo de disponibilidade em máquinas virtuais Do Azure SQL Server em diferentes regiões

Este artigo explica como configurar uma réplica do SQL Server sempre no grupo de disponibilidade em máquinas virtuais do Azure em uma localização remota do Azure. Use essa configuração para dar suporte à recuperação de desastre.

Este artigo se aplica às máquinas virtuais do Azure no modo do Resource Manager.

A imagem a seguir mostra uma implementação comum de um grupo de disponibilidade em máquinas virtuais do Azure:

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Nessa implantação, todas as máquinas virtuais estão em uma região do Azure. As réplicas do grupo de disponibilidade podem ter de confirmação síncrona com failover automático no SQL-1 e SQL-2. Para criar essa arquitetura, veja [Modelo ou tutorial de Grupo de Disponibilidade](virtual-machines-windows-portal-sql-availability-group-overview.md).

Essa arquitetura é vulnerável a tempo de inatividade se a região do Azure se tornar inacessível. Para superar essa vulnerabilidade, adicione uma réplica em uma região do Azure diferente. O diagrama a seguir mostra como a nova arquitetura:

   ![Recuperação de desastre de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

O diagrama acima mostra uma nova máquina virtual chamada SQL-3. SQL-3 está em outra região do Azure. SQL-3 é adicionado para o Cluster de Failover do Windows Server. SQL-3 pode hospedar uma réplica do grupo de disponibilidade. Finalmente, observe que a região do Azure para o SQL-3 tem um novo balanceador de carga do Azure.

>[!NOTE]
> Um conjunto de disponibilidade do Azure é necessário quando mais de uma máquina virtual está na mesma região. Se houver apenas uma máquina virtual na região, o conjunto de disponibilidade não é necessário. Você só pode colocar uma máquina virtual em um momento da criação do conjunto de disponibilidade. Se a máquina virtual já estiver em um conjunto de disponibilidade, você poderá adicionar uma máquina virtual para fazer uma réplica adicional mais tarde.

Nesta arquitetura, a réplica na região remota normalmente é configurada com o modo de disponibilidade de confirmação assíncrona e modo de failover manual.

Quando as réplicas de grupo de disponibilidade em máquinas virtuais do Azure em diferentes regiões do Azure, requer que cada região:

* Um gateway de rede virtual
* Uma conexão de gateway de rede virtual

O diagrama a seguir mostra como as redes se comunicam entre data centers.

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Essa arquitetura incorre em encargos de dados de saída para os dados replicados entre regiões do Azure. Veja [Preços de Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Criar réplica remota

Para criar uma réplica em um data center remoto, execute as seguintes etapas:

1. [Criar uma rede virtual na nova região](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Configure uma conexão VNet-to-VNet usando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Em alguns casos, você talvez precise usar o PowerShell para criar a conexão de VNet para VNet. Por exemplo, se você usar diferentes contas do Azure, será possível configurar a conexão no portal. Neste caso, veja [Configurar uma conexão de Rede Virtual para Rede Virtual usando o portal do Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Crie um controlador de domínio na nova região](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Esse controlador de domínio fornecerá autenticação se o controlador de domínio no site primário não estiver disponível.

1. [Criar uma máquina virtual do SQL Server na nova região](virtual-machines-windows-portal-sql-server-provision.md).

1. [Criar um balanceador de carga do Azure na rede em que a nova região](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Este balanceador de carga deve:

   - Estar na mesma rede e sub-rede como a nova máquina virtual.
   - Ter um endereço IP estático para o ouvinte do grupo de disponibilidade.
   - Inclua um pool de back-end que consiste somente as máquinas virtuais na mesma região que o balanceador de carga.
   - Use um teste de porta TCP específico para o endereço IP.
   - Ter uma regra específica para o SQL Server na mesma região balanceamento de carga.  
   - Seja um Standard Load Balancer se as máquinas virtuais no pool de back-end não forem parte de um único conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais. Para obter mais informações, examine [Visão geral do Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Adicionar o recurso cluster de Failover para o novo servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Adicionar o novo SQL Server ao domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Definir a nova conta de serviço do SQL Server para usar uma conta de domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Adicionar o novo SQL Server para o Cluster de Failover do Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Adicione um recurso de endereço IP ao cluster.

   Você pode criar o recurso de endereço IP no Gerenciador de Cluster de Failover. Selecione o nome do cluster e clique com o botão direito do mouse no nome do cluster em **Recursos de núcleo de cluster** e selecione **Propriedades**: 

   ![Propriedades do cluster](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-name-properties.png)

   Na caixa de diálogo **Propriedades,** **selecione Adicionar** em **endereço IP**e, em seguida, adicione o endereço IP do nome do cluster na região da rede remota. Selecione **OK** na caixa de diálogo **Endereço IP** e selecione **OK** novamente na caixa de diálogo Propriedades de **cluster** para salvar o novo endereço IP.. 

   ![Adicionar IP de cluster](./media/virtual-machines-windows-portal-sql-availability-group-dr/add-cluster-ip-address.png)


1. Adicione o endereço IP como uma dependência para o nome do cluster principal.

   Abra as propriedades do cluster mais uma vez e selecione a guia **Dependências.** Configure uma dependência OU para os dois endereços IP: 

   ![Propriedades do cluster](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-ip-dependencies.png)

1. Adicione um recurso de endereço IP à função de grupo de disponibilidade no cluster. 

   Clique com o botão direito do mouse na função grupo de disponibilidade no Failover Cluster Manager, selecione **Adicionar recurso,** **mais recursos**e selecione endereço **IP**.

   ![Criar endereço IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configure o endereço IP da seguinte maneira:

   - Use a rede do data center remoto.
   - Atribua o endereço IP do balanceador de carga do Azure novo. 

1. Adicione o recurso de endereço IP como uma dependência para o cluster do ouvinte cliente acesso ponto (nome da rede).

   Captura de tela a seguir mostra um recurso de cluster de endereço IP configurado corretamente:

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >O grupo de recursos de cluster inclui os dois endereços IP. Os dois endereços IP são dependências para o ponto de acesso de cliente do ouvinte. Use o **ou** operador na configuração de dependência do cluster.

1. [Defina os parâmetros de cluster no PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Execute o script do PowerShell com o nome de rede do cluster, o endereço IP e a porta de investigação configurado no balanceador de carga na nova região.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. No novo servidor SQL no SQL Server Configuration Manager, [habilitar grupos de disponibilidade AlwaysOn](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Abrir portas de firewall no novo servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Os números de porta que você precisa abrir dependem de seu ambiente. Investigação de integridade do balanceador de carga de portas abertas para o ponto de extremidade de espelhamento e o Azure.


1. [Adicionar uma réplica para o grupo de disponibilidade no novo servidor SQL](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Para uma réplica em uma região remota do Azure, configurá-lo para a replicação assíncrona com failover manual.  

## <a name="set-connection-for-multiple-subnets"></a>Definir conexão para várias sub-redes

A réplica no data center remoto é parte do grupo de disponibilidade, mas ele está em uma sub-rede diferente. Se essa réplica se tornar a réplica primária, tempo limite de conexão do aplicativo podem ocorrer. Esse comportamento é o mesmo que um grupo de disponibilidade local em uma implantação de várias sub-redes. Para permitir conexões de cliente de aplicativos, atualize a conexão de cliente ou configurar a resolução de nome do recurso de nome de rede de cluster de cache.

Preferencialmente, atualize as cadeias de conexão do cliente para definir `MultiSubnetFailover=Yes`. Consulte [conectar-se ao MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Se você não pode modificar as cadeias de conexão, você pode configurar o cache de resolução de nome. Consulte [o erro de tempo e não é possível conectar-se a um ouvinte do grupo de disponibilidade SQL Server 2012 AlwaysOn em um ambiente de várias sub-redes](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Failover para região remota

Para testar a conectividade do ouvinte para a região remota, é possível realizar failover da réplica para a região remota. Enquanto a réplica é assíncrona, o failover é vulnerável à perda de dados. Para fazer failover sem perda de dados, alterar o modo de disponibilidade para síncrono e defina o modo de failover como automático. Use as seguintes etapas:

1. Em **Pesquisador**, conecte-se à instância do SQL Server que hospeda a réplica primária.
1. Em **grupos de disponibilidade do AlwaysOn**, **grupos de disponibilidade**, clique com botão direito seu grupo de disponibilidade e clique em **propriedades**.
1. No **geral** página, em **réplicas de disponibilidade**, definir a réplica secundária no site para usar a recuperação de desastres **confirmação síncrona** modo de disponibilidade e **automáticas** modo de failover.
1. Se você tiver uma réplica secundária no mesmo site que a réplica primária para alta disponibilidade, definido desta réplica como **confirmação assíncrona** e **Manual**.
1. Clique em OK.
1. Em **Pesquisador**, com o botão direito no grupo de disponibilidade e clique em **Mostrar painel**.
1. No painel, verifique se a réplica no site de recuperação de desastre está sincronizada.
1. No **Object Explorer,** clique com o botão direito do mouse no grupo de disponibilidade e clique em **Failover...**. O SQL Server Management Studios abre um assistente para falhar no SQL Server.  
1. Clique em **Avançar** e selecione a instância do SQL Server no site de recuperação de desastre. Clique em **Avançar** novamente.
1. Conecte-se à instância do SQL Server no site de recuperação de desastre e clique em **Avançar**.
1. Sobre o **resumo** página, verifique as configurações e clique em **concluir**.

Depois de testar a conectividade, mova a réplica primária de volta para seu data center principal e definir o modo de disponibilidade para suas configurações operacionais normais. A tabela a seguir mostra as configurações operacionais normais para a arquitetura descrita neste documento:

| Location | Instância do servidor | Função | Modo de Disponibilidade | Modo de failover
| ----- | ----- | ----- | ----- | -----
| Data center principal | SQL-1 | Primária | Síncrona | Automático
| Data center principal | SQL-2 | Secundário | Síncrona | Automático
| Centro de dados remotos ou secundários | SQL-3 | Secundário | Assíncrona | Manual


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Para obter mais informações sobre failover manual forçado e não planejado

Para obter mais informações, consulte estes tópicos:

- [Realize um failover manual planejado de um grupo de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Executar um failover manual forçado de um grupo de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Links adicionais

* [Grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx)
* [Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Balanceadores de carga azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Conjuntos de disponibilidade do Azure](../manage-availability.md)
