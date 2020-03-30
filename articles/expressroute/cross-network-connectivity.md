---
title: Conectividade de rede cruzada do Azure
description: Esta página descreve um cenário de aplicativo para conectividade de rede cruzada e solução com base nos recursos de rede do Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644249"
---
# <a name="cross-network-connectivity"></a>Conectividade entre redes

A Fabrikam Inc. tem uma grande presença física e implantação do Azure no leste dos EUA. A Fabrikam tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute. Da mesma forma, a Contoso Ltd. tem presença e implantação do Azure no Oeste dos EUA. A Contoso tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute.  

Fabrikam Inc. adquire Contoso Ltd. Após a fusão, Fabrikam quer interligar as redes. A figura a seguir ilustra o cenário:

 [![1]][1]

As setas tracejadas no meio da figura acima indicam as interconexões de rede desejadas. Especificamente, existem três tipos de conexões cruzadas desejadas: 1) Fabrikam e Contoso VNets cross connect, 2) Cross regional on-premises e VNets cross connects (ou seja, conectando a rede local Fabrikam ao Contoso VNet e conectando Contoso rede local para Fabrikam VNet), e 3) Fabrikam e Contoso on-premises rede cross connect. 

A tabela a seguir mostra a tabela de rotas do peering privado da ExpressRoute da Contoso Ltd., antes da fusão.

[![2]][2]

A tabela a seguir mostra as rotas efetivas de uma VM na assinatura contoso, antes da fusão. De acordo com a tabela, a VM no VNet está ciente do espaço de endereço súxlo e da rede local Contoso, além das padrão. 

[![4]][4]

A tabela a seguir mostra a tabela de rotas do peering privado da ExpressRoute da Fabrikam Inc., antes da fusão.

[![3]][3]

A tabela a seguir mostra as rotas efetivas de um VM na assinatura fabrikam, antes da fusão. De acordo com a tabela, a VM no VNet está ciente do espaço de endereço sie da rede local Fabrikam, além das padrão.

[![5]][5]

Neste artigo, vamos passar passo a passo e discutir como alcançar as conexões cruzadas desejadas usando os seguintes recursos de rede Do Azure:

