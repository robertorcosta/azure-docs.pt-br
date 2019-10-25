---
title: Configurar a BFD no ExpressRoute – Azure | Microsoft Docs
description: Este artigo fornece instruções sobre como configurar a BFD (Detecção de Encaminhamento Bidirecional) no emparelhamento privado de um circuito do ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 8/17/2018
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: e33e90d988251afde630401bed165a4d3614d2cd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881459"
---
# <a name="configure-bfd-over-expressroute"></a>Configurar BFD no ExpressRoute

O ExpressRoute dá suporte para BFD (Detecção de Encaminhamento Bidirecional) em emparelhamento privado. Ao habilitar o BFD no ExpressRoute, você pode acelerar a detecção de falhas de link entre os dispositivos do Microsoft Enterprise Edge (MSEE) e os roteadores nos quais você encerra o circuito do ExpressRoute (PE/CE). É possível terminar o ExpressRoute nos dispositivos de roteamento de Borda de Clientes ou dispositivos de roteamento de Borda do Parceiro (se tiver optado pelo serviço de conexão gerenciado da Camada 3). Este documento explica a necessidade de BFD e de como habilitar a BFD no ExpressRoute.

## <a name="need-for-bfd"></a>Necessidade de BFD

O diagrama a seguir mostra o benefício de habilitar BFD no circuito do ExpressRoute: [![1]][1]

É possível habilitar o circuito do ExpressRoute por meio de conexões da Camada 2 ou por conexões gerenciadas da Camada 3. Em ambos os casos, se houver um ou mais dispositivos de Camada 2 no caminho de conexão do ExpressRoute, a responsabilidade de detectar qualquer falha de vínculo no caminho está na BGP sobreposta.

Nos dispositivos MSEE, o tempo em espera e de keep alive de BGP são tipicamente configurados como 60 e 180 segundos, respectivamente. Portanto, após uma falha de vínculo, levaria até três minutos para detectar qualquer falha de vínculo e alternar o tráfego para uma conexão alternativa.

É possível controlar os temporizadores de BGP, configurando o tempo em espera e de keep alive de BGP inferior no dispositivo de emparelhamento de borda de clientes. Se os temporizadores de BGP forem incompatíveis entre os dois dispositivos de emparelhamento, a sessão de BGP entre os pares usaria o valor temporal inferior. O keep alive de BGP pode ser definido em apenas três segundos e o tempo em espera na ordem de dezenas de segundos. No entanto, a configuração de temporizadores de BGP é dinamicamente menos preferível, porque o protocolo é processo intensivo.

Nesse cenário, a BFD pode ajudar. A BFD fornece detecção de falha de vínculo de baixa sobrecarga em um intervalo de tempo em fração de segundos. 


## <a name="enabling-bfd"></a>Habilitar BFD

A BFD é configurada por padrão em todas as interfaces de emparelhamento privado do ExpressRoute criadas recentemente nos MSEEs. Portanto, para habilitar o BFD, você precisa apenas configurar o BFD no PEs/CEs (ambos em seus dispositivos primários e secundários). A configuração do BFD é um processo de duas etapas: você precisa configurar o BFD na interface e, em seguida, vinculá-lo à sessão BGP.

Um exemplo de configuração PE/CE (usando o Cisco IOS XE) é mostrado abaixo. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Para habilitar a BFD em um emparelhamento privado já existente, será necessário redefinir o emparelhamento. Consulte [Redefinir emparelhamentos de ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negociação de temporizador de BFD

Entre pares de BFD, o mais lento dos dois pares determina a taxa de transmissão. Os intervalos de transmissão/recepção de BFD dos MSEEs estão definidos para 300 milissegundos. Em determinados cenários, o intervalo pode ser definido em um valor mais alto de 750 milissegundos. Ao configurar valores maiores você poderá forçar esses intervalos a serem mais longos, porém, não mais curtos.

>[!NOTE]
>Se você configurou circuitos de emparelhamento privado do ExpressRoute com redundância geográfica ou usa conectividade de VPN IPSec de site a site como backup para o emparelhamento privado do ExpressRoute, habilitar a BFD no emparelhamento privado permitiria failover mais rápido após uma falha de conectividade do ExpressRoute. 
>

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações ou ajuda, confira os seguintes links:

- [Criar e modificar um circuito do ExpressRoute][CreateCircuit]
- [Criar e modificar o roteamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "A BFD acelera o tempo de dedução de falha de vínculo"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






