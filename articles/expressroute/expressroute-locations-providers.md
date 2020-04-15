---
title: 'Locais e provedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: Este artigo fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e de como se conectar a regiões do Azure. Classificado por local.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0133322eab9a554cf28c0a90fbadf9afe6fbcd64
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314277"
---
# <a name="expressroute-partners-and-peering-locations"></a>Locais de emparelhamento e parceiros do ExpressRoute

> [!div class="op_single_selector"]
> * [Localizações por provedor](expressroute-locations.md)
> * [Provedores por local](expressroute-locations-providers.md)


As tabelas deste artigo fornecem informações sobre a cobertura geográfica e localizações da ExpressRoute, provedores de conectividade ExpressRoute e SIs (ExpressRoute System Integrators).

> [!Note]
> As regiões azure e as localizações do ExpressRoute são dois conceitos distintos e diferentes, entender a diferença entre os dois é fundamental para explorar a conectividade de rede híbrida do Azure. 
>
>

## <a name="azure-regions"></a>Regiões do Azure
As regiões do Azure são data centers globais onde os recursos de computação, rede e armazenamento do Azure estão localizados. Ao criar um recurso do Azure, um cliente precisa selecionar um local de recurso. A localização do recurso determina em qual data center do Azure (ou zona de disponibilidade) o recurso é criado.

## <a name="expressroute-locations"></a>Localizações do ExpressRoute
Os locais do ExpressRoute (às vezes chamados de locais de peering ou meet-me-locations) são instalações de co-localização onde os dispositivos Microsoft Enterprise edge (MSEE) estão localizados. As localizações do ExpressRoute são o ponto de entrada da rede da Microsoft – e são distribuídas globalmente, proporcionando aos clientes a oportunidade de se conectar à rede da Microsoft em todo o mundo. Esses locais são onde os parceiros ExpressRoute e ExpressRoute Direct emitem conexões cruzadas à rede da Microsoft. Em geral, a localização do ExpressRoute não precisa corresponder à região do Azure. Por exemplo, um cliente pode criar um circuito ExpressRoute com a localização de recursos *East US*, no local do *Seattle* Peering.

Você terá acesso aos serviços do Azure em todas as regiões dentro de uma região geopolítica se estiver conectado a pelo menos um local de ExpressRoute dentro da região geopolítica. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Regiões do Azure para locais expressRoute dentro de uma região geopolítica
A tabela a seguir fornece um mapa das regiões do Azure para locais de ExpressRoute em uma região geopolítica.

| **Região geopolítica** | **Regiões azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Governo da Austrália** | Austrália Central, Austrália Central 2 |Canberra, Canberra2 |
| **Europa** | França Central, França Sul, Alemanha Norte, Alemanha Centro-Oeste, Norte da Europa, Noruega Leste, Noruega Oeste, Suíça Norte, Suíça Oeste, Reino Unido Oeste, Reino Unido Sul, Europa Ocidental |Amsterdã, Amsterdã2, Copenhague, Dublin, Frankfurt, Genebra, Londres, Londres2, Marselha, Milão, Munique, Newport(País de Gales), Oslo, Paris, Stavanger, Estocolmo, Zurique, Munique |
| **América do Norte** | Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, Centro dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA, Centro-Oeste dos EUA, Centro do Canadá, Leste do Canadá |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova York, San Antonio, Seattle, Vale do Silício, Vale do Silício2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Ásia** | Leste da Ásia, Sudeste Asiático | Bangkok, Hong Kong, Hong Kong2, Jacarta, Kuala Lumpur, Cingapura, Cingapura2, Taipei |
| **Índia** | Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | Oeste do Japão, Leste do Japão |Osaka, Tóquio, Tóquio2 |
| **Oceania** | Sudeste da Austrália, Leste da Austrália |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Coreia do Sul** | Coreia Central, Sul da Coreia |Busan, Seul|
| **EAU** | Emirados Dos Emirados Central, Emirados Americanos | Dubai |
| **África do Sul** | África do Sul Oeste, África do Sul Norte |Cidade do Cabo, Joanesburgo |
| **América do Sul** | Sul do Brasil |São Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões azuis e fronteiras geopolíticas para nuvens nacionais
A tabela a seguir fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Nuvem do Governo dos EUA** |Gov. EUA - Arizona, US Gov Iowa, US Gov - Texas, US Gov Virginia, Região Central do US DoD, Leste do US DoD  |Atlanta, Chicago, Dallas, Nova Iorque, Phoenix, San Antonio, Seattle, Vale do Silício, Washington DC |
| **Leste da China** |Leste da China, Leste da China2 |Shanghai, Shanghai2 |
| **Norte da China** |Norte da China, Norte da China2 |Beijing, Beijing2 |
| **Alemanha** |Alemanha Central, Alemanha Oriental |Berlim+, Frankfurt |

