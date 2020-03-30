---
title: 'Azure ExpressRoute: Projetando para recuperação de desastres'
description: Esta página fornece recomendações arquitetônicas para recuperação de desastres ao usar o Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076698"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Projetando para recuperação de desastres com peering privado ExpressRoute

O ExpressRoute foi projetado para alta disponibilidade para fornecer conectividade de rede privada de nível de operadora aos recursos da Microsoft. Em outras palavras, não há um único ponto de falha no caminho ExpressRoute dentro da rede Microsoft. Para obter considerações sobre o design para maximizar a disponibilidade de um circuito ExpressRoute, consulte [Designing for high availability with ExpressRoute][HA].

No entanto, tomando o adátipo popular de Murphy -*se alguma coisa pode dar errado, ele vai*- em consideração, neste artigo vamos focar em soluções que vão além de falhas que podem ser tratadas usando um único circuito ExpressRoute. Em outras palavras, neste artigo, vamos analisar as considerações da arquitetura de rede para construir conectividade de rede backend robusta para recuperação de desastres usando circuitos ExpressRoute geo-redundantes.

## <a name="need-for-redundant-connectivity-solution"></a>Necessidade de solução de conectividade redundante

Há possibilidades e instâncias em que um serviço regional inteiro (seja o da Microsoft, provedores de serviços de rede, clienteou outros provedores de serviços em nuvem) é degradado. A causa principal para esse impacto regional de serviço saem da calamidade natural. Portanto, para a continuidade de negócios e aplicações críticas de missão é importante planejar a recuperação de desastres.   

Independentemente de você executar seus aplicativos de missão crítica em uma região do Azure ou no local ou em qualquer outro lugar, você pode usar outra região do Azure como seu site de failover. Os seguintes artigos abordam a recuperação de desastres de aplicativos e perspectivas de acesso ao frontend:

- [Recuperação de desastre de escala empresarial][Enterprise DR]
- [Recuperação de desastre para SMB com o Azure Site Recovery][SMB DR]

Se você confiar na conectividade ExpressRoute entre sua rede local e a Microsoft para operações de missão crítica, seu plano de recuperação de desastres também deve incluir conectividade de rede geo-redundante. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Desafios de usar vários circuitos ExpressRoute

Quando você interconecta o mesmo conjunto de redes usando mais de uma conexão, você introduz caminhos paralelos entre as redes. Caminhos paralelos, quando não devidamente arquitetados, podem levar a roteamento assimétrico. Se você tiver entidades estaduais (por exemplo, NAT, firewall) no caminho, o roteamento assimétrico pode bloquear o fluxo de tráfego.  Normalmente, ao longo do caminho de peering privado expressRoute você não vai se deparar com entidades estatais, como NAT ou Firewalls. Portanto, o roteamento assimétrico sobre o peering privado ExpressRoute não bloqueia necessariamente o fluxo de tráfego.
 
No entanto, se você carregar o tráfego de equilíbrio através de caminhos paralelos geo-redundantes, independentemente de você ter entidades estaduais ou não, você experimentará um desempenho de rede inconsistente. Neste artigo, vamos discutir como enfrentar esses desafios.

## <a name="small-to-medium-on-premises-network-considerations"></a>Considerações de rede de pequeno a médio porte

Vamos considerar o exemplo de rede ilustrado no diagrama a seguir. No exemplo, a conectividade expressroute geo-redundante é estabelecida entre a localização local de um Contoso e o VNet de Contoso em uma região azure. No diagrama, a linha verde sólida indica o caminho preferido (via ExpressRoute 1) e o pontilhado representa o caminho stand-by (via ExpressRoute 2).

[![1]][1]

Quando você está projetando conectividade ExpressRoute para recuperação de desastres, você precisa considerar:

- usando circuitos ExpressRoute geo-redundantes
- usando diversas redes de provedores de serviços para diferentes circuitos ExpressRoute
- projetando cada um dos circuitos ExpressRoute para [alta disponibilidade][HA]
- terminando o circuito ExpressRoute diferente em diferentes locais na rede do cliente