* [Peering de rede virtual][Virtual network peering] 
* [Conexão ExpressRoute de rede virtual][connection]
* [Alcance Global][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cross conectando VNets

O peering de rede virtual (peering VNet) fornece o melhor e o melhor desempenho de rede ao conectar duas redes virtuais. O peering VNet suporta peering de dois VNets tanto dentro da mesma região do Azure (comumente chamada de peering VNet) quanto em duas regiões diferentes do Azure (comumente chamada de peering Global VNet). 

Vamos configurar o GNet Global peering entre as assinaturas VNets em Contoso e Fabrikam Azure. Para saber como criar a rede virtual peering entre duas redes virtuais, consulte Criar um artigo [de peering de rede virtual.][Configure VNet peering]

A imagem a seguir mostra a arquitetura da rede após a configuração do peering Global VNet.

[![6]][6]

A tabela a seguir mostra as rotas conhecidas pela VM de assinatura contoso. Preste atenção à última entrada da tabela. Esta entrada é o resultado da conexão cruzada das redes virtuais.

[![7]][7]

A tabela a seguir mostra as rotas conhecidas pela VM de assinatura Fabrikam. Preste atenção à última entrada da tabela. Esta entrada é o resultado da conexão cruzada das redes virtuais.

[![8]][8]

O vnet peering liga diretamente duas redes virtuais (veja que não há próximo salto para a entrada *VNetGlobalPeering* nas duas tabelas acima)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross conectando VNets às redes locais

Podemos conectar um circuito ExpressRoute a várias redes virtuais. Consulte [os limites de assinatura e serviço][Subscription limits] para o número máximo de redes virtuais que podem ser conectadas a um circuito ExpressRoute. 

Vamos conectar o circuito Fabrikam ExpressRoute à assinatura contoso VNet e, da mesma forma, o circuito Contoso ExpressRoute à assinatura Fabrikam VNet para permitir conectividade cruzada entre redes virtuais e as redes locais. Para conectar uma rede virtual a um circuito ExpressRoute em uma assinatura diferente, precisamos criar e usar uma autorização.  Veja o artigo: [Conecte uma rede virtual a um circuito ExpressRoute][Connect-ER-VNet].

A imagem a seguir mostra a arquitetura da rede após a configuração da conectividade cruzada ExpressRoute com as redes virtuais.

[![9]][9]

A tabela a seguir mostra a tabela de rotas do peering privado do ExpressRoute of Contoso Ltd., após a conexão cruzada de redes virtuais às redes locais via ExpressRoute. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes virtuais.

[![10]][10]

A tabela a seguir mostra a tabela de rotas do peering privado da ExpressRoute da Fabrikam Inc., depois de cruzar redes virtuais com as redes locais via ExpressRoute. Veja se a tabela de rotas tem rotas pertencentes a ambas as redes virtuais.

[![11]][11]

A tabela a seguir mostra as rotas conhecidas pela VM de assinatura contoso. Preste atenção às *entradas* do gateway de rede virtual da tabela. A VM vê rotas para ambas as redes locais.

[![12]][12]

A tabela a seguir mostra as rotas conhecidas pela VM de assinatura Fabrikam. Preste atenção às *entradas* do gateway de rede virtual da tabela. A VM vê rotas para ambas as redes locais.

[![13]][13]

>[!NOTE]
>Nas assinaturas Fabrikam e/ou Contoso, você também pode ter falado VNets para o respectivo hub VNet (um design hub e spoke não é ilustrado nos diagramas de arquitetura deste artigo). As conexões cruzadas entre os gateways do Hub VNet para o ExpressRoute também permitirão a comunicação entre hubs e raios leste e oeste.
>

## <a name="cross-connecting-on-premises-networks"></a>Cross conectando redes locais

O ExpressRoute Global Reach fornece conectividade entre redes locais conectadas a diferentes circuitos expressroute. Vamos configurar o Alcance Global entre os circuitos Contoso e Fabrikam ExpressRoute. Como os circuitos ExpressRoute estão em diferentes assinaturas, precisamos criar e usar uma autorização. Consulte Configurar o artigo [ExpressRoute Global Reach][Configure Global Reach] para obter orientação passo a passo.

A imagem a seguir mostra a arquitetura da rede após a configuração do Global Reach.

[![14]][14]

A tabela a seguir mostra a tabela de rota do peering privado do ExpressRoute of Contoso Ltd., após a configuração global reach. Veja se a tabela de rotas possui rotas pertencentes a ambas as redes locais. 

[![15]][15]

A tabela a seguir mostra a tabela de rota do peering privado do ExpressRoute da Fabrikam Inc., após a configuração do Global Reach. Veja se a tabela de rotas possui rotas pertencentes a ambas as redes locais.

[![16]][16]

## <a name="next-steps"></a>Próximas etapas

Consulte [a rede virtual FAQ][VNet-FAQ], para quaisquer outras perguntas sobre VNet e VNet-peering. Consulte [o ExpressRoute FAQ][ER-FAQ] para obter mais perguntas sobre expressroute e conectividade de rede virtual.

O Global Reach é implantado em uma base país/região por país/região. Para ver se a Global Reach está disponível nos países/regiões que você deseja, consulte [ExpressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "O cenário de aplicação"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Tabela de rota suso ExpressRoute antes da fusão"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute tabela de rota antes da fusão"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "rotas de VM contoso antes da fusão"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "rotas Fabrikam VM antes da fusão"
[6]: ./media/cross-network-connectivity/vnet-peering.png "A arquitetura após o vnet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "rotas de VM contoso após o peering da VNet"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "rotas de VM Fabrikam após o peering da VNet"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "A arquitetura após a conexão cruzada ExpressRoutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Tabela de rota do Contoso ExpressRoute após cross conectando ExR e VNets"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute tabela de rota após cross conectando ExR e VNets"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "rotas de VM contoso após cross conectando ExR e VNets"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "rotas Fabrikam VM após cross conectando ExR e VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "A arquitetura após configurar o Alcance Global"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Tabela de rota do Contoso ExpressRoute após a Global Reach"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Tabela de rota Fabrikam ExpressRoute após global reach"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq