---
title: Pontos de extremidade de serviço de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como habilitar o acesso direto aos recursos do Azure de uma rede virtual usando pontos de extremidade de serviço.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: b75e0c1e53f1e00579de73897197cdd2f14d79af
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455403"
---
# <a name="virtual-network-service-endpoints"></a>Pontos de extremidade de serviço de Rede Virtual

Os pontos finais de serviço da Virtual Network (VNet) ampliam o espaço de endereço privado da rede virtual. Os pontos finais também estendem a identidade do seu VNet para os serviços do Azure por uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure.

Este recurso está disponível para os seguintes serviços e regiões do Azure. O recurso *da Microsoft\* * está entre parênteses. Habilite esse recurso do lado da sub-rede enquanto configura pontos finais de serviço para o seu serviço:

**Disponível**

- **[Armazenamento Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage):* Geralmente disponível em todas as regiões do Azure.
- **[Banco de dados Azure SQL](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Geralmente disponível em todas as regiões do Azure.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Geralmente disponível em todas as regiões do Azure.
- **[Banco de dados Azure para servidor PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Geralmente disponível em regiões azure onde o serviço de banco de dados está disponível.
- **[Banco de dados Azure para servidor MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Geralmente disponível em regiões azure onde o serviço de banco de dados está disponível.
- **[Banco de dados Azure para MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Geralmente disponível em regiões azure onde o serviço de banco de dados está disponível.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Geralmente disponível em todas as regiões do Azure.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Geralmente disponível em todas as regiões do Azure.
- **[Ônibus de serviço Azure](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Geralmente disponível em todas as regiões do Azure.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Geralmente disponível em todas as regiões do Azure.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Geralmente disponível em todas as regiões do Azure onde o ADLS Gen1 está disponível.
- **[Serviço de aplicativo Azure](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)**: Geralmente disponível em todas as regiões do Azure onde o serviço de aplicativo está disponível.

**Visualização pública**

- **[Registro de contêineres do Azure](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Visualização disponível em todas as regiões do Azure onde o Registro de Contêineres do Azure está disponível.

Para obter as notificações mais atualizadas, consulte a página [de atualizações da Rede Virtual Do Azure.](https://azure.microsoft.com/updates/?product=virtual-network)

## <a name="key-benefits"></a>Principais benefícios

Os pontos de extremidade de serviço fornecem os seguintes benefícios:

- **Segurança aprimorada para os recursos de serviço do Azure**: os espaços de endereços privados VNet podem se sobrepor. Você não pode usar espaços sobrepostos para identificar exclusivamente o tráfego que se origina do seu VNet. Os pontos finais de serviço fornecem a capacidade de proteger os recursos de serviço do Azure à sua rede virtual, estendendo a identidade VNet ao serviço. Depois de habilitar pontos finais de serviço em sua rede virtual, você pode adicionar uma regra de rede virtual para proteger os recursos de serviço do Azure à sua rede virtual. A adição de regras proporciona maior segurança, removendo totalmente o acesso público à internet aos recursos e permitindo o tráfego apenas a partir de sua rede virtual.
- **Roteamento ideal para o tráfego de serviços do Azure a partir de sua rede virtual**: Hoje, quaisquer rotas em sua rede virtual que forcem o tráfego da internet para seus locais e/ou dispositivos virtuais também forçam o tráfego de serviços do Azure a tomar a mesma rota que o tráfego da internet. Os pontos de extremidade de serviço oferecem o roteamento ideal para o tráfego do Azure. 

  Os pontos de extremidade sempre usam o tráfego do serviço diretamente da sua rede virtual para o serviço na rede de backbone do Microsoft Azure. Manter o tráfego na rede do backbone do Azure permite que você continue auditando e monitorando o tráfego da Internet de saída das suas redes virtuais, por meio de túnel forçado, sem afetar o tráfego do serviço. Para obter mais informações sobre rotas definidas pelo usuário e túnel forçado, consulte [o roteamento de tráfego de rede virtual do Azure](virtual-networks-udr-overview.md).
- **Simples de configurar com menos sobrecarga de gerenciamento**: você não precisa mais de endereços IP públicos reservados em suas redes virtuais para proteger recursos do Azure através do firewall de IP. Não há nat (Network Address Translation, tradução de endereço de rede) ou dispositivos de gateway necessários para configurar os pontos finais do serviço. Você pode configurar pontos finais de serviço através de um simples clique em uma sub-rede. Não há sobrecarga adicional para manter os pontos finais.

## <a name="limitations"></a>Limitações

- O recurso está disponível apenas para redes virtuais implantadas usando o modelo de implantação do Azure Resource Manager.
- Os pontos de extremidade são habilitados nas sub-redes configuradas em redes virtuais do Azure. Os pontos finais não podem ser usados para tráfego de suas instalações para os serviços do Azure. Para obter mais informações, consulte [o acesso ao serviço Secure Azure no local](#secure-azure-services-to-virtual-networks)
- Para o SQL do Azure, um ponto de extremidade de serviço aplica-se somente ao tráfego de serviço do Azure dentro de uma região da rede virtual. Para o Armazenamento Azure, os pontos finais também se estendem para incluir regiões emparelhadas onde você implanta a rede virtual para suportar o tráfego DE GRS (GeingGe-Access Geo-RedundantStorage) e GrS (Geo-Redundant Storage, armazenamento georedundante). Para obter mais informações, consulte [Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Para o AdLS (ADLS) Azure Data Lake Storage (ADLS) Gen 1, o recurso de Integração VNet só está disponível para redes virtuais dentro da mesma região. Observe também que a integração de rede virtual para o ADLS Gen1 usa a segurança de ponto final de serviço de rede virtual entre sua rede virtual e o Azure Active Directory (Azure AD) para gerar reivindicações adicionais de segurança no token de acesso. Essas declarações, em seguida, são usadas para autenticar sua rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. A tag *Microsoft.AzureActiveDirectory* listada em serviços que suportam pontos finais de serviço é usada apenas para suportar pontos finais de serviço ao ADLS Gen 1. O Azure AD não suporta pontos finais de serviço nativamente. Para obter mais informações sobre a integração do Azure Data Lake Store Gen 1 VNet, consulte [segurança de rede no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Serviços seguros do Azure para redes virtuais

- Um ponto de extremidade de serviço de rede virtual fornece a identidade da sua rede virtual para o serviço do Azure. Depois de habilitar pontos finais de serviço em sua rede virtual, você pode adicionar uma regra de rede virtual para proteger os recursos de serviço do Azure à sua rede virtual.
- Atualmente, o tráfego do serviço do Azure de uma rede virtual usa endereços IP públicos como endereços IP de origem. No caso dos pontos de extremidade de serviço, o tráfego do serviço muda para o uso de endereços de rede virtual privados como endereços IP de origem na hora de acessar o serviço do Azure de uma rede virtual. Essa opção permite que você acesse os serviços sem a necessidade dos endereços IP públicos reservados usados nos firewalls de IP.

   >[!NOTE]
   > Com pontos de extremidade de serviço, os endereços IP de origem das máquinas virtuais na sub-rede de tráfego do serviço troca de usar endereços IPv4 públicos para usar endereços IPv4 privados. As regras de firewall existentes do serviço do Azure usando endereços IP públicos do Azure irão parar de funcionar com essa troca. Certifique-se de que as regras de firewall de serviço do Azure permitem essa troca antes de configurar os pontos de extremidade de serviço. Você também pode enfrentar interrupções temporárias no tráfego de serviço dessa sub-rede ao configurar pontos de extremidade de serviço. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Acesso ao serviço Secure Azure a partir de locais

  Por padrão, os recursos de serviço do Azure protegidos para redes virtuais não podem ser alcançados a partir de redes locais. Se você quiser permitir o tráfego do local, também deverá permitir endereços IP públicos (normalmente, NAT) do seu local ou ExpressRoute. Você pode adicionar esses endereços IP através da configuração de firewall IP para recursos de serviço do Azure.

  ExpressRoute: Se você estiver usando [o ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para peering público ou microsoft olhando a partir de suas instalações, você precisará identificar os endereços IP NAT que você está usando. Para peering público, cada circuito ExpressRoute usa dois endereços IP NAT, por padrão, aplicados ao tráfego de serviço do Azure quando o tráfego entra no backbone da rede Microsoft Azure. Para peering da Microsoft, os endereços IP NAT são fornecidos pelo cliente ou fornecidos pelo provedor de serviços.Para permitir o acesso aos recursos do serviço, você deve permitir estes endereços IP públicos na configuração do firewall de IP do recurso.Para localizar os endereços IP públicos do circuito do ExpressRoute de emparelhamento, [abra um tíquete de suporte com o ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) por meio do Portal do Azure. Para obter mais informações sobre o NAT para o público ExpressRoute e peering da Microsoft, consulte [os requisitos do ExpressRoute NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Proteção dos serviços do Azure em redes virtuais](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuração

- Configure pontos finais de serviço em uma sub-rede em uma rede virtual. Os pontos de extremidade funcionam com qualquer tipo de instâncias de computação em execução dentro dessa sub-rede.
- Você pode configurar vários pontos finais de serviço para todos os serviços Azure suportados (Azure Storage ou Azure SQL Database, por exemplo) em uma sub-rede.
- Para o Banco de Dados SQL do Azure, as redes virtuais devem estar na mesma região do recurso do serviço do Azure. Se estiver usando contas GRS e RA-GRS do Armazenamento do Azure, a conta primária deverá estar na mesma região que a rede virtual. Para todos os outros serviços, você pode garantir recursos de serviço do Azure para redes virtuais em qualquer região. 
- A rede virtual em que o ponto de extremidade está configurado pode estar na mesma assinatura que o recurso do serviço do Azure ou em assinatura diferente. Para obter mais informações sobre as permissões necessárias para configurar pontos de extremidade e garantir os serviços do Azure, confira [Provisionamento](#provisioning).
- Para os serviços compatíveis, você pode proteger recursos novos ou existentes em redes virtuais usando pontos de extremidade de serviço.

### <a name="considerations"></a>Considerações

- Depois de habilitar um ponto final de serviço, os endereços IP de origem de máquinas virtuais no switch de sub-rede. Os endereços IP de origem mudam de usar endereços IPv4 públicos para usar seu endereço IPv4 privado ao se comunicar com o serviço a partir dessa sub-rede. Eventuais conexões TCP abertas existentes para o serviço são fechadas durante essa mudança. Verifique se não há tarefas críticas em execução ao habilitar ou desabilitar um ponto de extremidade de serviço para um serviço que atende a uma sub-rede. Além disso, verifique se seus aplicativos podem se conectar automaticamente aos serviços do Azure após a mudança de endereço IP.

  A mudança de endereço IP só afeta o tráfego do serviço de sua rede virtual. Não há impacto em nenhum outro tráfego endereçado a ou a partir dos endereços IPv4 públicos atribuídos às suas máquinas virtuais. No caso dos serviços do Azure, se você tem regras de firewall existentes usando endereços IP públicos do Azure, essas regras param de funcionar com a mudança para endereços de rede virtual privados.
- Com os pontos finais de serviço, as entradas de DNS para serviços do Azure permanecem como estão hoje e continuam a ser resolvidas em endereços IP públicos atribuídos ao serviço Azure.

- NSGs (grupos de segurança de rede) com pontos de extremidade de serviço:
  - Por padrão, os NSGs permitem tráfego de internet de saída e também permitem o tráfego de seus serviços VNet para Azure. Esse tráfego continua a funcionar com pontos finais de serviço como está. 
  - Se você quiser negar todo o tráfego de saída da Internet e permitir apenas tráfego para serviços específicos do Azure, você pode fazê-lo usando [tags de serviço](security-overview.md#service-tags) em seus NSGs. Você pode especificar os serviços suportados do Azure como destino em suas regras do NSG e o Azure também fornece a manutenção de endereços IP subjacentes a cada tag. Para saber mais, confira [Marcas do Serviço do Azure para NSGs.](security-overview.md#service-tags) 

### <a name="scenarios"></a>Cenários

- **Redes virtuais emparelhadas, conectadas ou múltiplas**: para proteger os serviços do Azure em várias sub-redes em uma rede virtual ou em várias redes virtuais, você pode habilitar pontos de extremidade de serviço em cada uma das sub-redes independentemente e proteger os recursos do serviço do Azure em todas elas.
- **Filtrando o tráfego de saída de uma rede virtual para os serviços do Azure**: Se você quiser inspecionar ou filtrar o tráfego enviado para um serviço Azure a partir de uma rede virtual, você pode implantar um dispositivo virtual de rede dentro da rede virtual. Você pode aplicar os pontos de extremidade de serviço à sub-rede na qual o dispositivo de rede virtual está implantado e proteger os recursos do serviço do Azure apenas nessa sub-rede. Esse cenário pode ser útil se você quiser usar a filtragem de aparelhos virtuais de rede para restringir o acesso ao serviço do Azure da sua rede virtual apenas a recursos específicos do Azure. Para obter mais informações, consulte [Saída com dispositivos de rede virtual](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Garantir os recursos do Azure para serviços implantados diretamente em redes virtuais**: Você pode implantar diretamente vários serviços do Azure em sub-redes específicas em uma rede virtual. Você pode proteger recursos do serviço do Azure em sub-redes de [serviço gerenciado](virtual-network-for-azure-services.md) configurando um ponto de extremidade de serviço na sub-rede de serviço gerenciado.
- **Tráfego de disco de uma máquina virtual Do Azure**: O tráfego de disco da máquina virtual para discos gerenciados e não gerenciados não é afetado pelas alterações de roteamento de pontos finais de serviço para o Armazenamento Azure. Este tráfego inclui diskIO, bem como montagem e desmontagem. Você pode limitar o acesso REST a blobs de página para selecionar redes através de pontos finais de serviço e [regras de rede de armazenamento do Azure](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Registro em log e solução de problemas

Depois de configurar os pontos finais do serviço em um serviço específico, valide se a rota de ponto final do serviço está em vigor por: 
 
- Validação do endereço IP de origem de qualquer solicitação de serviço no diagnóstico de serviço. Todas as novas solicitações com pontos de extremidade de serviço mostram o endereço IP de origem para a solicitação como o endereço IP privado de rede virtual atribuído ao cliente que fez a solicitação da sua rede virtual. Sem o ponto de extremidade, esse endereço é um endereço IP público do Azure.
- Exibindo as rotas efetivas em qualquer adaptador de rede em uma sub-rede. A rota para o serviço:
  - Mostra uma rota padrão mais específica para intervalos de prefixo de endereço de cada serviço
  - Tem um nextHopType como *VirtualNetworkServiceEndpoint*
  - Indica que uma conexão mais direta com o serviço está em vigor em comparação com quaisquer rotas de túnel forçado

>[!NOTE]
> Rotas de ponto de extremidade de serviço substituem quaisquer rotas BGP ou UDR na correspondência do prefixo de endereço de um serviço do Azure. Para obter mais informações, consulte [solução de problemas com rotas eficazes](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Provisionamento

Os pontos finais do serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso à gravação a uma rede virtual. Para proteger os recursos de serviço do Azure para um VNet, o usuário deve ter permissão para *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* para as sub-redes adicionadas. As funções de administrador de serviço incorporada incluem essa permissão por padrão. Você pode modificar a permissão criando funções personalizadas.

Para obter mais informações sobre funções incorporadas, consulte [Funções incorporadas para recursos do Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obter mais informações sobre como atribuir permissões específicas a funções personalizadas, consulte [Funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço da rede virtual e do Azure estão em assinaturas diferentes, os recursos devem estar no mesmo locatário do Active Directory (AD). 

## <a name="pricing-and-limits"></a>Preços e limites

Não há cobrança adicional pelo uso de pontos finais de serviço. O modelo atual de preços para serviços do Azure (Azure Storage, Azure SQL Database, etc.) se aplica como está hoje.

Não há limite para o número total de pontos finais de serviço em uma rede virtual.

Certos serviços do Azure, como contas de armazenamento do Azure, podem impor limites ao número de sub-redes usadas para proteger o recurso. Consulte a documentação de vários serviços na seção [Próximas etapas](#next-steps) para obter detalhes.

## <a name="vnet-service-endpoint-policies"></a>Políticas de ponto final de serviço do VNet 

As políticas de ponto final do serviço VNet permitem filtrar o tráfego de rede virtual para os serviços do Azure. Este filtro permite apenas recursos específicos de serviço do Azure em pontos finais de serviço. As políticas de ponto de extremidade de serviço fornecem controle de acesso granular para tráfego de rede virtual para serviços do Azure. Para obter mais informações, consulte [Políticas de ponto final de serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Perguntas frequentes

Para perguntas frequentes, consulte [perguntas frequentes sobre](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)o ponto final do serviço de rede virtual .

## <a name="next-steps"></a>Próximas etapas

- [Configurar pontos de extremidade de serviço de rede virtual](tutorial-restrict-network-access-to-resources.md)
- [Proteja uma conta do Azure Storage para uma rede virtual](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteja um banco de dados SQL do Azure para uma rede virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Proteja um Data Warehouse SQL do Azure para uma rede virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integração de serviços do Azure em redes virtuais](virtual-network-for-azure-services.md)
- [Políticas de Ponto de Extremidade de Serviço de Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