Por padrão, se você anunciar rotas de forma idêntica em todos os caminhos do ExpressRoute, o Azure irá equilibrar o tráfego no local em todos os caminhos do ExpressRoute usando roteamento ecmp (Equal-cost multi-path).

No entanto, com os circuitos expressroute geo-redundantes, precisamos levar em consideração diferentes desempenhos de rede com diferentes caminhos de rede (particularmente para latência de rede). Para obter um desempenho de rede mais consistente durante a operação normal, você pode preferir o circuito ExpressRoute que oferece a latência mínima.

Você pode influenciar o Azure a preferir um circuito ExpressRoute em vez de outro usando uma das seguintes técnicas (listadas na ordem de eficácia):

- anunciando rota mais específica sobre o circuito ExpressRoute preferido em comparação com outros circuitos ExpressRoute
- configurando maior peso de conexão na conexão que liga a rede virtual ao circuito ExpressRoute preferido
- anunciando as rotas sobre o circuito ExpressRoute menos preferido com caminho AS mais longo (as path prepend)

### <a name="more-specific-route"></a>Rota mais específica

O diagrama a seguir ilustra influenciando a seleção do caminho ExpressRoute usando um anúncio de rota mais específico. No exemplo ilustrado, contoso no local /24 ip range é anunciado como duas faixas de endereços /25 através do caminho preferido (ExpressRoute 1) e como /24 através do caminho stand-by (ExpressRoute 2).

[![2]][2]

Como /25 é mais específico, em comparação com /24, o Azure enviaria o tráfego destinado a 10.1.11.0/24 via ExpressRoute 1 no estado normal. Se ambas as conexões do ExpressRoute 1 forem para baixo, então o VNet verá o anúncio de rota 10.1.11.0/24 apenas via ExpressRoute 2; e, portanto, o circuito de espera é usado neste estado de falha.

### <a name="connection-weight"></a>Peso da conexão

A captura de tela a seguir ilustra a configuração do peso de uma conexão ExpressRoute através do portal Azure.

[![3]][3]

O diagrama a seguir ilustra a influência da seleção do caminho ExpressRoute usando o peso da conexão. O peso de conexão padrão é 0. No exemplo abaixo, o peso da conexão para ExpressRoute 1 é configurado como 100. Quando um VNet recebe um prefixo de rota anunciado através de mais de um circuito ExpressRoute, o VNet prefere a conexão com o maior peso.

[![4]][4]

Se ambas as conexões do ExpressRoute 1 forem para baixo, então o VNet verá o anúncio de rota 10.1.11.0/24 apenas via ExpressRoute 2; e, portanto, o circuito de espera é usado neste estado de falha.

### <a name="as-path-prepend"></a>Preparação do caminho AS

O diagrama a seguir ilustra a influência da seleção do caminho ExpressRoute usando o estieo de caminho DO AS. No diagrama, o anúncio de rota sobre expressRoute 1 indica o comportamento padrão do eBGP. No anúncio de rota sobre o ExpressRoute 2, o ASN da rede local é preparado adicionalmente no caminho AS da rota. Quando a mesma rota é recebida através de vários circuitos ExpressRoute, de acordo com o processo de seleção de rotas eBGP, o VNet prefere a rota com o caminho AS mais curto. 

[![5]][5]

Se ambas as conexões do ExpressRoute 1 forem para baixo, então o VNet verá o anúncio da rota 10.1.11.0/24 apenas via ExpressRoute 2. Consequentemente, o caminho mais longo do AS se tornaria irrelevante. Portanto, o circuito de espera seria usado neste estado de falha.

Usando qualquer uma das técnicas, se você influenciar o Azure a preferir uma de suas ExpressRoute em vez de outras, você também precisa garantir que a rede local também prefira o mesmo caminho ExpressRoute para tráfego vinculado ao Azure para evitar fluxos assimétricos. Normalmente, o valor de preferência local é usado para influenciar a rede no local para preferir um circuito ExpressRoute em vez de outros. A preferência local é uma métrica interna de BGP (iBGP). A rota BGP com o maior valor de preferência local é preferida.

> [!IMPORTANT]
> Quando você usa certos circuitos ExpressRoute como stand-by, você precisa gerenciá-los ativamente e testar periodicamente a operação de failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Grande rede empresarial distribuída

