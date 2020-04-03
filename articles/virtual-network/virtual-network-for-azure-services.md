---
title: Rede virtual dos serviços do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre os benefícios da implantação de recursos em uma rede virtual. Recursos em redes virtuais podem se comunicar entre si e com recursos locais, sem que o tráfego atravesse a Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: b721857f2fa76dcee144521fb34b34ce48b7bd95
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616915"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para os serviços do Azure

A integração dos serviços do Azure a uma rede virtual do Azure permite o acesso privado ao serviço de máquinas virtuais ou de recursos de computação na rede virtual.
Você pode integrar serviços do Azure à sua rede virtual com as seguintes opções:
- Implantando instâncias dedicadas do serviço em uma rede virtual. Os serviços podem ser acessados de maneira privada dentro da rede virtual e de redes locais.
- Usando [o Private Link](../private-link/private-link-overview.md) para acessar privadamente uma instância específica do serviço a partir de sua rede virtual e de redes locais.

Você também pode acessar o serviço usando pontos finais públicos, estendendo uma rede virtual ao serviço, através [de pontos finais de serviço.](virtual-network-service-endpoints-overview.md) Os pontos finais do serviço permitem que os recursos de serviço sejam protegidos para a rede virtual.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Implantar os serviços do Azure em redes virtuais

Quando você implanta serviços dedicados do Azure em uma [rede virtual](virtual-networks-overview.md), é possível comunicar-se com os recursos de serviço de modo privado, por meio de endereços IP privados.

![Serviços implantados em uma rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Implantar serviços em uma rede virtual fornece as seguintes funcionalidades:

- Recursos dentro da rede virtual podem se comunicar uns com os outros de modo privado, por meio de endereços IP privados. Por exemplo, transferindo dados diretamente entre o HDInsight e o SQL Server em execução em uma máquina virtual, na rede virtual.
- Recursos locais podem acessar recursos em uma rede virtual usando endereços IP privados por um [VPN Site a Site (Gateway de VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Redes virtuais podem ser [emparelhadas](virtual-network-peering-overview.md) para permitir que recursos nas redes virtuais se comuniquem entre si usando endereços IP privados.
- As instâncias de serviço em uma rede virtual são normalmente gerenciadas totalmente pelo serviço Azure. Isso inclui monitorar a saúde dos recursos e dimensionar com carga.
- Instâncias de serviço são implantadas em uma sub-rede em uma rede virtual. O acesso de rede de entrada e saída para a sub-rede deve ser aberto através de grupos de segurança de [rede,](security-overview.md#network-security-groups)de acordo com a orientação fornecida pelo serviço.
- Certos serviços também impõem restrições à sub-rede em que são implantados, limitando a aplicação de políticas, rotas ou combinando VMs e recursos de serviço dentro da mesma sub-rede. Verifique com cada serviço as restrições específicas, pois elas podem mudar com o tempo. Exemplos desses serviços são Arquivos Azure NetApp, HSM Dedicado, Instâncias de Contêiner do Azure, Serviço de Aplicativo. 
- Opcionalmente, os serviços podem exigir uma [sub-rede delegada](virtual-network-manage-subnet.md#add-a-subnet) como um identificador explícito de que uma sub-rede pode hospedar um serviço específico. Ao delegar, os serviços obtêm permissões explícitas para criar recursos específicos de serviço na sub-rede delegada.
- Veja um exemplo de uma resposta da API REST em uma [rede virtual com uma sub-rede delegada](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Uma lista abrangente de serviços que estão usando o modelo de sub-rede delegada pode ser obtida através da API [de Delegações Disponíveis.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implantados em uma rede virtual

|Categoria|Serviço| Rede Dedicada¹
|-|-|-|
| Computação | Máquinas virtuais [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Serviço de nuvem](https://msdn.microsoft.com/library/azure/jj156091): apenas rede virtual (clássico)<br/> [Lote do Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Não <br/> Não <br/> Não <br/> No²
| Rede | [Gateway de Aplicativo – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Aparelhos virtuais de rede](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Sim <br/> Sim <br/> Sim <br/> Não
|Dados|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância gerenciada do banco de dados Azure SQL](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Sim <br/> Sim <br/> 
|Análise | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identidade | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Não <br/>
| Contêineres | [AKS (Serviço de Kubernetes do Azure)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância do contêiner Azure (ACI)](https://www.aka.ms/acivnet)<br/>[Mecanismo do Serviço de Contêiner do Azure](https://github.com/Azure/acs-engine) com o [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI de Rede Virtual do Microsoft Azure<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |No²<br/> Sim <br/><br/> Não <br/> Sim
| Web | [Gerenciamento de API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Aplicativos Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente de serviço de aplicativos](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/> Sim <br/> Sim
| Hospedado | [HSM Dedicado do Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Sim <br/> Sim <br/>
| | |

¹ 'Dedicado' implica que apenas recursos específicos de serviço podem ser implantados nesta sub-rede e não podem ser combinados com VM/VMSSs do cliente <br/> ² É recomendada como uma prática recomendada ter esses serviços em uma sub-rede dedicada, mas não uma exigência obrigatória imposta pelo serviço.
