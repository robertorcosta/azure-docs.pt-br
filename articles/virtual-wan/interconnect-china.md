---
title: Interconexão com a China usando a WAN virtual do Azure e o Secure Hub
description: Saiba mais sobre conectividade de ramificação por ramificação escalonável automatizada de WAN Virtual, regiões disponíveis e parceiros.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985619"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interconexão com a China usando a WAN virtual do Azure e o Secure Hub

Ao examinar os setores automotivos, de manufatura, de logística ou de outras instituições como o embassies, geralmente há a questão de como melhorar a interconexão com a China. Esses aprimoramentos são principalmente relevantes para o uso de serviços de nuvem, como o Office 365, os serviços globais do Azure ou branches de interconexão dentro da China com um backbone de cliente.

Na maioria dos casos, os clientes estão lidando com latências altas, baixa largura de banda, conexão instável e altos custos conectados a fora da China (por exemplo, Europa ou Estados Unidos).

Um motivo para essas dificuldades é o "grande firewall da China", que protege a parte chinesa da Internet e filtra o tráfego para a China. Quase todo o tráfego em execução da China continental para fora da China, exceto as zonas de administração especiais, como Hong Kong e Macau, passa o ótimo firewall. O tráfego em execução por meio de Hong Kong e Macau não atinge o ótimo firewall em plena força, ele é tratado por um subconjunto do ótimo firewall.

![Interconexão de provedor](./media/interconnect-china/provider.png)

Usando a WAN virtual, um cliente pode estabelecer uma conexão mais eficaz e estável com os serviços Microsoft Cloud e uma conexão com sua rede corporativa sem quebrar a lei segurança cibernética do chinês.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisitos e fluxo de trabalho

Se você quiser manter a conformidade com a lei chinês segurança cibernética, precisará atender a um conjunto de determinadas condições.

Primeiro, você precisa trabalhar junto com uma rede e um ISP que possui uma licença de ICP (provedor de conteúdo da Internet) para a China. Na maioria dos casos, você acabará com um dos seguintes provedores:

* China Telecom global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW global Ltd.
* Hong Kong Telecom Ltd.

Dependendo do provedor e das suas necessidades, agora você precisa comprar um dos seguintes serviços de conectividade de rede para interconectar suas ramificações na China.

* Uma rede MPLS/IPVPN 
* Uma WAN definida pelo software (SDWAN)
* Acesso dedicado à Internet

Em seguida, você precisa concordar com esse provedor para fazer uma análise para a rede global da Microsoft e sua borda Entrada na Rede de Hong Kong, não em Pequim ou em Xangai. Nesse caso, Hong Kong é muito importante devido à sua conexão física e local à China.

Embora a maioria dos clientes considere o uso de Cingapura para interconexão é o melhor caso, pois parece mais próxima da China ao examinar o mapa, isso não é verdade. Quando você segue mapas de fibra de rede, quase todas as conexões de rede passam por Pequim, Xangai e Hong Kong. Isso torna o Hong Kong uma melhor opção de localização para interconectar à China.

Dependendo do provedor, você poderá obter diferentes ofertas de serviço. A tabela a seguir mostra um exemplo de provedores e o serviço que eles oferecem, com base nas informações no momento em que este artigo foi escrito.

| Serviço | Exemplos de provedor |
| --- | --- |
| Rede MPLS/IPVPN |PCCW, China Telecom global |
|SDWAN| PCCW, China Telecom global|
| Acesso dedicado à Internet | PCCW, Hong Kong Telecom, China Mobile|

Com seu provedor, você pode concordar sobre quais das duas soluções a seguir podem ser usadas para acessar o backbone global da Microsoft:

* Obter um Microsoft Azure ExpressRoute encerrado em Hong Kong. Esse seria o caso para o uso de MPLS/IPVPN. Atualmente, somente o único provedor de licença ICP com ExpressRoute para Hong Kong é a China Telecom global. No entanto, eles também podem se comunicar com os outros provedores se utilizarem provedores de Exchange de nuvem como o Megaport ou a internuvem. Para obter mais informações, consulte [provedores de conectividade do ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Usando um acesso de Internet dedicado diretamente em um dos seguintes pontos de troca de Internet ou usando uma interconexão de rede privada.

A lista a seguir mostra as trocas de Internet possíveis em Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Ao usar essa conexão, seu próximo salto de BGP para os serviços da Microsoft deve ser um número de sistema autônomo da Microsoft (como #) 8075. Se você usar um único local ou solução SDWAN, essa seria a opção de conexão.

De qualquer forma, ainda recomendamos que você tenha uma segunda análise de Internet regular para o continente chinês. Isso é para dividir o tráfego entre o tráfego corporativo para serviços de nuvem, como Microsoft 365 e o Azure, e o tráfego de Internet regulamentado por lei.

Uma arquitetura de rede compatível na China poderia ser semelhante ao exemplo a seguir:

![Várias ramificações](./media/interconnect-china/multi-branch.png)

Neste exemplo, tendo uma interconexão com o Microsoft global Entrada na Rede Hong Kong, agora você pode começar a aproveitar a [arquitetura de trânsito global da WAN virtual do Azure](virtual-wan-global-transit-network-architecture.md) e serviços adicionais, como o Hub Wan virtual seguro do Azure, para consumir serviços e interconexão com seus branches e Datacenter fora da China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicação de Hub para Hub

Nesta seção, usamos a comunicação de Hub para Hub de WAN virtual para interconexão. Nesse cenário, você cria um novo recurso de Hub de WAN virtual para se conectar a um hub de WAN virtual em Hong Kong, outras regiões que preferir, uma região onde você já tem recursos do Azure ou onde deseja se conectar.

Uma arquitetura de exemplo poderia ser semelhante ao seguinte exemplo:

![Exemplo de WAN](./media/interconnect-china/sample.png)

Neste exemplo, as ramificações da China conectam-se ao Azure cloud China e entre si usando conexões VPN ou MPLS. As ramificações que precisam ser conectadas aos serviços globais usam serviços MPLS ou baseados na Internet que estão conectados diretamente a Hong Kong. Se você quiser usar o ExpressRoute em Hong Kong, bem como na outra região, será necessário configurar o [expressroute alcance global](../expressroute/expressroute-global-reach.md) para interconectar ambos os circuitos do expressroute.

O ExpressRoute Alcance Global não está disponível em algumas regiões. Se você precisar interconectar com o Brasil ou com a Índia, por exemplo, será necessário aproveitar os [provedores de Exchange de nuvem](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) para fornecer os serviços de roteamento.

A figura a seguir mostra os dois exemplos para esse cenário.

![Alcance Global](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Análise de Internet segura para o Office 365

Outra consideração é a segurança da rede, bem como o registro em log para o ponto de entrada entre a China e o componente de backbone de WAN virtual estabelecido e o backbone do cliente. Na maioria dos casos, há a necessidade de fazer uma busca na Internet em Hong Kong para acessar diretamente a rede do Microsoft Edge e, com isso, os servidores de porta frontal do Azure usados para serviços de Microsoft 365.

Para ambos os cenários com a WAN virtual, você aproveitaria o [Hub seguro da WAN virtual do Azure](../firewall-manager/secured-virtual-hub.md). Usando o Gerenciador de firewall do Azure, você pode alterar um hub de WAN virtual regular para um hub protegido e, em seguida, implantar e gerenciar um firewall do Azure dentro desse Hub.

A figura a seguir mostra um exemplo desse cenário:

![Análise da Internet para tráfego de serviços da Web e da Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Fluxos de arquitetura e de tráfego

Dependendo da sua escolha em relação à conexão com o Hong Kong, a arquitetura geral pode mudar um pouco. Esta seção mostra três arquiteturas disponíveis em diferentes combinações com VPN ou SDWAN e/ou ExpressRoute.

Todas essas opções fazem uso do Hub seguro de WAN virtual do Azure para conectividade de M365 direta em Hong Kong. Essas arquiteturas também dão suporte aos requisitos de conformidade do [Office 365 de várias regiões geográficas](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) e mantêm esse tráfego próximo ao próximo local de porta frontal do Office 365. Como resultado, também é uma melhoria para o uso de Microsoft 365 da China.

Ao usar a WAN virtual do Azure junto com as conexões com a Internet, cada conexão pode se beneficiar de serviços adicionais como o [Microsoft Azure os serviços de emparelhamento (mapas)](https://docs.microsoft.com/azure/peering-service/about). O MAPS foi criado para otimizar o tráfego de entrada para a rede global da Microsoft de provedores de serviços de Internet de terceiros.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opção 1: SDWAN ou VPN

Esta seção discute um design que usa SDWAN ou VPN para Hong Kong e para outras ramificações. Essa opção mostra o uso e o fluxo de tráfego ao usar a conexão de Internet pura em ambos os sites do backbone de WAN virtual. Nesse caso, a conexão é trazida para Hong Kong usando acesso dedicado à Internet ou uma solução SDWAN do provedor ICP. Outros branches também usam soluções de Internet ou SDWAN puras.

![Tráfego da China para Hong Kong](./media/interconnect-china/china-traffic.png)

Nessa arquitetura, cada site é conectado à rede global da Microsoft usando a VPN e a WAN virtual do Azure. O tráfego entre os sites e o Hong Kong é transmitido Trough da rede da Microsoft e usa apenas a conexão de Internet regular na última quilometragem.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opção 2: ExpressRoute e SDWAN ou VPN

Esta seção discute um design que usa o ExpressRoute em Hong Kong e outros branches com branches de VPN/SDWAN. Essa opção mostra o uso do e do ExpressRoute encerrado em Hong Kong e em outros branches conectados por meio de SDWAN ou VPN. Atualmente, o ExpressRoute em Hong Kong está limitado a uma pequena lista de provedores, que você pode encontrar na lista de [parceiros de rota expressa](../expressroute/expressroute-locations-providers.md#global-commercial-azure).

![China para tráfego de Rae de Hong Kong](./media/interconnect-china/expressroute.png)

Também há opções para encerrar o ExpressRoute da China, por exemplo, na Coreia do Sul ou no Japão. Mas, considerando a conformidade, a regulamentação e a latência, Hong Kong é a melhor opção.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opção 3: somente ExpressRoute

Esta seção aborda um design no qual o ExpressRoute é usado para Hong Kong e outras ramificações. Essa opção mostra a interconexão usando o ExpressRoute em ambas as extremidades. Aqui, você tem um fluxo de tráfego diferente do outro. O tráfego de Microsoft 365 fluirá para o Hub seguro da WAN virtual do Azure e daí para a rede Microsoft Edge e a Internet.

O tráfego que vai para os branches interconectados ou deles para os locais na China seguirá uma abordagem diferente dentro dessa arquitetura. Atualmente, a WAN virtual não dá suporte a ExpressRoute para trânsito de ExpressRoute. O tráfego aproveitará o ExpressRoute Alcance Global ou a interconexão de terceiros sem passar o Hub WAN virtual. Ele fluirá diretamente de um Microsoft Enterprise Edge (MSEE) para outro.

![Alcance Global do ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

No momento, o ExpressRoute Alcance Global não está disponível em todos os países, mas você pode configurar uma solução usando a WAN virtual do Azure.

Você pode, por exemplo, configurar um ExpressRoute com emparelhamento da Microsoft e conectar um túnel VPN por meio desse emparelhamento à WAN virtual do Azure. Agora, você habilitou, novamente, o trânsito entre a VPN e o ExpressRoute sem Alcance Global e o provedor e o serviço de terceiros, como o Megaport Cloud.

## <a name="next-steps"></a>Próximas etapas

Veja os seguintes artigos para obter mais informações:

* [Arquitetura de rede de trânsito global com a WAN virtual do Azure](virtual-wan-global-transit-network-architecture.md)

* [Criar um hub WAN virtual](virtual-wan-site-to-site-portal.md)

* [Configurar um hub seguro de WAN virtual](../firewall-manager/secure-cloud-network.md)

* [Visão geral da visualização do serviço de emparelhamento do Azure](https://docs.microsoft.com/azure/peering-service/about)
