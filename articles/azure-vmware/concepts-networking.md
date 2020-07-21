---
title: Conceitos-interconectividade de rede
description: Saiba mais sobre os principais aspectos e casos de uso de rede e interconectividade na solução VMware do Azure (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 2378ad56e2754b2d2fde7f895f6673e7d7d561c9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539135"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Conceitos de rede e interconectividade da solução do Azure VMware (AVS) Preview

A interconectividade de rede entre suas nuvens privadas da AVS (solução do Azure VMware) e ambientes locais ou redes virtuais no Azure permite que você acesse e use sua nuvem privada. Alguns conceitos importantes de rede e de interconectividade que estabelecem a base da interconectividade são descritos neste artigo.

Uma perspectiva útil na interconectividade é considerar os dois tipos de implementações de nuvem privada de AVS: implementações com a interconectividade básica somente do Azure e implementações com a interconectividade completa de local para nuvem privada.

Os casos de uso para nuvens do AVS privado incluem:
- novas cargas de trabalho de VM VMware na nuvem
- Intermitência de carga de trabalho de VM para a nuvem
- Migração de carga de trabalho de VM para a nuvem
- recuperação de desastre
- consumo de serviços do Azure

 Todos os casos de uso do serviço AVS são habilitados com conectividade de nuvem local para privada. O modelo de interconectividade básica é mais adequado para avaliações de AVS ou implementações que não exigem acesso de ambientes locais.

Os dois tipos de interconectividade de nuvem privada de AVS são descritos nas seções a seguir.  A interconectividade mais básica é "conectividade de rede virtual do Azure"; Ele permite que você gerencie e use sua nuvem privada com apenas uma única rede virtual no Azure. A interconectividade descrita em "conectividade local" estende a conectividade de rede virtual para incluir também a interconectividade entre os ambientes locais e as nuvens privadas da AVS.

## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade de rede virtual do Azure

A interconectividade básica de rede estabelecida no momento de uma implantação de nuvem privada é mostrada no diagrama a seguir. Ele mostra a rede lógica, baseada em ExpressRoute, entre uma rede virtual no Azure e uma nuvem privada. A interconectividade preenche três dos principais casos de uso:
- Acesso de entrada para redes de gerenciamento em que o servidor vCenter e o NSX-T Manager estão localizados.
    - Acessível de VMs em sua assinatura do Azure, não de seus sistemas locais.
- Acesso de saída de VMs para os serviços do Azure.
- Acesso de entrada e consumo de cargas de trabalho que executam uma nuvem privada.

![Conectividade básica de rede virtual para nuvem privada](./media/concepts/adjacency-overview-drawing-single.png)

O circuito do ExpressRoute nesse cenário de rede virtual para nuvem privada é estabelecido quando você cria uma conexão de uma rede virtual em sua assinatura para o circuito do ExpressRoute de sua nuvem privada. O emparelhamento usa uma chave de autorização e uma ID de circuito que você solicita no portal do Azure. A conexão do ExpressRoute estabelecida por meio do emparelhamento é uma conexão privada, um-para-um entre a nuvem privada e a rede virtual. Você pode gerenciar sua nuvem privada, consumir cargas de trabalho em sua nuvem privada e acessar os serviços do Azure nessa conexão de ExpressRoute.

Quando você implanta uma nuvem privada de AVS, é necessário um único/22 espaço de endereço de rede privada. Esse espaço de endereço não deve se sobrepor aos espaços de endereço usados em outras redes virtuais em sua assinatura. Nesse espaço de endereço, o gerenciamento, o provisionamento e as redes do vMotion são provisionados automaticamente. O roteamento é baseado em BGP e é automaticamente provisionado e habilitado por padrão para cada implantação de nuvem privada.

Quando uma nuvem privada é implantada, você recebe os endereços IP para o vCenter e o NSX-T Manager. Para acessar essas interfaces de gerenciamento, você criará recursos adicionais em uma rede virtual em sua assinatura. Os procedimentos para criar esses recursos e estabelecer o emparelhamento privado do ExpressRoute são fornecidos nos tutoriais.

Você projeta a rede lógica de nuvem privada e a implementa com o NSX-T. A nuvem privada vem com o NSX-T pré-configurado. Um gateway de camada 0 & gateway de camada 1 é previamente provisionado para você. Você pode criar um segmento e anexá-lo ao gateway de camada 1 existente ou anexá-lo a um novo gateway de camada 1 que você pode definir. Os componentes de rede lógica do NSX-T fornecem conectividade leste-oeste entre cargas de trabalho e também fornecem conectividade norte-sul com a Internet e os serviços do Azure. 

## <a name="on-premises-interconnectivity"></a>Interconectividade local

Você também pode conectar ambientes locais para suas nuvens privadas da AVS. Esse tipo de interconectividade é uma extensão para a interconectividade básica descrita na seção anterior.

![conectividade de nuvem privada completa local e de rede virtual](./media/concepts/adjacency-overview-drawing-double.png)

Para estabelecer a interconectividade total com uma nuvem privada, use o portal do Azure para habilitar o Alcance Global de ExpressRoute entre um circuito de ExpressRoute de nuvem privada e um circuito de ExpressRoute local. Essa configuração estende a conectividade básica para incluir o acesso a nuvens privadas de ambientes locais.

Um circuito de ExpressRoute de rede virtual local para o Azure é necessário para se conectar de ambientes locais à sua nuvem privada no Azure. Esse circuito do ExpressRoute está em sua assinatura e não faz parte de uma implantação de nuvem privada. O circuito do ExpressRoute local está além do escopo deste documento. Se você precisar de conectividade local para sua nuvem privada, poderá usar um dos circuitos do ExpressRoute existentes ou comprar um no portal do Azure.

Uma vez vinculado com Alcance Global, os dois circuitos de ExpressRoute rotearão o tráfego de rede entre seus ambientes locais e sua nuvem privada. A interconectividade de nuvem local para privada é mostrada no diagrama anterior. A interconectividade representada no diagrama habilita os seguintes casos de uso:

- Excelente/frio entre o vMotion do vCenter
- Acesso de gerenciamento de nuvem privada local a AVS

Para habilitar a conectividade completa, uma chave de autorização e uma ID de emparelhamento privado para Alcance Global podem ser solicitadas no portal do Azure. Você usa a chave e a ID para estabelecer Alcance Global entre um circuito do ExpressRoute em sua assinatura e o circuito do ExpressRoute para sua nova nuvem privada. O [tutorial para criar uma nuvem privada](tutorial-create-private-cloud.md) fornece os procedimentos para solicitar e usar a chave e a ID.

Os requisitos de roteamento da solução exigem que você planeje espaços de endereço de rede de nuvem privada para evitar sobreposições com outras redes virtuais e redes locais. As nuvens privadas da AVS exigem pelo menos um bloco de endereços de rede CIDR `/22` para sub-redes, mostrado abaixo. Essa rede complementa suas redes locais. Para conectar-se a ambientes locais e redes virtuais, deve ser um bloco de endereço de rede não sobreposto.

Exemplo de bloco de endereço de rede CIDR `/22`: `10.10.0.0/22`

As sub-redes:

| Uso de rede             | Sub-rede | Exemplo        |
| ------------------------- | ------ | -------------- |
| Gerenciamento de nuvem privada            | `/24`    | `10.10.0.0/24`   |
| Rede do vMotion       | `/24`    | `10.10.1.0/24`   |
| Cargas de trabalho de VM | `/24`   | `10.10.2.0/24`   |
| Emparelhamento do ExpressRoute | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Próximas etapas 

A próxima etapa é aprender sobre os [conceitos de armazenamento em nuvem privada](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