Não há suporte para conectividade entre regiões geopolíticas no SKU de ExpressRoute padrão. Você precisará habilitar o complemento premium de ExpressRoute para dar suporte a conectividade global. Não há suporte a conectividade para ambientes de nuvem nacionais. Você pode trabalhar com seu provedor de conectividade se surgir necessidade de fazê-lo.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Provedores de conectividade do ExpressRoute

A tabela a seguir mostra os locais de conectividade e os provedores de serviço para cada local. Se você quiser exibir provedores de serviços e os locais para os quais podem fornecer serviço, confira [Locais pelo provedor de serviços](expressroute-locations.md).

* **As regiões locais do Azure** são as que o [ExpressRoute Local](expressroute-faqs.md) em cada local de peering pode acessar. **n/a** indica que o ExpressRoute Local não está disponível nesse local de peering.

* **Zone** refere-se a [preços](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Comercial global Azure
| **Localidade** | **Endereço** | **Zona** | **Regiões locais do Azure** | **ER Direct** | **Prestadores de serviços** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdã** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa Ocidental | 10g, 100g | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdã2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa Ocidental | 10g, 100g | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/d | n/d | Equinix, Megaport |
| **Auckland** | [Grupo Vocus NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/d | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Bangkok** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n/d | 10G | AIS |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Sul da Coreia | n/d | LG CNS |
| **Camberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central | 10g, 100g | CDC |
| **Camberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central 2| 10g, 100g | CDC |
| **Cidade do Cabo** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | África do Sul Ocidental | 10G | BCX, Soluções de Internet - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Sul da Índia | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Sul da Índia | n/d | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Centro-Norte dos EUA | 10g, 100g | Aryaka Networks, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenhague** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/d | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/d | 10g, 100g | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Centro-Oeste dos EUA | n/d | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Norte dos EAU | n/d | Emirados Etisalat |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Norte dos EAU | n/d | du datamena, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Norte da Europa | 10g, 100g | Colt, eir, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Alemanha Centro-Oeste | 10g, 100g | CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Genebra** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Suíça Ocidental | 10g, 100g | Equinix, Megaport |
| **RAE de Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Leste da Ásia | n/d | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hong Kong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | n/d | 10G | |
| **Jacarta** | Telkom Indonésia | 4 | n/d | 10G | |
| **Joanesburgo** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Norte da África do Sul | 10G | British Telecom, Soluções de Internet - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/d | n/d | TIME dotCom |
| **Las Vegas** | [Trocar LV](https://www.switch.com/las-vegas) | 1 | n/d | n/d | CenturyLink Cloud Connect, Megaport |
| **Londres** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Sul do Reino Unido | 10g, 100g | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Sul do Reino Unido | 10g, 100g | CenturyLink Cloud Connect, Colt, IX Reach, Equinix, Megaport, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/d | n/d | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **Marselha** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Sul da França | n/d | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Sudeste da Austrália | 10g, 100g | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/d | 10G | C3ntro, Equinix, Megaport, Neutrona Networks |
| **Milão** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/d | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/d | 10g, 100g | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Oeste da Índia | n/d | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Oeste da Índia | n/d | Airtel, Sify, Vodafone Idea |
| **Munique** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/d | 10g, 100g | |
| **Nova Iorque** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/d | n/d | CenturyLink Cloud Connect, Colt, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (País de Gales)** | [Dados da Próxima Geração](https://www.nextgenerationdata.co.uk) | 1 | Oeste do Reino Unido | n/d | British Telecom, Colt, Comunicação nível 3, dados de próxima geração |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Oeste do Japão | 10g, 100g | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Leste da Noruega | 10g, 100g | Megaport, Telenor, Telia Carrier |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | França Central | 10g, 100g | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/d | 10G | Megaport |
| **Cidade de Quebec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Leste do Canadá | n/d | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Centro-Sul dos Estados Unidos | 10g, 100g | CenturyLink Cloud Connect, Megaport |
| **São Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Sul do Brasil | n/d | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Oeste dos EUA 2 | 10g, 100g | Aryaka Networks, Equinix, Comunicação Nível 3, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Coreia Central | 10g, 100g | KINX, KT, LG CNS, Sejong Telecom |
| **Vale do Silício** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Oeste dos EUA | 10g, 100g | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Vale do Silício2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Oeste dos EUA | 10g, 100g | Colt | 
| **Singapura** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Sudeste Asiático | 10g, 100g | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Cingapura2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Sudeste Asiático | 10g, 100g | China Unicom Global, Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Montanha Verde DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Noruega Ocidental | 10g, 100g | |
| **Estocolmo** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/d | 10G | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Leste da Austrália | 10g, 100g | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Rio Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Leste da Austrália | 10g, 100g | Megaport |
| **Taipé** | Chief Telecom | 2 | n/d | 10G | Chefe de Telecomunicações, FarEasTone |
| **Tóquio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Leste do Japão | 10g, 100g | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tóquio2** | [EM TÓQUIO](https://www.attokyo.com/) | 2 | Leste do Japão | 10g, 100g | EM TÓQUIO |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canadá Central | 10g, 100g | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Cologix | 10g, 100g | |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Leste dos EUA, Leste dos EUA 2 | 10g, 100g | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Canteiro Desiano](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Leste dos EUA, Leste dos EUA 2 | 10g, 100g | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **Zurique** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Suíça Norte | 10g, 100g | Intercloud, Interxion, Megaport, Swisscom |

 **+** denota em breve

### <a name="national-cloud-environments"></a>Ambientes de nuvem nacionais

Nuvens nacionais azure são isoladas umas das outras e do comercial global Azure. O ExpressRoute para uma nuvem Azure não pode se conectar às regiões do Azure nas outras.

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Localidade** | **Endereço** | **Regiões locais do Azure**| **ER Direct** | **Prestadores de serviços** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/d | 10g, 100g | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/d | 10g, 100g | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/d | 10g, 100g | Equinix, Megaport, Verizon |
| **Nova Iorque** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/d | 10g, 100g | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | Governo dos EUA do Arizona | n/d | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | Governo dos EUA do Texas | n/d | CenturyLink Cloud Connect, Megaport |
| **Vale do Silício** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/d | 10g, 100g | AT&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/d | n/d | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | DoD East dos EUA, Eua Gov Virginia | 10g, 100g | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Localidade** | **Prestadores de serviços** |
| --- | --- |
| **Pequim** |China Telecom |
| **Beijing2** | China Telecom, China Unicom, GDS |
| **Xangai** |China Telecom |
| **Shanghai2** | China Telecom, GDS |

Para saber mais, consulte [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Alemanha
| **Localidade** | **Prestadores de serviços** |
| --- | --- |
| **Berlim** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Conectividade através de provedores de Exchange
Se seu provedor de conectividade não estiver listado em seções anteriores, você ainda pode criar uma conexão.

* Verifique com seu provedor de conectividade para ver se eles estão conectados a qualquer um dos Exchanges na tabela acima. Você pode verificar os links a seguir para obter mais informações sobre os serviços oferecidos por provedores do Exchange. Vários provedores de conectividade já estão conectados a Exchanges de Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Faça com que seu provedor de conectividade estenda sua rede para o local de emparelhamento de sua escolha.
  * Certifique-se de que seu provedor de conectividade estenda sua conectividade de maneira altamente disponível para que não exista nenhum ponto de falha.
* Solicite um circuito do ExpressRoute com o Exchange como o provedor de conectividade para conectar-se à Microsoft.
  * Siga as etapas em [Criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

## <a name="connectivity-through-satellite-operators"></a>Conectividade através de operadores de satélite
Se você é remoto e não tem conectividade de fibra ou quer explorar outras opções de conectividade, você pode verificar os seguintes operadores de satélite. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Conectividade através de provedores de serviços adicionais
| **Localidade** | **Exchange** | **Provedores de conectividade** |
| --- | --- | --- |
| **Amsterdã** | Equinix, Interxion, Comunicação nível 3 | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castelo Crown
| **Cidade do Cabo** | Teraco | MTN |
| **Chicago** | Equinix| Castelo da Coroa, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburgo** | Equinix | Cinia |
| **RAE de Hong Kong** | Equinix | Chief, Macroview Telecom |
| **Joanesburgo** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madri** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **Nova Iorque** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Cidade de Quebec** | Megaport | Fibrenoire |
| **São Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Vale do Silício** |Coresite | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapura** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tóquio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Integradores de sistema de ExpressRoute
Habilitar a conectividade privada para atender às suas necessidades pode ser desafiador, dependendo da escala de sua rede. Você pode trabalhar com qualquer dos integradores de sistema listados na tabela a seguir para ajudá-lo com integração ao ExpressRoute.

| **Continente** | **Integradores do sistema** |
| --- | --- |
| **Ásia** |Avanade Inc., OneAs1a |
| **Austrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **América do Norte** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **América do Sul** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre o ExpressRoute, consulte o [FAQ ExpressRoute](expressroute-faqs.md).
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