Quando você tem uma grande rede corporativa distribuída, é provável que você tenha vários circuitos ExpressRoute. Nesta seção, vamos ver como projetar a recuperação de desastres usando os circuitos ExpressRoute ativos ativos, sem precisar de circuitos adicionais de stand-by. 

Vamos considerar o exemplo ilustrado no diagrama a seguir. No exemplo, Contoso possui dois locais no local conectados a duas implantações do Contoso IaaS em duas regiões diferentes do Azure através de circuitos ExpressRoute em dois locais diferentes. 

[![6]][6]

Como arquitetamos a recuperação de desastres tem um impacto sobre como o tráfego transversal regional para cross location (região1/região2 para localização2/localização1) é roteado. Vamos considerar duas arquiteturas de desastres diferentes que cruzam o tráfego de região de forma diferente.

### <a name="scenario-1"></a>Cenário 1

No primeiro cenário, vamos projetar a recuperação de desastres de tal forma que todo o tráfego entre uma região azure e uma rede local flua através do circuito ExpressRoute local no estado estável. Se o circuito local do ExpressRoute falhar, o circuito remoto ExpressRoute será usado para todos os fluxos de tráfego entre o Azure e a rede local.

O cenário 1 é ilustrado no diagrama a seguir. No diagrama, linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e on-premises. As linhas azuis indicam caminhos para o fluxo de tráfego entre as redes VNet2 e local. Linhas sólidas indicam o caminho desejado no estado estável e as linhas tracejadas indicam o caminho de tráfego na falha do circuito ExpressRoute correspondente que carrega fluxo de tráfego de estado estável. 

[![7]][7]

Você pode projetar o cenário usando o peso da conexão para influenciar os VNets a preferir a conexão com o local de peering local ExpressRoute para tráfego vinculado à rede no local. Para concluir a solução, você precisa garantir fluxo de tráfego reverso simétrico. Você pode usar a preferência local na sessão iBGP entre seus roteadores BGP (nos quais os circuitos ExpressRoute são encerrados no lado local) para preferir um circuito ExpressRoute. A solução é ilustrada no diagrama a seguir. 

[![8]][8]

### <a name="scenario-2"></a>Cenário 2

O Cenário 2 é ilustrado no diagrama a seguir. No diagrama, linhas verdes indicam caminhos para o fluxo de tráfego entre as redes VNet1 e on-premises. As linhas azuis indicam caminhos para o fluxo de tráfego entre as redes VNet2 e local. In the steady-state (solid lines in the diagram), all the traffic between VNets and on-premises locations flow via Microsoft backbone for the most part, and flows through the interconnection between on-premises locations only in the failure state (dotted lines in o diagrama) de uma ExpressRoute.

[![9]][9]

A solução é ilustrada no diagrama a seguir. Como ilustrado, você pode projetar o cenário usando rota mais específica (Opção 1) ou preparação de caminho AS (Opção 2) para influenciar a seleção do caminho Do VNet. Para influenciar a seleção da rota de rede no local para tráfego vinculado ao Azure, você precisa configurar a interconexão entre o local no local como menos preferível. Como configurar o link de interconexão, conforme preferível, depende do protocolo de roteamento usado na rede local. Você pode usar a preferência local com iBGP ou métrica com IGP (OSPF ou IS-IS).

[![10]][10]


## <a name="next-steps"></a>Próximas etapas

Neste artigo, discutimos como projetar para a recuperação de desastres de um circuito ExpressRoute conectividade privada de peering. Os seguintes artigos abordam a recuperação de desastres de aplicativos e perspectivas de acesso ao frontend:

- [Recuperação de desastre de escala empresarial][Enterprise DR]
- [Recuperação de desastre para SMB com o Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "considerações de rede de pequeno a médio porte no local"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "influenciando a seleção de caminhos usando rotas mais específicas"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "configuração do peso da conexão através do portal Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "influenciando a seleção do caminho usando o peso da conexão"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "influenciando a seleção do caminho usando o pré-final do caminho AS"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "grandes considerações de rede distribuídas no local"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "cenário 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "dispositivos expressroute ativos 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "cenário 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "circuitos ExpressRoute ativos ativos solução 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





