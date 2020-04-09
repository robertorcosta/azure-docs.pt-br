---
title: Interconecte-se com a China usando o Azure Virtual WAN e o Secure Hub
description: Saiba mais sobre conectividade de ramificação por ramificação escalonável automatizada de WAN Virtual, regiões disponíveis e parceiros.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985619"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconecte-se com a China usando o Azure Virtual WAN e o Secure Hub

Quando se olha para indústrias automotivas, manufaturas, logísticas comuns ou outros institutos como embaixadas, muitas vezes há a questão sobre como melhorar a interconexão com a China. Essas melhorias são principalmente relevantes para o uso de Serviços de Nuvem como o Office 365, Azure Global Services ou interligar filiais dentro da China com um backbone do cliente.

Na maioria dos casos, os clientes estão lutando com altas latências, baixa largura de banda, conexão instável e altos custos de conexão fora da China (por exemplo, Europa ou Estados Unidos).

Uma razão para essas lutas é o "Grande Firewall da China", que protege a parte chinesa da Internet e filtra o tráfego para a China. Quase todo o tráfego que vai da China continental para fora da China, exceto as zonas de administração especiais como Hong Kong e Macau, passa pelo Grande Firewall. O tráfego que atravessa Hong Kong e Macau não atinge o Grande Firewall com força total, ele é manuseado por um subconjunto do Grande Firewall.

![Interconexão do provedor](./media/interconnect-china/provider.png)

Usando o WAN virtual, um cliente pode estabelecer uma conexão mais performática e estável com o Microsoft Cloud Services e uma conexão com sua rede corporativa sem infringir a lei chinesa de segurança cibernética.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisitos e fluxo de trabalho

Se você quiser ficar em conformidade com a lei chinesa de segurança cibernética, você precisa atender a um conjunto de certas condições.

Primeiro, você precisa trabalhar em conjunto com uma rede e um provedor de internet que possui uma licença ICP (Internet Content Provider) para a China. Na maioria dos casos, você vai acabar com um dos seguintes provedores:

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltda.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

Dependendo do provedor e de suas necessidades, agora você precisa comprar um dos seguintes serviços de conectividade de rede para interconectar suas filiais dentro da China.

* Uma rede MPLS/IPVPN 
* Um SOFTWARE DEFINED WAN (SDWAN)
* Acesso dedicado à Internet

Em seguida, você precisa concordar com esse provedor para dar uma fuga para a Microsoft Global Network e sua Edge Network em Hong Kong, não em Pequim ou Xangai. Neste caso, Hong Kong é muito importante por causa de sua conexão física e localização para a China.

Embora a maioria dos clientes pense que usar Cingapura para interconexão é o melhor caso porque parece mais próximo da China quando se olha no mapa, isso não é verdade. Quando você segue mapas de fibra de rede, quase todas as conexões de rede passam por Pequim, Xangai e Hong Kong. Isso faz de Hong Kong uma melhor opção de localização para se interconectar com a China.

Dependendo do provedor, você pode obter diferentes ofertas de serviço. A tabela abaixo mostra um exemplo de prestadores e o serviço que oferecem, com base em informações no momento em que este artigo foi escrito.

| Serviço | Exemplos de provedores |
| --- | --- |
| Rede MPLS/IPVPN |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| Acesso dedicado à Internet | PCCW, Hong Kong Telecom, China Mobil|

Com o seu provedor, você pode concordar em qual das duas soluções a seguir usar para alcançar o backbone global da Microsoft:

