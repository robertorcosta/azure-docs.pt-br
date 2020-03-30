---
title: Diretrizes para o planejamento da rede Azure NetApp Files | Microsoft Docs
description: Descreve diretrizes que podem ajudá-lo a projetar uma arquitetura de rede eficaz usando arquivos do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 8e6a1c3472c6b20b27cf181edbeeb96ab71eb58d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242485"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Diretrizes para planejamento de rede do Azure NetApp Files

O planejamento de arquitetura de rede é um elemento-chave para projetar qualquer infra-estrutura de aplicativos. Este artigo ajuda você a projetar uma arquitetura de rede eficaz para suas cargas de trabalho para se beneficiar dos recursos ricos do Azure NetApp Files.

Os volumes de arquivos do Azure NetApp foram projetados para serem contidos em uma sub-rede de propósito especial chamada [uma sub-rede delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) dentro de sua Rede Virtual Azure. Portanto, você pode acessar os volumes diretamente do seu VNet, de VNets peered na mesma região ou de locais on-premises através de um Virtual Network Gateway (ExpressRoute ou VPN Gateway) conforme necessário. A sub-rede é dedicada aos Arquivos Azure NetApp e não há conectividade com outros serviços do Azure ou com a Internet.

## <a name="considerations"></a>Considerações  

Você deve entender algumas considerações quando planeja a rede Azure NetApp Files.

### <a name="constraints"></a>Restrições

Os recursos abaixo não têm suporte para arquivos do Azure NetApp: 

* Grupos de segurança de rede (NSGs) aplicados à sub-rede delegada
* Rotas definidas pelo usuário (UDRs) com prefixo de endereço como sub-rede de arquivos Azure NetApp
* Políticas do Azure (por exemplo, políticas de nomeação personalizadas) na interface Arquivos do Azure NetApp
* Balanceadores de carga para tráfego de arquivos do Azure NetApp

As seguintes restrições de rede se aplicam aos arquivos do Azure NetApp:

* O número de IPs em uso em um VNet com Arquivos Azure NetApp (incluindo VNets peered) não pode exceder 1000. Estamos trabalhando para aumentar esse limite para atender às demandas da escala do cliente. Nesse ínterim, se você precisar de mais IPs, entre em contato com nossa equipe de suporte com seu estojo de uso e limite necessário.
* Em cada VNet (Rede virtual do Azure), apenas uma sub-rede pode ser delegada para o Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologias de rede com suporte

A tabela a seguir descreve as topologias de rede suportadas pelo Azure NetApp Files.  Também descreve as soluçãos para as topologias sem suporte. 

|    Topologias    |    É suportado    |     Solução alternativa    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Conectividade ao volume em um VNet local    |    Sim    |         |
|    Conectividade ao volume em um VNet peered (Mesma região)    |    Sim    |         |
|    Conectividade ao volume em um VNet peered (região cruzada ou peering global)    |    Não    |    Nenhum    |
|    Conectividade a um volume no gateway ExpressRoute    |    Sim    |         |
|    Conectividade de on-premises para um volume em um VNet falado sobre o gateway ExpressRoute e VNet peering com o gateway transit    |    Sim    |        |
|    Conectividade de on-premises para um volume em um VNet falado sobre gateway VPN    |    Sim    |         |
|    Conectividade de on-premises para um volume em um VNet falado sobre gateway VPN e VNet peering com trânsito de gateway    |    Sim    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rede virtual para volumes de arquivos do Azure NetApp

Esta seção explica conceitos que ajudam no planejamento de rede virtual.

### <a name="azure-virtual-networks"></a>Redes virtuais do Azure

