---
title: Wan virtual do Azure e trabalhando remotamente
description: Esta página descreve como você pode aproveitar o Azure Virtual WAN para permitir o funcionamento remoto devido à pandemia COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337135"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Wan virtual do Azure e suporte ao trabalho remoto

>[!NOTE]
>Este artigo descreve como você pode aproveitar o Azure Virtual WAN, o Azure, a rede Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e mitigar os problemas de rede que você está enfrentando por causa da crise do COVID-19.
>

Você está lutando para fornecer conectividade para usuários remotos?
Você de repente vê a necessidade de suportar uma onda de usuários além do que você havia planejado?
Você precisa que o usuário se conecte de casa e não só acesse a nuvem, mas também seja capaz de alcançar no local?
Você precisa que seus usuários remotos sejam capazes de alcançar recursos por trás de uma WAN privada?
Você precisa que os usuários acessem recursos intra-nuvem sem ter a necessidade de configurar conectividade entre regiões?
Como essa pandemia global cria mudanças sem precedentes ao nosso redor, a equipe de WAN Virtual do Azure está aqui para você atender às suas necessidades de conectividade.

O Azure Virtual WAN é um serviço de rede que reúne muitas funcionalidades de rede, segurança e roteamento para fornecer uma única interface operacional. Essas funcionalidades incluem conectividade de ramificação (via automação de conectividade de dispositivos virtuais wan partner, como SD-WAN ou VPN CPE), conectividade VPN site-to-site, conectividade de Usuário Remoto VPN (Ponto a local), conectividade privada (ExpressRoute), conectividade privada (ExpressRoute), conectividade privada (ExpressRoute), conectividade privada (ExpressRoute), conectividade privada (ExpressRoute), conectividade privada (ExpressRoute), conectividade privada (ExpressRoute), Conectividade intra-nuvem (conectividade transitiva para redes virtuais), Interconectividade VPN ExpressRoute, Roteamento, firewall Azure, Criptografia para conectividade privada etc. Você não precisa ter todos esses casos de uso para começar a usar wan virtual. Você pode começar com apenas um caso de uso e ajustar sua rede à medida que ela evolui.

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan1.png)

Agora, falando sobre usuários remotos, vamos ver o que você precisa para colocar sua rede em funcionamento:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Configure a conectividade remota do usuário

Você pode conectar seus recursos no Azure por meio de uma conexão de IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão requer que um cliente VPN seja configurado para o usuário remoto. Este cliente pode ser o [cliente Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554) ou O Cliente OpenVPN ou qualquer cliente que suporte IKEv2. Para obter mais informações, consulte [Criar uma conexão ponto a site](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Conectividade do usuário remoto ao local

Você tem duas opções aqui:

* Configure a conectividade site-a-site com qualquer dispositivo VPN existente. Quando você conecta o dispositivo IPsec VPN ao hub WAN virtual do Azure, a interconectividade entre o Usuário VPN (usuário remoto) e a VPN site-to-site é automática. Para obter mais informações sobre como configurar a VPN site-to-site do seu dispositivo VPN local para o Azure Virtual WAN, consulte [Criar uma conexão site-a-site usando WAN virtual](virtual-wan-site-to-site-portal.md).

* Conecte seu circuito ExpressRoute ao hub WAN virtual. Conectar um circuito ExpressRoute requer a implantação de um gateway ExpressRoute na WAN Virtual. Assim que você implantou um, a interconectividade entre o Usuário VPN e o usuário expressRoute é automática. Para criar a conexão ExpressRoute, consulte [Criar uma conexão ExpressRoute usando WAN Virtual](virtual-wan-expressroute-portal.md). Você pode usar um circuito ExpressRoute existente para se conectar ao Azure Virtual WAN.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Cliente WAN virtual básico existente

A WAN Virtual Básica fornece apenas VPN de site para local. Para que os usuários remotos se conectem, você precisará atualizar a WAN virtual para a WAN virtual padrão. Para obter etapas para atualizar uma WAN virtual, consulte [Atualizar uma WAN virtual de Básico para Padrão](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Informações adicionais

A WAN virtual suporta um hub por região/localização. Para obter informações de localização, consulte o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md). Cada hub suporta até 10.000 conexões remotas de usuários, 1.000 conexões de filiais, quatro circuitos ExpressRoute e até 500 conexões de Rede Virtual. À medida que você dimensiona os usuários remotos, se você tiver alguma azurevirtualwan@microsoft.comdúvida, não hesite em procurar ajuda enviando um e-mail para . Se você precisar de suporte técnico, certifique-se de abrir um bilhete de suporte do portal Azure e a ajuda estará a caminho.

## <a name="faq"></a><a name="faq"></a>Perguntas frequentes

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN virtual, consulte [visão geral da WAN virtual](virtual-wan-about.md)