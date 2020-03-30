---
title: 'Azure ExpressRoute: Projetando para alta disponibilidade'
description: Esta página fornece recomendações arquitetônicas para alta disponibilidade ao usar o Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076673"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Projetando para alta disponibilidade com expressroute

O ExpressRoute foi projetado para alta disponibilidade para fornecer conectividade de rede privada de nível de operadora aos recursos da Microsoft. Em outras palavras, não há um único ponto de falha no caminho ExpressRoute dentro da rede Microsoft. Para maximizar a disponibilidade, o cliente e o segmento de prestadores de serviços do seu circuito ExpressRoute também devem ser arquitetados para alta disponibilidade. Neste artigo, primeiro vamos analisar as considerações da arquitetura de rede para construir conectividade de rede robusta usando um ExpressRoute, então vamos olhar para os recursos de ajuste fino que ajudam a melhorar a alta disponibilidade do seu circuito ExpressRoute.


## <a name="architecture-considerations"></a>Considerações de arquitetura

A figura a seguir ilustra a maneira recomendada de se conectar usando um circuito ExpressRoute para maximizar a disponibilidade de um circuito ExpressRoute.

 [![1]][1]

Para alta disponibilidade, é essencial manter a redundância do circuito ExpressRoute em toda a rede de ponta a ponta. Em outras palavras, você precisa manter a redundância dentro de sua rede local e não deve comprometer a redundância dentro da rede de prestadores de serviços. Manter a redundância no mínimo implica evitar um único ponto de falha de rede. Ter energia redundante e resfriamento para os dispositivos de rede melhorará ainda mais a alta disponibilidade.

### <a name="first-mile-physical-layer-design-considerations"></a>Considerações sobre o projeto da camada física da primeira milha

 Se você encerrar as conexões primárias e secundárias de um circuito ExpressRoute no mesmo Equipamento de Premissa do Cliente (CPE), você está comprometendo a alta disponibilidade dentro de sua rede local. Além disso, se você configurar as conexões primárias e secundárias através da mesma porta de um CPE (seja terminando as duas conexões em diferentes subinterfaces ou mesclando as duas conexões dentro da rede parceira), você está forçando o parceiro para comprometer a alta disponibilidade em seu segmento de rede também. Este compromisso é ilustrado na figura a seguir.

[![2]][2]

Por outro lado, se você terminar as conexões primárias e secundárias de um circuito ExpressRoute em diferentes locais geográficos, então você pode estar comprometendo o desempenho de rede da conectividade. Se o tráfego for ativamente carregado equilibrado entre as conexões primárias e secundárias que são terminadas em diferentes locais geográficos, uma diferença substancial potencial na latência da rede entre os dois caminhos resultaria em rede subótima Desempenho. 

Para considerações de design geo-redundantes, consulte [Designing for disaster recovery with ExpressRoute][DR].

### <a name="active-active-connections"></a>Conexões ativas

A rede Microsoft está configurada para operar as conexões primárias e secundárias dos circuitos ExpressRoute no modo ativo. No entanto, através de seus anúncios de rota, você pode forçar as conexões redundantes de um circuito ExpressRoute a operar no modo ativo-passivo. Publicidade rotas mais específicas e bgp as vias pré-pendências são as técnicas comuns usadas para fazer um caminho preferido em relação ao outro.

Para melhorar a alta disponibilidade, recomenda-se operar ambas as conexões de um circuito ExpressRoute no modo ativo. Se você permitir que as conexões operem no modo ativo ativo, a rede Microsoft carregará o tráfego entre as conexões por fluxo.

A execução das conexões primárias e secundárias de um circuito ExpressRoute no modo ativo-passivo enfrenta o risco de ambas as conexões falharem após uma falha no caminho ativo. As causas comuns para a falha na troca são a falta de gerenciamento ativo da conexão passiva e a publicidade passiva de rotas obsoletas.