Antes de provisionar um volume de Arquivos Do Azure NetApp, você precisa criar uma rede virtual Do Zure (VNet) ou usar uma que já existe em sua assinatura. O VNet define o limite de rede do volume.  Para obter mais informações sobre a criação de redes virtuais, consulte a [documentação da Rede Virtual Do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Sub-redes

As sub-redes segmentam a rede virtual em espaços de endereço separados que são utilizáveis pelos recursos do Azure neles.  Os volumes de arquivos do Azure NetApp estão contidos em uma sub-rede de propósito especial chamada [sub-rede delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

A delegação da sub-rede fornece permissões explícitas ao serviço Azure NetApp Files para criar recursos específicos de serviço na sub-rede.  Ele usa um identificador exclusivo na implantação do serviço. Neste caso, uma interface de rede é criada para permitir a conectividade aos Arquivos Do Azure NetApp.

Se você usar um novo VNet, você pode criar uma sub-rede e delegar a sub-rede para arquivos do Azure NetApp seguindo instruções em [Delegar uma sub-rede para Arquivos Do Azure NetApp](azure-netapp-files-delegate-subnet.md). Você também pode delegar uma sub-rede vazia existente que ainda não está delegada a outros serviços.

Se o VNet estiver associado a outro VNet, não será possível expandir o espaço de endereço VNet. Por essa razão, a nova sub-rede delegada precisa ser criada dentro do espaço de endereço VNet. Se você precisar estender o espaço de endereço, você deve excluir o peering Do VNet antes de expandir o espaço de endereço.

### <a name="udrs-and-nsgs"></a>UDRs e NSGs

As rotas definidas pelo usuário (UDRs) e os NSGs (Network security groups) não são suportadas em sub-redes delegadas para arquivos Do Azure NetApp.

Como solução, você pode aplicar NSGs a outras sub-redes que permitem ou negam o tráfego de e para a sub-rede delegada Azure NetApp Files.  

## <a name="azure-native-environments"></a>Ambientes nativos do Azure

O diagrama a seguir ilustra um ambiente nativo do Azure:

![Ambiente de rede nativo do Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Local VNet

Um cenário básico é criar ou conectar-se a um volume de Arquivos Do Azure NetApp a partir de uma máquina virtual (VM) no mesmo VNet. Para VNet 2 no diagrama acima, o Volume 1 é criado em uma sub-rede delegada e pode ser montado na VM 1 na sub-rede padrão.

### <a name="vnet-peering"></a>Emparelhamento VNet

Se você tiver VNets adicionais na mesma região que precisam de acesso aos recursos uns dos outros, os VNets podem ser conectados usando [o peering VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) para permitir conectividade segura através da infra-estrutura do Azure. 

Considere o VNet 2 e o VNet 3 no diagrama acima. Se o VM 1 precisar se conectar à VM 2 ou ao Volume 2, ou se a VM 2 precisar se conectar ao VM 1 ou ao Volume 1, então você precisa ativar o vnet peering entre o VNet 2 e o VNet 3. 

Além disso, considere um cenário onde o VNet 1 é associado ao VNet 2, e o VNet 2 é associado com o VNet 3 na mesma região. Os recursos do VNet 1 podem se conectar aos recursos no VNet 2, mas não podem se conectar a recursos no VNet 3, a menos que o VNet 1 e o VNet 3 sejam suportados. 

No diagrama acima, embora o VM 3 possa se conectar ao Volume 1, o VM 4 não pode se conectar ao Volume 2.  A razão para isso é que os VNets falados não são peered, e _o roteamento de trânsito não é suportado por peering VNet_.

## <a name="hybrid-environments"></a>Ambientes híbridos

O diagrama a seguir ilustra um ambiente híbrido: 

![Ambiente de rede híbrida](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

No cenário híbrido, os aplicativos de datacenters locais precisam ter acesso aos recursos do Azure.  Este é o caso se você deseja estender seu datacenter para o Azure, ou se você quer usar serviços nativos do Azure ou para recuperação de desastres. Consulte [opções de planejamento do VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) para obter informações sobre como conectar vários recursos no local aos recursos no Azure através de uma VPN local-para-site ou de uma ExpressRoute.

Em uma topologia híbrida com fala de hub, o hub VNet no Azure atua como um ponto central de conectividade à sua rede local. Os raios são VNets olhados com o hub, e eles podem ser usados para isolar cargas de trabalho.

Dependendo da configuração, você pode conectar recursos no local aos recursos do hub e dos raios.

Na topologia ilustrada acima, a rede local é conectada a um hub VNet no Azure, e há 2 VNets de fala na mesma região que espiaram com o hub VNet.  Neste cenário, as opções de conectividade suportadas para os volumes de arquivos do Azure NetApp são as seguintes:

* Recursos no local VM 1 e VM 2 podem se conectar ao Volume 1 no hub através de um circuito VPN ou ExpressRoute site-to-site. 
* Recursos no local Os recursos VM 1 e VM 2 podem se conectar ao Volume 2 ou Volume 3 através de uma VPN local-para-local e peering vnet regional.
* VM 3 no hub VNet pode se conectar ao Volume 2 em vnet falou 1 e volume 3 em falou VNet 2.
* VM 4 de falou VNet 1 e VM 5 de falou VNet 2 pode se conectar ao Volume 1 no hub VNet.

VM 4 em spoke VNet 1 não pode se conectar ao Volume 3 no VNet 2 falado. Além disso, a VM 5 em spoke VNet2 não pode se conectar ao Volume 2 no VNet 1 falado. Este é o caso porque os VNets falados não são peered e _o roteamento de trânsito não é suportado por peering VNet_.

## <a name="next-steps"></a>Próximas etapas

[Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
