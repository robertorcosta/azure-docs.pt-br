---
title: Rede global da Microsoft - Azure
description: Descreve como a Microsoft constrói sua rede global rápida e confiável
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453064"
---
# <a name="microsoft-global-network"></a>Rede global da Microsoft

A Microsoft possui e opera uma das maiores redes backbone do mundo. Esta arquitetura global e sofisticada, abrangendo mais de 100.000 milhas, conecta nossos data centers e clientes. 
 
Todos os dias, clientes em todo o mundo se conectam e passam trilhões de solicitações para microsoft Azure, Bing, Dynamics 365, Office 365, XBox e muitos outros. Independentemente do tipo, os clientes esperam confiabilidade instantânea e capacidade de resposta de nossos serviços. 
 
A [rede global da Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) é uma parte central de oferecer uma grande experiência em nuvem. Conectando nossos [data centers](https://azure.microsoft.com/global-infrastructure/) da Microsoft em 54 regiões do Azure e uma grande malha de nós de borda estrategicamente posicionados em todo o mundo, nossa rede global oferece disponibilidade, capacidade e flexibilidade para atender a qualquer demanda.

![Rede global da Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Obtenha a rede de nuvem premium
 
Optar pela [melhor experiência possível](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) é fácil quando você usa a nuvem da Microsoft. A partir do momento em que o tráfego de clientes entra em nossa rede global através de nossos nós de borda estrategicamente posicionados, seus dados viajam através de rotas otimizadas perto da velocidade da luz. Isso garante uma latência ideal para o melhor desempenho. Esses nós de borda, todos interconectados a mais de 3500 parceiros únicos da Internet (pares) através de milhares de conexões em mais de 145 locais, fornecem a base de nossa estratégia de interconexão. 
 
Seja conectando-se de Londres a Tóquio, ou de Washington DC a Los Angeles, o desempenho da rede é quantificado e impactado por coisas como latência, nervosismo, perda de pacotes e throughput.  Na Microsoft, preferimos e usamos interconexões diretas em oposição a links de trânsito, isso mantém o tráfego de resposta simétrico e ajuda a manter os saltos, partes e caminhos o mais curtos e simples possível. 

Por exemplo, se um usuário em Londres tentar acessar um serviço em Tóquio, então o tráfego da Internet entra em uma de nossas bordas em Londres, passa por cima da Microsoft WAN através da França, nossos caminhos trans-Saudita entre a Europa e a Índia, e depois para o Japão, onde o serviço está hospedado. O tráfego de resposta é simétrico. Isso às vezes é chamado [de roteamento de batata fria,](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) o que significa que o tráfego permanece na rede Microsoft o maior tempo possível antes de entregá-lo.  
  
Então, isso significa todo e qualquer tráfego ao usar serviços da Microsoft? Sim, qualquer tráfego entre data centers, dentro do Microsoft Azure ou entre serviços da Microsoft, como Máquinas Virtuais, Office 365, XBox, DBs SQL, Armazenamento e redes virtuais são roteados dentro de nossa rede global e nunca pela Internet pública, para garantir o ideal desempenho e integridade.  
 
Investimentos maciços em capacidade de fibra e diversidade em caminhos metroviários, terrestres e submarinos são cruciais para mantermos o nível de serviço consistente e alto, ao mesmo tempo em que alimentam os extremos crescimento de nossa nuvem e serviços on-line. Adições recentes à nossa rede global são o nosso cabo submarino [MAREA,](https://www.submarinecablemap.com/#/submarine-cable/marea) o primeiro Sistema de Linha Aberta (OLS) da indústria sobre o submara, entre Bilbao, Espanha e Virginia Beach, Virgínia, EUA, bem como o [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) entre Nova York, EUA e Dublin, Irlanda e [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) entre Tóquio, Japão, e Portland, Oregon, EUA. 
 

## <a name="our-network-is-your-network"></a>Nossa rede é sua rede

Colocamos duas décadas de experiência, juntamente com investimentos maciços na rede, para garantir o melhor desempenho em todos os momentos. As empresas podem aproveitar ao máximo nossos ativos de rede e construir arquiteturas avançadas de sobreposição no topo. 
 
O Microsoft Azure oferece o mais rico portfólio de serviços e recursos, permitindo que os clientes construam, expandam e atendam aos requisitos de rede em qualquer lugar. Nossa família de serviços de conectividade abrange a rede virtual de peering entre regiões, arquiteturas híbridas e em nuvem ponto a ponto e local-a-local, bem como cenários globais de trânsito ip.  Para empresas que procuram conectar seu próprio data center ou rede ao Azure, ou clientes com necessidades maciças de ingestão de dados ou trânsito, [o ExpressRoute](../expressroute/expressroute-introduction.md)e o [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) oferecem opções de até 100 Gbps de largura de banda, diretamente na rede global da Microsoft em locais de peeringinging em todo o mundo.  
 
[O ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) foi projetado para complementar a implementação wan do seu provedor de serviços e conectar seus sites locais em todo o mundo. Se você executar uma operação global, você pode usar o ExpressRoute Global Reach em conjunto com seus provedores de serviços locais e preferidos para conectar todos os seus sites globais usando a rede global da Microsoft. A expansão de sua nova rede na nuvem (WAN) para abranger um grande número de filiais pode ser realizada através do Azure Virtual WAN, que traz a capacidade de conectar perfeitamente suas filiais à rede global da Microsoft com dispositivos SDWAN & VPN (isto é, Customer Premises Equipment ou CPE) com facilidade de uso incorporada e gerenciamento automatizado de conectividade e configuração. 
 
[O peering Global VNet](../virtual-network/virtual-network-peering-overview.md) permite que os clientes conectem duas ou mais redes virtuais Do Zure em regiões sem problemas. Uma vez olhando, as redes virtuais aparecem como uma só. O tráfego entre máquinas virtuais nas redes virtuais peered é roteado através da infra-estrutura backbone da Microsoft, assim como o tráfego é roteado entre máquinas virtuais na mesma rede virtual - através apenas de endereços IP privados. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Bem gerenciado usando inovação definida por software

Administrando uma das principais nuvens do mundo, a Microsoft ganhou muita experiência e experiência na construção e gerenciamento de infra-estrutura global de alto desempenho.  
 
Aderimos a um conjunto robusto de princípios operacionais: 
 
- Use o hardware de comutação do melhor da raça nos vários níveis da rede.  
- Implante novos recursos com impacto zero para os usuários finais.  
- Implemente atualizações de forma segura e confiável em toda a frota, o mais rápido possível. Horas em vez de semanas.  
- Utilize telemetria profunda em escala de nuvem e mitigação de falhas totalmente automatizada.  
- Use a tecnologia de rede unificada e definida por software para controlar todos os elementos de hardware da rede.  Eliminando a duplicação e redução de falhas. 
 
Esses princípios se aplicam a todas as camadas da rede: desde a interface de rede host, plataforma de comutação, funções de rede no data center, como Balanceadores de Carga, até o WAN com nossa plataforma de engenharia de tráfego e nossas redes ópticas.  
 
O crescimento exponencial do Azure e sua rede chegou a um ponto em que finalmente percebemos que a intuição humana não poderia mais ser confiada para gerenciar as operações globais de rede. Para atender à necessidade de validar mudanças de longo, médio e curto prazo na rede, desenvolvemos uma plataforma para espelhar e emular nossa rede de produção sinteticamente. A capacidade de criar ambientes espelhados e executar milhões de simulações, nos permite testar mudanças de software e hardware e seu impacto, antes de comprometê-los à nossa plataforma de produção e rede. 

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre os serviços de networking prestados no Azure](https://azure.microsoft.com/product-categories/networking/)
