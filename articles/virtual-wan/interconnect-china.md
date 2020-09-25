---
title: Interconexão com a China usando a WAN Virtual do Azure e um hub seguro
description: Saiba como interconectar com a China usando a WAN virtual do Azure e um hub protegido.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: sukishen
ms.openlocfilehash: d95330c14e0d088bdee03c0edbc2eb9d1deb12cb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329220"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconexão com a China usando a WAN Virtual do Azure e um hub seguro

Ao analisar os setores automotivo, de produção, logística e outras instituições, como embaixadas, geralmente há a questão de como melhorar a interconexão com a China. Essas melhorias são relevantes principalmente para o uso de serviços de nuvem como o Office 365, os Serviços Globais do Azure ou interconectam ramificações dentro da China com um backbone de cliente.

Na maioria dos casos, os clientes estão lidando com altas latências, baixa largura de banda, conexão instável e altos custos de conexão com locais fora da China (por exemplo, Europa ou Estados Unidos).

Um motivo para essas dificuldades é o "Grande Firewall da China", que protege a parte chinesa da Internet e filtra o tráfego para a China. Quase todo o tráfego da China Continental para fora da China, exceto as regiões administrativas especiais como Hong Kong e Macau, passa pelo Grande Firewall. O tráfego que passa por Hong Kong e Macau não percorre o Grande Firewall com força total, ele é processado por um subconjunto do Grande Firewall.

![Interconexão do provedor](./media/interconnect-china/provider.png)

Usando a WAN virtual, um cliente pode estabelecer uma conexão mais eficiente e estável com os Serviços de Nuvem da Microsoft e uma conexão à sua rede corporativa sem violar a lei chinesa de segurança cibernética.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisitos e fluxo de trabalho

Se você deseja manter a conformidade com a lei de segurança cibernética chinesa, precisa atender a um conjunto de determinadas condições.

Primeiro, é preciso trabalhar em conjunto com uma rede e um ISP que possua uma licença ICP (Internet Content Provider) para a China. Na maioria dos casos, você acabará com um dos seguintes provedores:

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

Dependendo do provedor e de suas necessidades, agora é preciso adquirir um dos seguintes serviços de conectividade de rede para interconectar suas ramificações na China.

* Uma rede MPLS/IPVPN 
* Uma WAN definida por software (SDWAN)
* Acesso dedicado à Internet

Em seguida, é preciso entrar em acordo com esse provedor para trabalhar com a Rede Global da Microsoft e a Rede de Borda em Hong Kong, não em Pequim ou Xangai. Nesse caso, Hong Kong é muito importante por causa de sua localização e conexão física com a China.

Embora a maioria dos clientes acredite que Singapura seria a melhor opção para a interconexão, pois parece mais próxima da China ao examinar o mapa, isso não é verdade. Quando você acompanha os mapas de fibra de rede, quase todas as conexões de rede passam por Pequim, Xangai e Hong Kong. Isso faz de Hong Kong a melhor opção de localização para interconectar à China.

Dependendo do provedor, você pode receber ofertas de serviços diferentes. A tabela abaixo mostra um exemplo de provedores e o serviço que eles oferecem, com base nas informações no momento em que este artigo foi escrito.

| Serviço | Exemplos de provedor |
| --- | --- |
| Rede MPLS/IPVPN |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| Acesso dedicado à Internet | PCCW, Hong Kong Telecom, China Mobil|

Com o seu provedor, você pode decidir qual das duas soluções a seguir usar para alcançar o backbone global da Microsoft:

* Usar um Microsoft Azure ExpressRoute que se encerra em Hong Kong. Esse seria o caso do uso de MPLS/IPVPN. Atualmente, o único fornecedor de licenças de ICP com ExpressRoute para Hong Kong é a China Telecom Global. No entanto, eles também podem conversar com outros provedores se utilizarem Cloud Exchange Providers como Megaport ou InterCloud. Para saber mais, confira [Provedores de conectividade do ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Usar um acesso dedicado à Internet diretamente em um dos seguintes pontos de troca na Internet ou usar uma interconexão de rede privada.

A lista a seguir mostra as Trocas de Tráfego possíveis em Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Ao usar essa conexão, seu próximo salto de BGP para os serviços da Microsoft deve ser um Número de Sistema Autônomo da Microsoft (AS#) 8075. Se você usar um único local ou solução SDWAN, essa seria a opção de conexão.

De qualquer forma, ainda recomendamos que você tenha uma segunda opção de Internet regular no continente chinês. Isso é para dividir o tráfego entre o tráfego da empresa para serviços em nuvem como Microsoft 365 e Azure e o tráfego da Internet regulamentado por lei.

Uma arquitetura de rede compatível na China pode ser semelhante ao exemplo a seguir:

![Várias ramificações](./media/interconnect-china/multi-branch.png)

Neste exemplo, com uma interconexão com a Rede Global da Microsoft em Hong Kong, agora você pode começar a aproveitar a [Arquitetura de Trânsito Global da WAN Virtual do Azure](virtual-wan-global-transit-network-architecture.md) e serviços adicionais, como o hub seguro de WAN virtual do Azure, para consumir serviços e interconectar-se às ramificações e datacenter fora da China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicação hub a hub

Nesta seção, usamos a comunicação hub a hub da WAN virtual para interconectar. Nesse cenário, você cria um novo recurso de hub da WAN virtual para se conectar a um hub da WAN virtual em Hong Kong, outras regiões de sua preferência, uma região em que você já possui recursos do Azure, ou onde desejar se conectar.

Uma arquitetura de amostra pode parecer com o seguinte exemplo:

![Exemplo de WAN](./media/interconnect-china/sample.png)

Neste exemplo, as ramificações da China se conectam ao Azure Cloud China e entre si usando conexões VPN ou MPLS. As ramificações que precisam estar conectados aos Serviços Globais usam MPLS ou serviços baseados na Internet conectados diretamente a Hong Kong. Se você quiser usar o ExpressRoute em Hong Kong e em outra região, precisará configurar o [Alcance Global do ExpressRoute](../expressroute/expressroute-global-reach.md) para interconectar os dois circuitos do ExpressRoute.

O Alcance Global do ExpressRoute não está disponível em algumas regiões. Se você precisar interconectar ao Brasil ou à Índia, por exemplo, precisará usar os [Provedores de Troca de Nuvem](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) para fornecer os serviços de roteamento.

A figura abaixo mostra ambos exemplos para esse cenário.

![Alcance Global](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Internet segura para o Office 365

Outra consideração é a segurança da rede, bem como o registro do ponto de entrada entre a China e o componente de backbone estabelecido pela WAN virtual e o backbone do cliente. Na maioria dos casos, é necessária a Internet em Hong Kong para acessar diretamente a Rede do Microsoft Edge e, com isso, os Servidores do Azure Front Door usados para os Serviços do Microsoft 365.

Em ambos cenários com a WAN virtual, você usaria o [hub seguro da WAN Virtual do Azure](../firewall-manager/secured-virtual-hub.md). Usando o Gerenciador de Firewall do Azure, você pode alterar um hub da WAN virtual normal para um hub protegido e implantar e gerenciar um Firewall do Azure dentro desse hub.

A figura a seguir mostra um exemplo desse cenário:

![Acesso à Internet para tráfego de serviços da Web e da Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Fluxos de tráfego e arquitetura

Dependendo da sua escolha em relação à conexão com Hong Kong, a arquitetura geral pode mudar um pouco. Esta seção mostra três arquiteturas disponíveis em combinação diferente com VPN ou SDWAN e/ou ExpressRoute.

Todas essas opções fazem uso do hub seguro da WAN Virtual do Azure para conectividade direta do M365 em Hong Kong. Essas arquiteturas também oferecem suporte aos requisitos de conformidade do [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) e mantêm esse tráfego próximo ao local seguinte do Office 365 Front Door. Como resultado, também é uma melhoria para o uso do Microsoft 365 fora da China.

Ao usar a WAN Virtual do Azure junto com as conexões à Internet, todas as conexões podem se beneficiar de serviços adicionais como o [Serviço de Emparelhamento do Microsoft Azure (MAPS)](https://docs.microsoft.com/azure/peering-service/about). O MAPS foi criado para otimizar o tráfego que chega à Rede Global da Microsoft de Provedores de Serviços de Internet de Terceiros.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opção 1: SDWAN ou VPN

Esta seção aborda um design que usa SDWAN ou VPN para Hong Kong e outras ramificações. Essa opção mostra o uso e o fluxo de tráfego ao usar a conexão com a Internet pura em ambos os sites do backbone da WAN virtual. Nesse caso, a conexão é trazida para Hong Kong usando acesso dedicado à Internet ou uma solução SDWAN do provedor ICP. Outras ramificações também estão usando Internet pura ou soluções SDWAN.

![Tráfego da China para Hong Kong](./media/interconnect-china/china-traffic.png)

Nesta arquitetura, todos os sites estão conectados à Rede Global da Microsoft usando a VPN e a WAN Virtual do Azure. O tráfego entre os sites e Hong Kong é transmitido pela Rede da Microsoft e usa apenas a conexão regular à Internet no último trecho.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opção 2: ExpressRoute e SDWAN ou VPN

Esta seção discute um design que usa o ExpressRoute em Hong Kong e outras ramificações com ramificações VPN/SDWAN. Essa opção mostra o uso do ExpressRoute encerrado em Hong Kong e outras ramificações conectadas por meio de SDWAN ou VPN. No momento, o ExpressRoute em Hong Kong está limitado a um pequeno número de provedores, que você pode encontrar na lista de [Parceiros do ExpressRoute](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![Tráfego do ExpressRoute da China para Hong Kong](./media/interconnect-china/expressroute.png)

Também há opções para encerrar o ExpressRoute da China, por exemplo, na Coreia do Sul ou no Japão. Mas, dada a conformidade, a regulamentação e a latência, Hong Kong é atualmente a melhor opção.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opção 3: Somente ExpressRoute

Esta seção discute um design em que o ExpressRoute é usado para Hong Kong e outras ramificações. Essa opção mostra a interconexão usando o ExpressRoute em ambas as extremidades. Aqui você tem um fluxo de tráfego diferente do outro. O tráfego do Microsoft 365 fluirá para o hub seguro da WAN virtual do Azure e daí para a Microsoft Edge Network e a Internet.

O tráfego que vai para as ramificações interconectadas ou parte deles para os locais na China seguirá uma abordagem diferente dentro dessa arquitetura. Atualmente, a WAN virtual não oferece suporte ao trânsito ExpressRoute para ExpressRoute. O tráfego aproveitará o Alcance Global do ExpressRoute ou a interconexão de terceiros sem passar pelo hub WAN virtual. Ele fluirá diretamente de um Microsoft Enterprise Edge (MSEE) para outro.

![Alcance Global do ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

Atualmente, o Alcance Global do ExpressRoute não está disponível em todos os países/regiões, mas você pode configurar uma solução usando a WAN virtual do Azure.

Você pode, por exemplo, configurar um ExpressRoute com o Emparelhamento da Microsoft e conectar um túnel de VPN por meio desse mesmo ponto à WAN Virtual do Azure. Agora, você habilitou novamente o trânsito entre a VPN e o ExpressRoute sem o Alcance Global e o provedor e serviço de terceiros, como o Megaport Cloud.

## <a name="next-steps"></a>Próximas etapas

Confira os artigos a seguir para saber mais:

* [Arquitetura de rede de trânsito global com WAN Virtual do Azure](virtual-wan-global-transit-network-architecture.md)

* [Criar um hub de WAN Virtual](virtual-wan-site-to-site-portal.md)

* [Configurar um hub seguro de WAN virtual](../firewall-manager/secure-cloud-network.md)

* [Visão Geral da Versão Prévia do Serviço de Emparelhamento do Azure](https://docs.microsoft.com/azure/peering-service/about)
