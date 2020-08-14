---
title: Conceitos-interconectividade de rede
description: Saiba mais sobre os principais aspectos e casos de uso de rede e interconectividade na solução VMware do Azure (AVS)
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 6f1f1f5a089781f1f7e882c9c8692f0c845ae485
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214098"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Conceitos de rede e interconectividade da versão prévia da Solução VMware no Azure (AVS)

A interconectividade de rede entre suas nuvens privadas da AVS (solução do Azure VMware) e ambientes locais ou redes virtuais no Azure permite que você acesse e use sua nuvem privada. Neste artigo, abordaremos alguns conceitos importantes que estabelecem a base da rede e da interconectividade.

Uma perspectiva útil na interconectividade é considerar os dois tipos de implementações de nuvem privada de AVS:

1. A [**interconectividade básica somente do Azure**](#azure-virtual-network-interconnectivity) permite que você gerencie e use sua nuvem privada com apenas uma única rede virtual no Azure. Essa implementação é mais adequada para avaliações de AVS ou implementações que não exigem acesso de ambientes locais.

1. A [**interconectividade completa de local para nuvem privada**](#on-premises-interconnectivity) estende a implementação básica somente do Azure para incluir a interconectividade entre nuvens privadas locais e AVS.
 
Você pode encontrar mais informações sobre os requisitos e os dois tipos de implementações de interconectividade de nuvem privada de sincronização automática descritas nas seções abaixo.

## <a name="avs-private-cloud-use-cases"></a>Casos de uso de nuvem privada AVS

Os casos de uso para nuvens do AVS privado incluem:
- novas cargas de trabalho de VM VMware na nuvem
- Carga de trabalho de VM de intermitência para a nuvem (somente local para AVS)
- Migração de carga de trabalho de VM para a nuvem (somente no local para AVS)
- recuperação de desastre (AVS para AVS ou local para AVS)
- consumo de serviços do Azure

 Todos os casos de uso do serviço AVS são habilitados com conectividade de nuvem local para privada. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Requisitos de rede virtual e de circuito de ExpressRoute
 
Quando você cria uma conexão de uma rede virtual em sua assinatura, o circuito do ExpressRoute é estabelecido por meio de emparelhamento, usa uma chave de autorização e uma ID de emparelhamento solicitada no portal do Azure. O emparelhamento é uma conexão privada, um-para-um entre sua nuvem privada e a rede virtual.

> [!NOTE] 
> O circuito do ExpressRoute não faz parte de uma implantação de nuvem privada. O circuito do ExpressRoute local está além do escopo deste documento. Se você precisar de conectividade local para sua nuvem privada, poderá usar um dos circuitos do ExpressRoute existentes ou comprar um no portal do Azure.

Ao implantar uma nuvem privada, você recebe endereços IP para o vCenter e o NSX-T Manager. Para acessar essas interfaces de gerenciamento, você precisará criar recursos adicionais em uma rede virtual em sua assinatura. Você pode encontrar os procedimentos para criar esses recursos e estabelecer o emparelhamento privado do ExpressRoute nos tutoriais.

A rede lógica da nuvem privada é fornecida com o NSX-T pré-configurado. Um gateway de camada 0 e um gateway de camada 1 são previamente provisionados para você. Você pode criar um segmento e anexá-lo ao gateway de camada 1 existente ou anexá-lo a um novo gateway de camada 1 que você definir. Os componentes de rede lógica do NSX-T fornecem conectividade leste-oeste entre cargas de trabalho e também fornecem conectividade norte-sul com a Internet e os serviços do Azure. 

## <a name="routing-and-subnet-requirements"></a>Requisitos de roteamento e sub-rede

O roteamento é Border Gateway Protocol (BGP) com base, que é automaticamente provisionado e habilitado por padrão para cada implantação de nuvem privada. Para nuvens de AVS privado, é necessário planejar espaços de endereço de rede de nuvem privada com um mínimo de/22 blocos de endereços de rede CIDR de comprimento de prefixo para sub-redes, mostrados na tabela a seguir. O bloco de endereço não deve se sobrepor aos blocos de endereços usados em outras redes virtuais que estão em sua assinatura e redes locais. Nesse bloco de endereço, o gerenciamento, o provisionamento e as redes do vMotion são provisionados automaticamente.

Exemplo de bloco de endereço de rede CIDR `/22`: `10.10.0.0/22`

As sub-redes:

| Uso de rede             | Sub-rede | Exemplo        |
| ------------------------- | ------ | -------------- |
| Gerenciamento de nuvem privada  | `/24`  | `10.10.0.0/24` |
| Rede do vMotion           | `/24`  | `10.10.1.0/24` |
| Cargas de trabalho de VM              | `/24`  | `10.10.2.0/24` |
| Emparelhamento do ExpressRoute      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade de rede virtual do Azure

Na implementação de rede virtual para nuvem privada, você pode gerenciar sua nuvem privada da AVS, consumir cargas de trabalho em sua nuvem privada e acessar os serviços do Azure pela conexão do ExpressRoute. 

O diagrama a seguir mostra a interconectividade de rede básica estabelecida no momento de uma implantação de nuvem privada. Ele mostra a rede lógica, baseada em ExpressRoute, entre uma rede virtual no Azure e uma nuvem privada. A interconectividade preenche três dos principais casos de uso:
* Acesso de entrada ao vCenter Server e ao NSX-T Manager que é acessível de VMs em sua assinatura do Azure e não de seus sistemas locais. 
* Acesso de saída de VMs para os serviços do Azure. 
* Acesso de entrada e consumo de cargas de trabalho que executam uma nuvem privada.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Rede virtual básica para conectividade de nuvem privada" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconectividade local

Na rede virtual e na implementação de nuvem privada completa, você pode acessar suas nuvens do AVS privado a partir de ambientes locais. Essa implementação é uma extensão da implementação básica descrita na seção anterior. Como a implementação básica, um circuito do ExpressRoute é necessário, mas com essa implementação, ele é usado para se conectar de ambientes locais à sua nuvem privada no Azure. 

O diagrama a seguir mostra a interconectividade de local para nuvem privada, que habilita os seguintes casos de uso:
* Excelente/frio entre o vMotion do vCenter
* Acesso de gerenciamento de nuvem privada local a AVS

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Conectividade de nuvem privada completa local e de rede virtual" border="false":::

Para obter a interconectividade completa com sua nuvem privada, habilite o ExpressRoute Alcance Global e, em seguida, solicite uma chave de autorização e uma ID de emparelhamento privado para Alcance Global no portal do Azure. A chave de autorização e a ID de emparelhamento são usadas para estabelecer Alcance Global entre um circuito de ExpressRoute em sua assinatura e o circuito de ExpressRoute para sua nova nuvem privada. Uma vez vinculado, os dois circuitos do ExpressRoute roteiam o tráfego de rede entre seus ambientes locais para sua nuvem privada.  Consulte o [tutorial para criar um ExpressRoute alcance global emparelhamento para uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md) para que os procedimentos solicitem e usem a chave de autorização e a ID de emparelhamento.


## <a name="next-steps"></a>Próximas etapas 

A próxima etapa é aprender sobre os [conceitos de armazenamento em nuvem privada](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
