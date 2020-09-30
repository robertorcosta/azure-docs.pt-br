---
title: Conceitos-interconectividade de rede
description: Saiba mais sobre os principais aspectos e casos de uso de rede e interconectividade na solução do Azure VMware.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: f8e9ed143d53afe2f7a24c832c69390c6ffcb36b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575751"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Conceitos de rede e interconectividade da solução do Azure VMware

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Uma perspectiva útil na interconectividade é considerar os dois tipos de implementações de nuvem privada da solução Azure VMware:

1. A [**interconectividade básica somente do Azure**](#azure-virtual-network-interconnectivity) permite que você gerencie e use sua nuvem privada com apenas uma única rede virtual no Azure. Essa implementação é mais adequada para avaliações ou implementações da solução Azure VMware que não exigem acesso de ambientes locais.

1. A [**interconectividade completa de local para nuvem privada**](#on-premises-interconnectivity) estende a implementação básica somente do Azure para incluir a interconectividade entre as nuvens privadas da solução do Azure VMware e locais.
 
Neste artigo, abordaremos alguns conceitos importantes que estabelecem rede e interconectividade, incluindo requisitos e limitações. Também abordaremos mais informações sobre os dois tipos de implementações de interconectividade de nuvem privada da solução Azure VMware. Este artigo fornece as informações que você precisa saber para configurar sua rede para trabalhar com a solução do Azure VMware corretamente.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Casos de uso de nuvem privada da solução Azure VMware

Os casos de uso das nuvens privadas da solução Azure VMware incluem:
- Novas cargas de trabalho de VM VMware na nuvem
- Carga de trabalho de VM intermitente para a nuvem (somente para a solução do Azure VMware)
- Migração de carga de trabalho de VM para a nuvem (somente para solução de VMware local para Azure)
- Recuperação de desastre (solução do Azure VMware para solução Azure VMware ou local para solução VMware do Azure)
- Consumo de serviços do Azure

> [!TIP]
> Todos os casos de uso para o serviço de solução do Azure VMware são habilitados com conectividade de nuvem local para privada.

## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade de rede virtual do Azure

Na implementação de rede virtual para nuvem privada, você pode gerenciar sua nuvem privada da solução Azure VMware, consumir cargas de trabalho em sua nuvem privada e acessar os serviços do Azure pela conexão do ExpressRoute. 

O diagrama a seguir mostra a interconectividade de rede básica estabelecida no momento de uma implantação de nuvem privada. Ele mostra a rede lógica, baseada em ExpressRoute, entre uma rede virtual no Azure e uma nuvem privada. A interconectividade preenche três dos principais casos de uso:
* Acesso de entrada ao vCenter Server e ao NSX-T Manager que é acessível de VMs em sua assinatura do Azure e não de seus sistemas locais. 
* Acesso de saída de VMs para os serviços do Azure. 
* Acesso de entrada e consumo de cargas de trabalho que executam uma nuvem privada.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Rede virtual básica para conectividade de nuvem privada" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconectividade local

Na rede virtual e na implementação de nuvem privada completa, você pode acessar suas nuvens privadas da solução Azure VMware a partir de ambientes locais. Essa implementação é uma extensão da implementação básica descrita na seção anterior. Como a implementação básica, um circuito do ExpressRoute é necessário, mas com essa implementação, ele é usado para se conectar de ambientes locais à sua nuvem privada no Azure. 

O diagrama a seguir mostra a interconectividade de local para nuvem privada, que habilita os seguintes casos de uso:
* Excelente/frio entre o vMotion do vCenter
* Acesso de gerenciamento de nuvem privada de solução local para o Azure VMware

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Rede virtual básica para conectividade de nuvem privada" border="false":::

Para obter a interconectividade completa com sua nuvem privada, habilite o ExpressRoute Alcance Global e, em seguida, solicite uma chave de autorização e uma ID de emparelhamento privado para Alcance Global no portal do Azure. A chave de autorização e a ID de emparelhamento são usadas para estabelecer Alcance Global entre um circuito de ExpressRoute em sua assinatura e o circuito de ExpressRoute para sua nova nuvem privada. Uma vez vinculado, os dois circuitos do ExpressRoute roteiam o tráfego de rede entre seus ambientes locais para sua nuvem privada.  Consulte o [tutorial para criar um ExpressRoute alcance global emparelhamento para uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md) para que os procedimentos solicitem e usem a chave de autorização e a ID de emparelhamento.



## <a name="next-steps"></a>Próximas etapas 
Saiba mais sobre os [conceitos de armazenamento em nuvem privada](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

