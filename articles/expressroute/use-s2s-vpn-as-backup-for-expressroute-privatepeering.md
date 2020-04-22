---
title: Usando o S2S VPN como backup do Azure ExpressRoute Private Peering | Microsoft Docs
description: Esta página fornece recomendações arquitetônicas para fazer backup do peering privado do Azure ExpressRoute com o S2S VPN.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687864"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Usando o S2S VPN como backup para peering privado ExpressRoute

No artigo intitulado [Designing for disaster recovery with ExpressRoute private peering][DR-PP], discutimos a necessidade de solução de conectividade de backup para uma conectividade privada de peering expressRoute e como usar circuitos ExpressRoute geo-redundantes para o efeito. Neste artigo, vamos considerar como aproveitar e manter a VPN site-to-site (S2S) como um back para peering privado ExpressRoute. 

Ao contrário dos circuitos ExpressRoute geo-redundantes, você pode usar a combinação de recuperação de desastres ExpressRoute-VPN apenas no modo ativo-passivo. Um grande desafio de usar qualquer conectividade de rede de backup no modo passivo é que a conexão passiva muitas vezes falharia ao lado da conexão primária. A razão comum para as falhas da conexão passiva é a falta de manutenção ativa. Portanto, neste artigo vamos nos concentrar em como verificar e manter ativamente a conectividade S2S VPN que está fazendo backup de um peering privado ExpressRoute.

>[!NOTE] 
>Quando uma determinada rota é anunciada via ExpressRoute e VPN, o Azure prefere o roteamento ao longo do ExpressRoute.  
>

Neste artigo, vamos ver como verificar a conectividade tanto da perspectiva do Azure quanto da perspectiva de borda da rede do lado do cliente. A capacidade de validar de qualquer extremidade ajudará, independentemente de você gerenciar ou não os dispositivos de rede do lado do cliente que se comparam às entidades da rede Microsoft. 

## <a name="example-topology"></a>Exemplo de topologia

Em nossa configuração, temos uma rede local conectada a um VNet hub azure através de um circuito ExpressRoute e uma conexão Vpn S2S. O Hub Azure VNet é, por sua vez, peered para um VNet falado, como mostrado no diagrama abaixo:

![1][1]

Na configuração, o circuito ExpressRoute é encerrado em um par de roteadores "Customer Edge" (CE) no local. A LAN no local é conectada aos roteadores CE através de um par de firewalls que operam no modo líder-seguidor. O S2S VPN é diretamente encerrado nos firewalls.

A tabela a seguir lista os principais prefixos IP da topologia:

| **Entidade** | **Prefixo** |
| --- | --- |
| LAN no local | 10.1.11.0/25 |
| Azure Hub VNet | 10.17.11.0/25 |
| Azure falou VNet | 10.17.11.128/26 |
| Servidor de teste no local | 10.1.11.10 |
| VM de teste VNet falou | 10.17.11.132 |
| Sub-rede p2p de conexão primária ExpressRoute | 192.168.11.16/30 |
| Sub-rede p2p de conexão secundária ExpressRoute | 192.168.11.20/30 |
| IP de peer BGP principal do gateway VPN | 10.17.11.76 |
| IP de peer BGP secundário do gateway VPN | 10.17.11.77 |
| Ip de peer BGP do firewall local | 192.168.11.88 |
| Roteador CE primário i/f para ip firewall | 192.168.11.0/31 |
| Firewall i/f em direção ao IP do roteador CE principal | 192.168.11.1/31 |
| Roteador de CE secundário i/f em direção ao IP de firewall | 192.168.11.2/31 |
| Firewall i/f para IP do roteador ce secundário | 192.168.11.3/31 |


A tabela a seguir lista as ASNs da topologia:

| **Sistema autônomo** | **Asn** |
| --- | --- |
| Local | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Rede Virtual GW (ExR) | 65515 |
| Rede Virtual GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Alta disponibilidade sem assimétrica

### <a name="configuring-for-high-availability"></a>Configuração para alta disponibilidade

