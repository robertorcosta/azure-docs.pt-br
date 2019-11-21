---
title: Arquitetura de rede do SAP HANA do Azure (Instâncias Grandes) | Microsoft Docs
description: arquitetura de rede para implantar o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3777180a4d62f8b253ac4cd096bff15613f33565
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206615"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Arquitetura de rede do SAP HANA (Instâncias Grandes)

A arquitetura dos serviços de rede do Azure é um componente essencial da implantação com êxito dos aplicativos do SAP HANA em Instâncias Grandes. Normalmente, as implantações do SAP HANA no Azure (Instâncias Grandes) têm uma estrutura SAP maior com diversas soluções SAP com vários tamanhos de bancos de dados, consumo de recursos de CPU e a utilização de memória. It's likely that not all IT systems are located in Azure already. Your SAP landscape is often hybrid as well from a DBMS point and SAP application point of view using a mixture of NetWeaver, and S/4HANA and SAP HANA and other DBMS. Azure offers different services that allow you to run the different DBMS, NetWeaver, and S/4HANA systems in Azure. Azure also offers you network technology to make Azure look like a virtual data center to your on-premises software deployments

Unless your complete IT systems are hosted in Azure. Azure networking functionality is used to connect the on-premises world with your Azure assets to make Azure look like a virtual datacenter of yours. The Azure network functionality used is: 

