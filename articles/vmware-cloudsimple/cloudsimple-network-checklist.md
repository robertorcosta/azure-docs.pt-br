---
title: Soluções VMware do Azure (AVS)-lista de verificação de rede
description: Lista de verificação para alocar o CIDR de rede na solução VMware do Azure por AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5cbb0fc0514c17fe34f8e57806e6620cfa8b3f68
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025003"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-avs"></a>Pré-requisitos de rede para solução do Azure VMware por AVS

As soluções VMware do Azure (AVS) oferecem um ambiente de nuvem privada VMware que é acessível para usuários e aplicativos de ambientes locais, dispositivos gerenciados pela empresa e recursos do Azure. A conectividade é fornecida por meio de serviços de rede, como VPNs e conexões do Azure ExpressRoute. Alguns desses serviços de rede exigem que você especifique intervalos de endereços de rede para habilitar os serviços. 

As tabelas neste artigo descrevem o conjunto de intervalos de endereços e os serviços correspondentes que usam os endereços especificados. Alguns dos endereços são obrigatórios e alguns dependem dos serviços que você deseja implantar. Esses espaços de endereço não devem se sobrepor a nenhuma de suas sub-redes locais, sub-redes de rede virtual do Azure ou sub-redes de carga de trabalho de AVS planejadas.

## <a name="network-address-ranges-required-for-creating-an-avs-private-cloud"></a>Intervalos de endereços de rede necessários para criar uma nuvem privada de AVS

Durante a criação de um serviço AVS e uma nuvem privada de AVS, você deve estar em conformidade com os intervalos de CIDR (roteamento entre domínios sem classe de rede) especificados, como a seguir.

| Nome/usado para     | Description                                                                                                                            | Intervalo de endereços            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR do gateway      | Necessário para serviços de borda (gateways de VPN). Esse CIDR é necessário durante a criação do serviço AVS e deve ser do espaço RFC 1918. | / 28                      |
| CIDR vSphere/vSAN | Necessário para redes de gerenciamento do VMware. Esse CIDR deve ser especificado durante a criação da nuvem privada AVS.                                    | /24 ou/23 ou/22 ou/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Intervalo de endereços de rede necessário para a conexão de rede do Azure a uma rede local

Conectar-se de uma [rede local à rede de nuvem privada da AVS por meio do ExpressRoute](on-premises-connection.md) estabelece uma conexão alcance global. A conexão usa Border Gateway Protocol (BGP) para trocar rotas entre sua rede local, sua rede de nuvem privada de AVS e suas redes do Azure.

| Nome/usado para             | Description                                                                                                                                                                             | Intervalo de endereços |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| CIDR de emparelhamento do ExpressRoute | Necessário quando você usa o ExpressRoute Alcance Global para conectividade local. Esse CIDR deve ser fornecido quando uma solicitação de conexão Alcance Global é feita por meio de um tíquete de suporte. | / 29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Intervalo de endereços de rede necessário para usar uma conexão VPN site a site para uma rede local

Conectar-se de uma [rede local à rede de nuvem privada da AVS usando a VPN site a site](vpn-gateway.md) requer os seguintes endereços IP, rede local e identificadores. 

| Intervalo de endereços/endereços | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| IP par               | Endereço IP público do gateway de VPN local. Necessário para estabelecer uma conexão VPN site a site entre um datacenter local e a região de serviço AVS. Esse endereço IP é necessário durante a criação do gateway de VPN site a site.                                         |
| Identificador de par       | Identificador de par do gateway de VPN local. Isso geralmente é o mesmo que o **IP par**.  Se um identificador exclusivo for especificado no gateway de VPN local, o identificador deverá ser especificado.  A ID de par é necessária durante a criação do gateway de VPN site a site.   |
| Redes locais   | Prefixos locais que precisam acessar redes AVS na região.  Inclua todos os prefixos de uma rede local que acessarão a rede AVS, incluindo a rede cliente de onde os usuários acessarão a rede.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Intervalo de endereços de rede necessário para usar conexões VPN ponto a site

Uma conexão VPN ponto a site permite o acesso à rede AVS de um computador cliente. [Para configurar a VPN ponto a site](vpn-gateway.md), você deve especificar o intervalo de endereços de rede a seguir.

| Intervalo de endereços/endereços | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sub-rede do cliente         | Os endereços DHCP são fornecidos pela sub-rede do cliente quando você se conecta usando uma VPN ponto a site. Essa sub-rede é necessária enquanto você está criando um gateway de VPN ponto a site em um portal de AVS. A rede é dividida em duas sub-redes, uma para a conexão UDP e a outra para conexões TCP. |

## <a name="next-steps"></a>Próximos passos

* [Configuração de firewall local para acessar sua nuvem privada de AVS](on-premises-firewall-configuration.md)
* [Início rápido-criar um serviço AVS](quickstart-create-cloudsimple-service.md)
* [Início rápido-configurar uma nuvem privada de AVS](quickstart-create-private-cloud.md)
* Saiba mais sobre [as conexões de rede do Azure](cloudsimple-azure-network-connection.md)
* Saiba mais sobre [gateways de VPN](cloudsimple-vpn-gateways.md)