* Terminando um Microsoft Azure ExpressRoute em Hong Kong. Esse seria o caso do uso do MPLS/IPVPN. Atualmente, apenas o único provedor de licença si com ExpressRoute para Hong Kong é a China Telecom Global. No entanto, eles também podem falar com os outros provedores se eles aproveitarem provedores de cloud exchange como Megaport ou InterCloud. Para obter mais informações, consulte [os provedores de conectividade ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Usando um acesso dedicado à Internet diretamente em um dos seguintes Pontos de Troca de Internet, ou usando uma interconexão de rede privada.

A lista a seguir mostra as trocas de Internet possíveis em Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Ao usar este connect, seu próximo salto BGP para Serviços Microsoft deve ser o Número do Sistema Autônomo da Microsoft (AS#) 8075. Se você usar um único local ou solução SDWAN, essa seria a escolha da conexão.

De qualquer forma, nós ainda recomendamos que você tenha uma segunda e regular fuga da Internet para o continente chinês. Isso é para dividir o tráfego entre o tráfego corporativo para serviços de nuvem, como o Microsoft 365 e o Azure, e por lei regulamentado tráfego da Internet.

Uma arquitetura de rede compatível na China pode parecer o seguinte exemplo:

![Múltiplos ramos](./media/interconnect-china/multi-branch.png)

Neste exemplo, tendo uma interconexão com a Rede Global Microsoft em Hong Kong, agora você pode começar a aproveitar a [Arquitetura de Trânsito Global WAN Virtual do Azure](virtual-wan-global-transit-network-architecture.md) e serviços adicionais, como o hub Wan virtual seguro do Azure, a fim de consumir serviços e interconectar-se às suas filiais e datacenter fora da China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicação hub-to-hub

Nesta seção, usamos a comunicação virtual wan hub-to-hub para interconectar. Neste cenário, você cria um novo recurso de hub WAN virtual para se conectar a um hub WAN virtual em Hong Kong, outras regiões que você preferir, uma região onde você já tem recursos do Azure ou onde deseja se conectar.

Uma arquitetura de exemplo pode parecer o seguinte exemplo:

![Amostra wan](./media/interconnect-china/sample.png)

Neste exemplo, as filiais da China se conectam ao Azure Cloud China e entre si usando conexões VPN ou MPLS. As agências que precisam estar conectadas aos Serviços Globais usam MPLS ou serviços baseados na Internet que estão conectados diretamente a Hong Kong. Se você quiser usar o ExpressRoute em Hong Kong, bem como na outra região, você precisa configurar o [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para interconectar ambos os Circuitos ExpressRoute.

O ExpressRoute Global Reach não está disponível em algumas regiões. Se você precisa se conectar com o Brasil ou a Índia, por exemplo, você precisa aproveitar [os Provedores de Cloud Exchange](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) para fornecer os serviços de roteamento.

A figura abaixo mostra os dois exemplos para este cenário.

![Alcance Global](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Fuga segura da Internet para o Office 365

Outra consideração é a segurança da rede, bem como o registro para o ponto de entrada entre a China e o componente backbone estabelecido pela WAN Virtual e o backbone do cliente. Na maioria dos casos, há a necessidade de sair para a Internet em Hong Kong para alcançar diretamente a Microsoft Edge Network e, com isso, os Servidores Front Door do Azure usados para os Serviços Microsoft 365.

Para ambos os cenários com WAN virtual, você aproveitaria o hub protegido WAN Virtual do [Azure](../firewall-manager/secured-virtual-hub.md). Usando o Azure Firewall Manager, você pode alterar um hub WAN virtual regular para um hub seguro e, em seguida, implantar e gerenciar um Firewall Azure dentro desse hub.

A figura a seguir mostra um exemplo deste cenário:

![Fuga da Internet para o tráfego de serviços da Web e Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Arquitetura e fluxos de tráfego

Dependendo da sua escolha em relação à conexão com Hong Kong, a arquitetura geral pode mudar ligeiramente. Esta seção mostra três arquiteturas disponíveis em diferentes combinações com VPN ou SDWAN e/ou ExpressRoute.

Todas essas opções fazem uso do hub seguro Azure Virtual WAN para conectividade direta M365 em Hong Kong. Essas arquiteturas também suportam os requisitos de conformidade para o [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) e mantêm esse tráfego perto da próxima localização do Office 365 Front Door. Como resultado, também é uma melhoria para o uso do Microsoft 365 fora da China.

Ao usar o Azure Virtual WAN juntamente com conexões com a Internet, toda conexão pode se beneficiar de serviços adicionais como [o Microsoft Azure Peering Services (MAPS).](https://docs.microsoft.com/azure/peering-service/about) O MAPS foi construído para otimizar o tráfego que chega à Rede Global da Microsoft a partir de provedores de serviços de Internet de terceiros.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opção 1: SDWAN ou VPN

Esta seção discute um design que usa SDWAN ou VPN para Hong Kong e para outras filiais. Esta opção mostra o fluxo de uso e tráfego ao usar conexão pura da Internet em ambos os sites do backbone WAN virtual. Neste caso, a conexão é trazida para Hong Kong usando acesso dedicado à Internet ou uma solução SDWAN do provedor de ICP. Outras filiais também estão usando soluções puras de Internet ou SDWAN.

![Tráfego da China para Hong Kong](./media/interconnect-china/china-traffic.png)

Nesta arquitetura, todos os sites estão conectados à Rede Global da Microsoft usando VPN e Wan Virtual Do Azure. O tráfego entre os sites e Hong Kong é transmitido através da Rede Microsoft e só usa conexão regular com a Internet na última milha.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opção 2: ExpressRoute e SDWAN ou VPN

Esta seção discute um design que usa o ExpressRoute em Hong Kong e outras filiais com filiais VPN/SDWAN. Esta opção mostra o uso do ExpressRoute encerrado em Hong Kong e outras filiais conectadas via SDWAN ou VPN. O ExpressRoute em Hong Kong está atualmente limitado a uma pequena lista de Provedores, que você pode encontrar na lista de [Parceiros expressos](../expressroute/expressroute-locations-providers.md#global-commercial-azure)da Rota .

![China para Hong Kong tráfego ExpressRoute](./media/interconnect-china/expressroute.png)

Há também opções para encerrar o ExpressRoute da China, por exemplo, na Coreia do Sul ou no Japão. Mas, dada a conformidade, regulação e latência, Hong Kong é atualmente a melhor escolha.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opção 3: Somente ExpressRoute

Esta seção discute um design onde o ExpressRoute é usado para Hong Kong e outras Filiais. Esta opção mostra a interconexão usando ExpressRoute em ambas as extremidades. Aqui você tem um fluxo de tráfego diferente do outro. O tráfego do Microsoft 365 fluirá para o hub virtual WAN do Azure e de lá para a Microsoft Edge Network e a Internet.

O tráfego que vai para os ramos interligados ou deles para os locais na China seguirá uma abordagem diferente dentro dessa arquitetura. Atualmente, a WAN virtual não suporta expressroute para expressroute trânsito. O tráfego aproveitará o ExpressRoute Global Reach ou a interconexão de terceiros sem passar pelo HUB WAN virtual. Ele fluirá diretamente de um Microsoft Enterprise Edge (MSEE) para outro.

![Alcance Global do ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

Atualmente, o ExpressRoute Global Reach não está disponível em todos os países, mas você pode configurar uma solução usando o Azure Virtual WAN.

Você pode, por exemplo, configurar uma ExpressRoute com o Microsoft Peering e conectar um túnel VPN através desse peering ao Azure Virtual WAN. Agora você habilitou, novamente, o trânsito entre VPN e ExpressRoute sem Global Reach e o provedor e serviço de terceiros, como o Megaport Cloud.

## <a name="next-steps"></a>Próximas etapas

Veja os seguintes artigos para obter mais informações:

* [Arquitetura de rede Global Transit com WAN Virtual Azure](virtual-wan-global-transit-network-architecture.md)

* [Crie um hub WAN virtual](virtual-wan-site-to-site-portal.md)

* [Configure um hub virtual protegido por WAN](../firewall-manager/secure-cloud-network.md)

* [Visão geral da visualização do serviço de peering do Azure](https://docs.microsoft.com/azure/peering-service/about)