Alternativamente, executar as conexões primárias e secundárias de um circuito ExpressRoute no modo ativo ativo, resulta em apenas cerca de metade dos fluxos falhando e sendo redirecionado, após uma falha de conexão ExpressRoute. Assim, o modo ativo-ativo ajudará significativamente a melhorar o Tempo Médio de Recuperação (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT para peering microsoft 

O peering da Microsoft foi projetado para comunicação entre pontos finais públicos. Então, comumente, os pontos finais privados no local são o Network Address Translated (NATed) com IP público na rede de clientes ou parceiros antes de se comunicarem por peering da Microsoft. Supondo que você use as conexões primárias e secundárias no modo ativo, onde e como você NAT tem um impacto na rapidez com que você se recupera após uma falha em uma das conexões ExpressRoute. Duas opções diferentes de NAT são ilustradas na seguinte figura:

[![3]][3]

Na opção 1, o NAT é aplicado após dividir o tráfego entre as conexões primárias e secundárias do ExpressRoute. Para atender aos requisitos estaduais do NAT, são utilizados pools NAT independentes entre os dispositivos primários e secundários para que o tráfego de retorno chegue ao mesmo dispositivo de borda através do qual o fluxo egressiu.

Na opção 2, um pool NAT comum é usado antes de dividir o tráfego entre as conexões primárias e secundárias do ExpressRoute. É importante fazer a distinção de que o pool NAT comum antes de dividir o tráfego não significa introduzir um único ponto de falha, comprometendo assim a alta disponibilidade.

Com a opção 1, após uma falha de conexão ExpressRoute, a capacidade de alcançar o pool NAT correspondente é quebrada. Portanto, todos os fluxos quebrados devem ser restabelecidos por TCP ou camada de aplicação após o tempo de janela correspondente. Se qualquer um dos pools NAT for usado para frontend qualquer um dos servidores locais e se a conectividade correspondente falhar, os servidores locais não poderão ser alcançados a partir do Azure até que a conectividade seja corrigida.

Considerando que, com a opção 2, o NAT pode ser alcançado mesmo após uma falha de conexão primária ou secundária. Portanto, a própria camada de rede pode redirecionar os pacotes e ajudar a recuperar mais rapidamente após a falha. 

> [!NOTE]
> Se você usar a opção NAT 1 (pools NAT independentes para conexões ExpressaRoute primárias e secundárias) e mapear uma porta de um endereço IP de um pool NAT para um servidor local, o servidor não será acessível através do circuito ExpressRoute quando o correspondente conexão falha.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Recursos de ajuste fino para peering privado

Nesta seção, vamos rever recursos opcionais (dependendo da sua implantação do Azure e de quão sensível você é ao MTTR) recursos que ajudam a melhorar a alta disponibilidade do seu circuito ExpressRoute. Especificamente, vamos rever a implantação com reconhecimento de zona dos gateways de rede virtuais ExpressRoute e da Detecção de Encaminhamento Bidirecional (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Disponibilidade Zona de disponibilidade ciente de gateways de rede virtual ExpressRoute

Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Se você optar pela implantação do Azure IaaS redundante na zona, também poderá querer configurar gateways de rede virtuais redundantes de zona que terminam o peering privado expressRoute. Para saber mais, consulte [Gateways de rede virtual redundantes em áreas no Azure Availability Zones][zone redundant vgw]. Para configurar o gateway de rede virtual redundante de zona, consulte [Criar um gateway de rede virtual redundante de zona nas Regiões de Disponibilidade do Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Melhorando o tempo de detecção de falhas

O ExpressRoute suporta BFD por peering privado. O BFD reduz o tempo de falha de detecção sobre a rede de camada 2 entre o Microsoft Enterprise Edge (MSEEs) e seus vizinhos BGP no lado local de cerca de 3 minutos (padrão) para menos de um segundo. O tempo de detecção de falhas rápidas ajuda a acelerar a recuperação da falha. Para saber mais, consulte [Configurar BFD via ExpressRoute][BFD].

## <a name="next-steps"></a>Próximas etapas

Neste artigo, discutimos como projetar para alta disponibilidade de conectividade de circuito ExpressRoute. Um ponto de peering do circuito ExpressRoute é fixado em uma localização geográfica e, portanto, pode ser impactado por uma falha catastrófica que afeta todo o local. 

Para considerar o projeto para construir conectividade de rede geo-redundante ao backbone da Microsoft que possa suportar falhas catastróficas, que afetam toda uma região, consulte [Designing for de recovery with ExpressRoute private peering][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Maneira recomendada de se conectar usando o ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Conectividade de última milha subótima"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "opções DE NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