- Azure virtual networks are connected to the [ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuit that connects to your on-premises network assets.
- An ExpressRoute circuit that connects on-premises to Azure should have a minimum bandwidth of [1 Gbps or higher](https://azure.microsoft.com/pricing/details/expressroute/). Essa largura de banda mínima permite largura de banda adequada para a transferência de dados entre sistemas locais e sistemas em execução nas VMs. Também permite largura de banda adequada para conexão a sistemas do Azure a partir de usuários locais.
- All SAP systems in Azure are set up in virtual networks to communicate with each other.
- Active Directory and DNS hosted on-premises are extended into Azure through ExpressRoute from on-premises, or are running complete in Azure.

For the specific case of integrating HANA Large Instances into the Azure data center network fabric, Azure ExpressRoute technology is used as well


> [!NOTE] 
> Only one Azure subscription can be linked to only one tenant in a HANA Large Instance stamp in a specific Azure region. Conversely, a single HANA Large Instance stamp tenant can be linked to only one Azure subscription. Esse requisito é consistente com outros objetos cobráveis no Azure.

If SAP HANA on Azure (Large Instances) is deployed in multiple different Azure regions, a separate tenant is deployed in the HANA Large Instance stamp. É possível executar ambos sob a mesma assinatura do Azure, desde que essas instâncias façam parte da mesma paisagem do SAP. 

> [!IMPORTANT] 
> Only the Azure Resource Manager deployment method is supported with SAP HANA on Azure (Large Instances).

 

## <a name="additional-virtual-network-information"></a>Informações adicionais sobre rede virtual

To connect a virtual network to ExpressRoute, an Azure ExpressRoute gateway must be created. For more information, see [About Expressroute gateways for ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

An Azure ExpressRoute gateway is used with ExpressRoute to an infrastructure outside of Azure or to an Azure Large Instance stamp. You can connect the Azure ExpressRoute gateway to a maximum of four different ExpressRoute circuits as long as those connections come from different Microsoft enterprise edge routers. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> The maximum throughput you can achieve with a ExpressRoute gateway is 10 Gbps by using an ExpressRoute connection. A cópia de arquivos entre uma VM que reside em uma rede virtual e um sistema local (como um único fluxo de cópia) não alcança a taxa de transferência total as diferentes SKUs do gateway. To leverage the complete bandwidth of the ExpressRoute gateway, use multiple streams. Ou, será necessário copiar arquivos diferentes em fluxos paralelos de um único arquivo.


## <a name="networking-architecture-for-hana-large-instance"></a>Arquitetura de rede para SAP HANA em Instâncias Grandes
A arquitetura de rede para o SAP HANA em Instâncias Grandes pode ser separada em quatro partes diferentes:

- Rede local e conexão do ExpressRoute para o Azure. Essa parte é o domínio do cliente e é conectada ao Azure por meio do ExpressRoute. This Expressroute circuit is fully paid by you as a customer. The bandwidth should be large enough to handle the network traffic between your on-premises assets and the Azure region you are connecting against. Observe o canto inferior direito na figura a seguir.
- Azure network services, as previously discussed, with virtual networks, which again need ExpressRoute gateways added. Essa parte é uma área na qual você precisa localizar os projetos apropriados para os requisitos do aplicativo, segurança e requisitos de conformidade. Se você usa o SAP HANA em Instâncias Grandes, é outro ponto a ser considerado em termos do número de redes virtuais e SKUs do gateway do Azure a escolher. Observe a parte superior direita na figura.
- Conectividade do SAP HANA em Instâncias Grandes através da tecnologia do ExpressRoute no Azure. Essa parte é implantada e feita pela Microsoft. Tudo o que você precisa fazer é fornecer alguns intervalos de endereço IP após a implantação dos ativos no SAP HANA em Instâncias Grandes conectar o circuito do ExpressRoute às redes virtuais. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). There is no additional fee for you as a customer for the connectivity between the Azure data center network fabric and HANA Large Instance units.
- Networking within the HANA Large Instance stamp, which is mostly transparent for you.

![Rede virtual conectada ao SAP HANA do Azure (Instâncias Grandes) e no local](./media/hana-overview-architecture/image1-architecture.png)

O requisito de que os ativos locais devem conectar-se por meio do ExpressRoute para o Azure não altera porque você utiliza o SAP HANA em Instâncias Grandes. O requisito de ter uma ou várias redes virtuais executando as VMs, que hospedam a camada de aplicativo que conecta-se às instâncias do HANA hospedadas nas unidades do SAP HANA em Instâncias Grandes, também não é alterado. 

As diferenças para implantações do SAP no Azure são:

- As unidades do SAP HANA em Instâncias Grandes do locatário do cliente são conectadas por meio de outro circuito do ExpressRoute às redes virtuais. To separate load conditions, the on-premises to Azure virtual network ExpressRoute circuits and the circuits between Azure virtual networks and HANA Large Instances don't share the same routers.
- O perfil de carga de trabalho entre a camada de aplicativo do SAP e o SAP HANA em Instâncias Grandes é de natureza diferente, com muitas solicitações e intermitências pequenas, como transferências de dados (conjuntos de resultados) do SAP HANA para a camada de aplicativo.
- A arquitetura de aplicativos do SAP é mais sensível à latência de rede que os cenários típicos em que os dados são trocados entre o local e o Azure.
- The Azure ExpressRoute gateway has at least two ExpressRoute connections. One circuit that is connected from on-premises and one that is connected from HANA Large Instances. This leaves only room for another two additional circuits from different MSEEs to connect to on ExpressRoute Gateway. This restriction is independent of the usage of ExpressRoute Fast Path. All the connected circuits share the maximum bandwidth for incoming data of the ExpressRoute gateway.

With Revision 3 of HANA Large Instance stamps, the network latency experienced between VMs and HANA Large Instance units can be higher than a typical VM-to-VM network round-trip latency. Dependendo da região do Azure, os valores medidos podem exceder a latência de viagem de ida e volta de 0,7 ms, classificada como abaixo da média na [Nota SAP 1100926 – Perguntas frequentes: desempenho de rede](https://launchpad.support.sap.com/#/notes/1100926/E). Dependendo da ferramenta e da região do Azure para medir a latência da viagem de ida e volta da rede entre uma VM do Azure e a unidade de Instância Grande do HANA, a latência medida pode ser até cerca de 2 milissegundos. No entanto, os clientes implantam aplicativos do SAP de produção baseados no SAP HANA com êxito no SAP HANA em Instâncias Grandes. Certifique-se de testar os processos empresariais completamente no SAP HANA em Instâncias Grandes do Azure. A new functionality, called ExpressRoute Fast Path, is able to reduce the network latency between HANA Large Instances and application layer VMs in Azure substantially (see below). 

With Revision 4 of HANA Large Instance stamps, the network latency between Azure VMs that are deployed in proximity to the HANA Large Instance stamp, is experienced to meet the average or better than average classification as documented in [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) if Azure ExpressRoute Fast Path is configured (see below). In order to deploy Azure VMs in close proximity to HANA Large Instance units of Revision 4, you need to leverage [Azure Proximity Placement Groups](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). The way how proximity placement groups can be used to locate the SAP application layer in the same Azure datacenter as Revision 4 hosted HANA Large Instance units is described in [Azure Proximity Placement Groups for optimal network latency with SAP applications](sap-proximity-placement-scenarios.md).

To provide deterministic network latency between VMs and HANA Large Instance, the choice of the ExpressRoute gateway SKU is essential. Diferentemente dos padrões de tráfego entre VMs locais e VMs, o padrão de tráfego entre as VMs e o SAP HANA em Instâncias Grandes pode desenvolver pequenas, mas elevadas intermitências de solicitações e volumes de dados a serem transmitidos. Para lidar bem com essas intermitências, é recomendável usar a SKU do gateway UltraPerformance. For the Type II class of HANA Large Instance SKUs, the use of the UltraPerformance gateway SKU as a ExpressRotue gateway is mandatory.

> [!IMPORTANT] 
> Dado o tráfego geral entre o aplicativo do SAP e as camadas do banco de dados, somente as SKUs do gateway HighPerformance ou UltraPerformance para redes virtuais têm suporte para conexão com o SAP HANA do Azure (Instâncias Grandes). For HANA Large Instance Type II SKUs, only the UltraPerformance gateway SKU is supported as a ExpressRoute gateway. Exceptions apply when using ExpressRoute Fast Path (see below)

### <a name="expressroute-fast-path"></a>ExpressRoute Fast Path
To lower the latency, ExpressRoute Fast Path got introduced and released in May 2019 for the specific connectivity of HANA Large Instances to Azure virtual networks that host the SAP application VMs. The major difference to the solution rolled out so far, is, that the data flows between VMs and HANA Large Instances are not routed through the ExpressRoute gateway anymore. Instead the VMs assigned in the subnet(s) of the Azure virtual network are directly communicating with the dedicated enterprise edge router. 

> [!IMPORTANT] 
> The ExpressRoute Fast Path functionality requires that the subnets running the SAP application VMs are in the same Azure virtual network that got connected to the HANA Large Instances. VMs located in Azure virtual networks that are peered with the Azure virtual network connected directly to the HANA Large Instance units are not benefiting from ExpressRoute Fast Path. As a result typical hub and spoke virtual network designs, where the ExpressRoute circuits are connecting against a hub virtual network and virtual networks containing the SAP application layer (spokes) are getting peered, the optimization by ExpressRoute Fast Path will not work. In addtion, ExpressRoute Fast Path does not support user defined routing rules (UDR) today. For more information, see [ExpressRoute virtual network gateway and FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


For more details on how to configure ExpressRoute Fast Path, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> An UltraPerformance ExpressRoute gateway is required to have ExpressRoute Fast Path working


## <a name="single-sap-system"></a>Único sistema SAP

A infraestrutura local mostrada anteriormente é conectada por meio do ExpressRoute no Azure. The ExpressRoute circuit connects into a Microsoft enterprise edge router (MSEE). Para obter mais informações, consulte [Visão geral técnica do ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). After the route is established, it connects into the Azure backbone.

> [!NOTE] 
> Para executar paisagens do SAP no Azure, conecte o roteador de borda corporativa mais próximo da região do Azure na paisagem do SAP. HANA Large Instance stamps are connected through dedicated enterprise edge router devices to minimize network latency between VMs in Azure IaaS and HANA Large Instance stamps.

The ExpressRoute gateway for the VMs that host SAP application instances are connected to one ExpressRoute circuit that connects to on-premises. A mesma rede virtual é conectada a um roteador de borda corporativa separado dedicado para conectar os carimbos da Instância Grande. Using ExpressRoute Fast Path, the data flow from HANA Large Instances to the SAP application layer VMs are not routed through the ExpressRoute gateway anymore and with that reduce the network round-trip latency.

Esse sistema é um exemplo direto de um único sistema SAP. A camada de aplicativo do SAP é hospedada no Azure. O banco de dados do SAP HANA é executado no SAP HANA do Azure (Instâncias Grandes). The assumption is that the ExpressRoute gateway bandwidth of 2-Gbps or 10-Gbps throughput doesn't represent a bottleneck.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Vários sistemas SAP ou grandes sistemas SAP

If multiple SAP systems or large SAP systems are deployed to connect to SAP HANA on Azure (Large Instances), the throughput of the ExpressRoute gateway might become a bottleneck. Or you want to isolate production and non-production systems in different Azure virtual networks. Nesse caso, divida as camadas do aplicativo em várias redes virtuais. Também é possível criar uma rede virtual especial que conecta-se ao SAP HANA em Instâncias Grandes para casos como:

- Execução de backups diretamente das instâncias do HANA na Instância Grande do HANA para uma VM no Azure que hospeda compartilhamentos NFS.
- Cópia de backups grandes ou de outros arquivos de unidades de Instância Grande do HANA com o espaço em disco gerenciado no Azure.

Use a separate virtual network to host VMs that manage storage for mass transfer of data between HANA Large Instances and Azure. This arrangement avoids the effects of large file or data transfer from HANA Large Instance to Azure on the ExpressRoute gateway that serves the VMs that run the SAP application layer. 

Uma arquitetura de rede mais escalonável:

- Aproveite várias redes virtuais para uma única camada de aplicativo do SAP maior.
- Implante uma rede virtual separada para cada sistema SAP implantado, em comparação à combinação desses sistemas SAP em sub-redes separadas sob a mesma rede virtual.

  Uma arquitetura de rede mais escalonável para o SAP HANA no Azure (Instâncias Grandes):

![Implantar camada de aplicativo do SAP em várias redes virtuais](./media/hana-overview-architecture/image4-networking-architecture.png)

Dependent on the rules and restrictions, you want to apply between the different virtual networks hosting VMs of different SAP systems, you should peer those virtual networks. Para obter mais informações sobre o emparelhamento de rede virtual, consulte [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Roteamento no Azure

By default deployment, three network routing considerations are important for SAP HANA on Azure (Large Instances):

* SAP HANA on Azure (Large Instances) can be accessed only through Azure VMs and the dedicated ExpressRoute connection, not directly from on-premises. O acesso direto do local para as unidades do SAP HANA em Instâncias Grandes, conforme fornecido pela Microsoft a você, não é possível imediatamente. As restrições de roteamento transitivas devem-se à arquitetura de rede atual do Azure usada para o SAP HANA em Instâncias Grandes. Alguns clientes de administração e quaisquer aplicativos que precisam de acesso direto, como o SAP Solution Manager em execução no local, não podem conectar ao banco de dados do SAP HANA. For exceptions check the section 'Direct Routing to HANA Large Instances'.

* If you have HANA Large Instance units deployed in two different Azure regions for disaster recovery, the same transient routing restrictions applied in the past. In other words, IP addresses of a HANA Large Instance unit in one region (for example, US West) were not routed to a HANA Large Instance unit deployed in another region (for example, US East). This restriction was independent of the use of Azure network peering across regions or cross-connecting the ExpressRoute circuits that connect HANA Large Instance units to virtual networks. Para uma representação gráfica, consulte a figura na seção "Usar unidades do SAP HANA em Instâncias Grandes em várias regiões". This restriction, which came with the deployed architecture, prohibited the immediate use of HANA System Replication as disaster recovery functionality. For recent changes, look up the section 'Use HANA Large Instance units in multiple regions'. 

* SAP HANA on Azure (Large Instances) units have an assigned IP address from the server IP pool address range that you submitted when requesting the HANA Large Instance deployment. Para obter mais informações, consulte [Infraestrutura e conectividade do SAP HANA (Instâncias Grandes) do Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). This IP address is accessible through the Azure subscriptions and circuit  that connects Azure virtual networks to HANA Large Instances. O endereço IP atribuído fora desse intervalo de endereços do pool de IP do servidor é atribuído diretamente à unidade de hardware. Ele*não* é atribuído pelo NAT, como foi o caso nas primeiras implantações dessa solução. 

### <a name="direct-routing-to-hana-large-instances"></a>Direct Routing to HANA Large Instances

By default, the transitive routing does not work in these scenarios:

* Between HANA Large Instance units and an on-premises deployment.

* Between HANA Large Instance routing that are deployed in two different regions.

There are three ways to enable transitive routing in those scenarios:

- Um proxy reverso para rotear dados, de e para. For example, F5 BIG-IP, NGINX with Traffic Manager deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises as a virtual firewall/traffic routing solution.
- Usar [regras IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) em uma VM Linux para habilitar o roteamento entre localidades locais e as unidades do SAP HANA em Instâncias Grandes ou entre unidades do SAP HANA em Instâncias Grandes em regiões diferentes. The VM running IPTables needs to be deployed in the Azure virtual network that connects to HANA Large Instances and to on-premises. The VM needs to be sized accordingly, so, that the network throughput of the VM is sufficient for the expected network traffic. For details on VM network bandwidth, check the article [Sizes of Linux virtual machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) would be another solution to enable direct traffic between on-premises and HANA Large instance units. 

All the traffic of these solutions would be routed through an Azure virtual network and as such the traffic could be additionally restricted by the soft appliances used or by Azure Network Security Groups, so, that certain IP addresses or IP address ranges from on-premises could be blocked or explicitly allowed accessing HANA Large Instances. 

> [!NOTE]  
> Esteja ciente de que a implementação e o suporte para soluções personalizadas envolvendo IPTables ou dispositivos de rede de terceiros não são fornecidos pela Microsoft. O suporte deve ser fornecido pelo fornecedor do componente usado ou pelo integrador. 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft introduced a new functionality called [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global Reach can be used for HANA Large Instances in two scenarios:

- Enable direct access from on-premises to your HANA Large Instance units deployed in different regions
- Enable direct communication between your HANA Large Instance units deployed in different regions


##### <a name="direct-access-from-on-premises"></a>Direct Access from on-premises
In the Azure regions where Global Reach is offered, you can request enabling the Global Reach functionality for your ExpressRoute circuit that connects your on-premises network to the Azure virtual network that connects to your HANA Large Instance units as well. There are some cost implications for the on-premises side of your ExpressRoute circuit. For prices, check the prices for [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). There are no additional costs for you related to the circuit that connects the HANA Large Instance unit(s) to Azure. 

> [!IMPORTANT]  
> In case of using Global Reach for enabling direct access between your HANA Large Instance units and on-premises assets, the network data and control flow is **not routed through Azure virtual networks**, but directly between the Microsoft enterprise exchange routers. As a result any NSG or ASG rules, or any type of firewall, NVA, or proxy you deployed in an Azure virtual network, are not getting touched. **If you use ExpressRoute Global Reach to enable direct access from on-premises to HANA Large instance units restrictions and permissions to access HANA large Instance units need to be defined in firewalls on the on-premises side** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connecting HANA Large Instances in different Azure regions
In the same way, as ExpressRoute Global Reach can be used for connecting on-premises to HANA Large Instance units, it can be used to connect tow HANA Large Instance tenants that are deployed for you in two different regions. The isolation is the ExpressRoute circuits that your HANA Large Instance tenants are using to connect to Azure in both regions. There are no additional charges for connecting two HANA Large Instance tenants that are deployed in two different regions. 

> [!IMPORTANT]  
> The data flow and control flow of the network traffic between the different HANA Large instance tenants will not be routed through azure networks. As a result you can't use Azure functionality or NVAs to enforce communication restrictions between your two HANA Large Instances tenants. 

For more details on how to get ExpressRoute Global Reach enabled, read the document [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Conectividade com a Internet do SAP HANA em Instâncias Grandes
O SAP HANA em Instâncias Grandes *não* tem conectividade direta com a Internet. Por exemplo, essa limitação pode restringir a capacidade de registrar a imagem do SO diretamente com o fornecedor do SO. Talvez seja necessário trabalhar com o servidor da Ferramenta de Gerenciamento de Assinaturas do SUSE Linux Enterprise Server ou com o Gerenciador de Assinaturas do Red Hat Enterprise Linux.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Criptografia de dados entre VMs e o SAP HANA em Instâncias Grandes
Os dados transferidos entre o SAP HANA em Instâncias Grandes e as VMs não são criptografados. No entanto, apenas para a troca entre o lado do DBMS do HANA e aplicativos baseados em JDBC/ODBC, é possível habilitar criptografia do tráfego. Para obter mais informações, consulta [esta documentação pelo SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usar unidades do SAP HANA em Instâncias Grandes em várias regiões

To realize disaster recovery set ups, you need to have SHANA Large Instance units in multiple Azure regions. Even with using Azure [Global Vnet Peering], the transitive routing by default is not working between HANA Large Instance tenants in two different regions. However, Global Reach opens up the communication path between the HANA Large Instance units you have provisioned in two different regions. This usage scenario of ExpressRoute Global Reach enables:

 - HANA System Replication without any additional proxies or firewalls
 - Copying backups between HANA Large Instance units in two different regions to perform system copies or system refreshes


![Rede virtual conectada a carimbos da Instância Grande do Azure em diferentes regiões do Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

The figure shows how the different virtual networks in both regions are connected to two different ExpressRoute circuits that are used to connect to SAP HANA on Azure (Large Instances) in both Azure regions (grey lines). Reason for this two cross connections is to protect from an outage of the MSEEs on either side. The communication flow between the two virtual networks in the two Azure regions is supposed to be handled over the [global peering](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) of the two virtual networks in the two different regions (blue dotted line). The thick red line describes the ExpressRoute Global Reach connection, which allows the HANA Large Instance units of your tenants in two different regions to communicate with each other. 

> [!IMPORTANT] 
> Se foram utilizados vários circuitos do ExpressRoute, as configurações de prefixação do AS Path e de BGP de Preferência Local deverão ser usadas para garantir o roteamento apropriado do tráfego.

**Próximas etapas**
- Confira [Arquitetura de armazenamento do SAP HANA (Instâncias Grandes)](hana-storage-architecture.md)