[Configurar conexões coexistentes do ExpressRoute e do Site para o Local][Conf-CoExist] discute como configurar o circuito ExpressRoute coexistente e as conexões VPN S2S. Como discutimos no [Projeto de Alta Disponibilidade com o ExpressRoute,][HA]para melhorar a alta disponibilidade do ExpressRoute, nossa configuração mantém a redundância de rede (evita um único ponto de falha) até os pontos finais. Além disso, as conexões primárias e secundárias dos circuitos ExpressRoute são configuradas para operar no modo ativo, anunciando os prefixos on-premises da mesma forma através de ambas as conexões. 

O anúncio de rota no local do roteador CE primário através da conexão primária do circuito ExpressRoute é exibido abaixo (comandos Junos):

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

O anúncio de rota no local do roteador CE secundário através da conexão secundária do circuito ExpressRoute é exibido abaixo (comandos Junos):

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

Para melhorar a alta disponibilidade da conexão de backup, o S2S VPN também está configurado no modo ativo. A configuração do gateway Azure VPN é mostrada abaixo. Nota como parte da configuração VPN, os endereços IP de pares BGP do gateway - 10.17.11.76 e 10.17.11.77 - também estão listados.

![2][2]

A rota no local é anunciada pelos firewalls para os pares BGP primário e secundário do gateway VPN. Os anúncios de rota são mostrados abaixo (Junos):

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>Configurar o S2S VPN no modo ativo ativo não apenas fornece alta disponibilidade para a conectividade da rede de backup de recuperação de desastres, mas também fornece maior throughput à conectividade de backup. Em outras palavras, a configuração do S2S VPN no modo ativo é recomendada à medida que força a criação de vários túneis subjacentes.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Configuração para fluxo de tráfego simétrico

Notamos que quando uma determinada rota no local é anunciada tanto via ExpressRoute quanto S2S VPN, o Azure prefere o caminho expressroute. Para forçar o Azure a preferir o caminho de VPN S2S em vez do ExpressRoute coexistente, você precisa anunciar rotas mais específicas (prefixo mais longo com máscara de sub-rede maior) através da conexão VPN. Nosso objetivo aqui é usar as conexões VPN apenas como de volta. Assim, o comportamento padrão de seleção de caminho do Azure está em linha com nosso objetivo. 

É nossa responsabilidade garantir que o tráfego destinado ao Azure a partir do local também prefira o caminho ExpressRoute em vez do S2S VPN. A preferência local padrão dos roteadores e firewalls CE em nossa configuração no local é de 100. Assim, configurando a preferência local das rotas recebidas através dos peerings privados ExpressRoute maiores que 100 (digamos 150), podemos fazer com que o tráfego destinado ao Azure prefira o circuito ExpressRoute no estado estável.

A configuração BGP do roteador CE primário que termina a conexão primária do circuito ExpressRoute é mostrada abaixo. Observe que o valor da preferência local das rotas anunciadas ao longo da sessão iBGP está configurado para 150. Da mesma forma, precisamos garantir que a preferência local do roteador CE secundário que encerra a conexão secundária do circuito ExpressRoute também esteja configurada para ser 150.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

A tabela de roteamento dos firewalls locais confirma (mostrado abaixo) que para o tráfego local destinado ao Azure o caminho preferido é sobre o ExpressRoute no estado estável.

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

Na tabela de rotas acima, para o hub e as rotas vNet faladas - 10.17.11.0/25 e 10.17.11.128/26 -- vemos que o circuito ExpressRoute é preferido sobre conexões VPN. Os 192.168.11.0 e 192.168.11.2 são IPs na interface de firewall para roteadores CE.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validação da troca de rotas sobre o S2S VPN

No início deste artigo, verificamos o anúncio de rota local dos firewalls para os pares BGP primário e secundário do gateway VPN. Além disso, vamos confirmar as rotas do Azure recebidas pelos firewalls dos pares BGP primário e secundário do gateway VPN.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

