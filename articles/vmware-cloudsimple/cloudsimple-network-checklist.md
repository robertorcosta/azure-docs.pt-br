---
title: Solução Azure VMware por CloudSimple - Lista de verificação de rede
description: Checklist para alocar CIDR de rede na solução Azure VMware pela CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025003"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Pré-requisitos de rede para a solução Azure VMware pela CloudSimple

O Azure VMware Solution by CloudSimple oferece um ambiente de nuvem privada VMware acessível para usuários e aplicativos de ambientes locais, dispositivos gerenciados por empresas e recursos do Azure. A conectividade é fornecida através de serviços de rede, como VPNs e conexões Azure ExpressRoute. Alguns desses serviços de rede exigem que você especifique os intervalos de endereços de rede para habilitar os serviços. 

As tabelas deste artigo descrevem o conjunto de faixas de endereços e serviços correspondentes que usam os endereços especificados. Alguns dos endereços são obrigatórios e alguns dependem dos serviços que você deseja implantar. Esses espaços de endereço não devem se sobrepor a nenhuma de suas sub-redes no local, sub-redes de rede virtual do Azure ou sub-redes de carga de trabalho planejadas do CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Faixas de endereço susceptío de rede necessárias para criar uma nuvem privada

Durante a criação de um serviço CloudSimple e uma nuvem privada, você deve cumprir as faixas de roteamento entre domínios sem classe de rede especificadas (CIDR), da seguinte forma.

| Nome/usado para     | Descrição                                                                                                                            | Intervalo de endereços            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway CIDR      | Necessário para serviços de borda (gateways VPN).  Este CIDR é necessário durante a criação do CloudSimple Service e deve ser do espaço RFC 1918. | / 28                      |
| vSphere/vSAN CIDR | Necessário para redes de gerenciamento VMware. Este CIDR deve ser especificado durante a criação de nuvens privadas.                                    | /24 ou /23 ou /22 ou /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Faixa de endereço de rede necessária para conexão de rede Do Azure a uma rede local

Conectar-se de uma [rede local à rede privada de nuvem através do ExpressRoute](on-premises-connection.md) estabelece uma conexão Global Reach.  A conexão usa o Border Gateway Protocol (BGP) para trocar rotas entre sua rede local, sua rede de nuvem privada e suas redes Azure.

| Nome/usado para             | Descrição                                                                                                                                                                             | Intervalo de endereços |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute Peering CIDR | Necessário quando você usa o ExpressRoute Global Reach para conectividade no local. Este CIDR deve ser fornecido quando uma solicitação de conexão Global Reach é feita através de um bilhete de suporte. | / 29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Faixa de endereço de rede necessária para usar uma conexão VPN site-a-site para uma rede local

A conexão de uma rede local à rede privada de [nuvem usando VPN site-to-site](vpn-gateway.md) requer os seguintes endereços IP, rede local e identificadores. 

| Intervalo de endereços/endereços | Descrição                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer IP               | No local, o gateway VPN é um endereço IP público. Necessário para estabelecer uma conexão VPN site-site entre um datacenter local e a região do CloudSimple Service. Este endereço IP é necessário durante a criação do gateway VPN site-to-site.                                         |
| Identificador de pares       | Identificador por pares do gateway VPN no local. Isso é geralmente o mesmo **que peer IP**.  Se um identificador exclusivo for especificado no gateway VPN local, o identificador deve ser especificado.  O Peer ID é necessário durante a criação do gateway VPN site-to-site.   |
| Redes locais   | Prefixos no local que precisam acessar redes CloudSimple na região.  Inclua todos os prefixos de uma rede local que acessará a rede CloudSimple, incluindo a rede cliente de onde os usuários acessarão a rede.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Faixa de endereço de rede necessária para o uso de conexões VPN ponto a ponto

Uma conexão VPN ponto a ponto permite o acesso à rede CloudSimple a partir de uma máquina cliente.  [Para configurar vpn ponto a ponto,](vpn-gateway.md)você deve especificar o seguinte intervalo de endereços de rede.

| Intervalo de endereços/endereços | Descrição                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sub-rede do cliente         | Os endereços DHCP são fornecidos pela sub-rede do cliente quando você se conecta usando uma VPN ponto a ponto. Essa sub-rede é necessária enquanto você está criando um gateway VPN ponto a ponto em um portal CloudSimple.  A rede é dividida em duas sub-redes; um para a conexão UDP e o outro para conexões TCP. |

## <a name="next-steps"></a>Próximas etapas

* [Configuração de firewall no local para acessar sua nuvem privada](on-premises-firewall-configuration.md)
* [Quickstart - Crie um serviço CloudSimple](quickstart-create-cloudsimple-service.md)
* [Quickstart- Configure uma nuvem privada](quickstart-create-private-cloud.md)
* Saiba mais sobre [as conexões de rede do Azure](cloudsimple-azure-network-connection.md)
* Saiba mais sobre [gateways VPN](cloudsimple-vpn-gateways.md)