Da mesma forma, vamos verificar os prefixos de rota de rede no local recebidos pelo gateway Azure VPN. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

Como visto acima, o gateway VPN tem rotas recebidas tanto pelos pares BGP primários quanto secundários do gateway VPN. Ele também tem visibilidade sobre as rotas recebidas através de conexões ExpressaRoute primárias e secundárias (as com as rotas DE AS preparadas com 12076). Para confirmar as rotas recebidas através de conexões VPN, precisamos conhecer o IP de peer BGP local das conexões. Em nossa configuração em consideração, é 192.168.11.88 e vemos as rotas recebidas dele.

Em seguida, vamos verificar as rotas anunciadas pelo gateway Azure VPN para o peer BGP de firewall local (192.168.11.88).

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


A falha na realização das trocas de rotas indica falha na conexão. Consulte [Solução de problemas: Uma conexão VPN site-site do Azure não pode se conectar e parar de trabalhar][VPN Troubleshoot] para ajudar na solução de problemas da conexão VPN.

## <a name="testing-failover"></a>Failover de teste

Agora que confirmamos trocas de rotas bem-sucedidas sobre a conexão VPN (plano de controle), estamos definidos para mudar o tráfego (data plane) da conectividade ExpressRoute para a conectividade VPN. 

>[!NOTE] 
>Em ambientes de produção, os testes de failover devem ser feitos durante a janela de trabalho de manutenção de rede programada, pois pode ser disruptivo de serviço.
>

Antes de fazer o switch de tráfego, vamos rastrear o caminho atual em nossa configuração desde o servidor de teste no local até a VM de teste no VNet falado.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

As sub-redes de conexão ponto a ponto principal e secundária da nossa configuração são, respectivamente, 192.168.11.16/30 e 192.168.11.20/30. Na rota de rastreamento acima, na etapa 3 vemos que estamos atingindo 192.168.11.18, que é a interface IP do MSEE principal. A presença da interface MSEE confirma que, como esperado, nosso caminho atual está sobre o ExpressRoute.

Como relatado nos [peerings do circuito Reset ExpressRoute,][RST]vamos usar os seguintes comandos powershell para desativar tanto o peering primário quanto o secundário do circuito ExpressRoute.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

O tempo de comutação de failover depende do tempo de convergência BGP. Em nossa configuração, o interruptor de failover leva alguns segundos (menos de 10). Após o switch, a repetição da rota de rastreamento mostra o seguinte caminho:

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

O resultado do traceroute confirma que a conexão de backup via S2S VPN está ativa e pode fornecer continuidade de serviço se as conexões expressroute primárias e secundárias falharem. Para concluir o teste de failover, vamos ativar as conexões ExpressRoute de volta e normalizar o fluxo de tráfego, usando o seguinte conjunto de comandos.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

Para confirmar se o tráfego foi comutado de volta para o ExpressRoute, repita a rota de rastreamento e certifique-se de que ela está passando pelo peering privado ExpressRoute.

## <a name="next-steps"></a>Próximas etapas

O ExpressRoute foi projetado para alta disponibilidade sem nenhum ponto de falha dentro da rede Microsoft. Ainda assim, um circuito ExpressRoute está confinado a uma única região geográfica e a um provedor de serviços. O S2S VPN pode ser uma boa solução de backup passivo de recuperação de desastres para um circuito ExpressRoute. Para uma solução de conexão de backup passiva confiável, a manutenção regular da configuração passiva e a validação periódica da conexão são importantes. É essencial não deixar que a configuração de VPN fique obsoleta e repetir periodicamente (cada trimestre) as etapas de teste de validação e failover descritas neste artigo durante a janela de manutenção.

Para habilitar o monitoramento e os alertas com base nas métricas do gateway VPN, consulte [Configurar alertas nas métricas do VPN Gateway][VPN-alerts].

Para acelerar a convergência do BGP após uma falha no ExpressRoute, [configure bfd via ExpressRoute][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologia em consideração"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Configuração DE GW VPN"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



