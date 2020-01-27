---
title: 'Locais e provedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: Este artigo fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e de como se conectar a regiões do Azure. Classificado por local.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: cherylmc
ms.openlocfilehash: cbddd912831247e30f2913414c6dfa13131dde99
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719850"
---
# <a name="expressroute-partners-and-peering-locations"></a>Locais de emparelhamento e parceiros do ExpressRoute

> [!div class="op_single_selector"]
> * [Locais por provedor](expressroute-locations.md)
> * [Provedores por local](expressroute-locations-providers.md)


As tabelas neste artigo fornecem informações sobre os locais e a cobertura geográfica do ExpressRoute, provedores de conectividade do ExpressRoute e SIs (integradores de sistema do ExpressRoute).

> [!Note]
> As regiões do Azure e locais de ExpressRoute são dois conceitos distintos e diferentes, entender a diferença entre os dois é essencial para explorar a conectividade de rede híbrida do Azure. 
>
>

## <a name="azure-regions"></a>Regiões do Azure
As regiões do Azure são data centers globais em que os recursos de computação, rede e armazenamento do Azure estão localizados. Ao criar um recurso do Azure, um cliente precisa selecionar um local de recurso. O local do recurso determina qual Datacenter do Azure (ou zona de disponibilidade) o recurso é criado.

## <a name="expressroute-locations"></a>Locais do ExpressRoute
Locais de ExpressRoute (às vezes chamados de locais de emparelhamento ou de encontro-me – localizações) são instalações de colocalização em que os dispositivos Microsoft Enterprise Edge (MSEE) estão localizados. Os locais de ExpressRoute são o ponto de entrada para a rede da Microsoft – e são distribuídos globalmente, fornecendo aos clientes a oportunidade de se conectar à rede da Microsoft em todo o mundo. Esses locais são onde os parceiros do ExpressRoute e os clientes do ExpressRoute Direct emitem conexões cruzadas com a rede da Microsoft. Em geral, o local do ExpressRoute não precisa corresponder à região do Azure. Por exemplo, um cliente pode criar um circuito do ExpressRoute com o local do recurso *leste dos EUA*, no local de emparelhamento de *Seattle* .

Você terá acesso aos serviços do Azure em todas as regiões dentro de uma região geopolítica se estiver conectado a pelo menos um local de ExpressRoute dentro da região geopolítica. 

## <a name="locations"></a>Regiões do Azure para locais de ExpressRoute em uma região geopolítica
A tabela a seguir fornece um mapa das regiões do Azure para locais de ExpressRoute em uma região geopolítica.

| **Região Geopolítica** | **Regiões do Azure** | **Locais de ExpressRoute** |
| --- | --- | --- |
| **Governo da Austrália** | Austrália Central, Austrália Central 2 |Canberra, Canberra2 |
| **Europa** | França central, sul da França, Norte da Alemanha, Centro-oeste da Alemanha, Europa Setentrional, EUA, leste da Noruega, oeste da Noruega, Norte da Suíça, Oeste da Suíça, oeste do Reino Unido, sul do Reino Unido, Europa Ocidental |Amsterdã, Amsterdam2, Copenhague, Dublin, Frankfurt, Geneva, Londres, London2, Marselha, Milão, Munique, Newport (Gales), Oslo, Paris, Stavanger, Estocolmo, Zurique, Munique |
| **América do Norte** | Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, Centro dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA, Centro-Oeste dos EUA, Centro do Canadá, Leste do Canadá |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova York, San Antonio, Seattle, vale do silício, silício Valley2, Washington DC, Washington DC2, Montreal, cidade de Quebec, Toronto |
| **Ásia** | Leste da Ásia, Sudeste Asiático |Rae de Hong Kong, Jacarta, Kuala Lumpur, Cingapura, Cingapura2, Taipé |
| **Índia** | Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Oceânia** | Sudeste da Austrália, Leste da Austrália |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Coreia do Sul** | Coreia Central, Sul da Coreia |Busan, Seul|
| **DOS EAU** | EAU Central, Norte dos EAU | Dubai, Dubai2 |
| **África do Sul** | Oeste da África do Sul, norte da África do Sul |Cidade do Cabo, Joanesburgo |
| **América do Sul** | Sul do Brasil |São Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões do Azure e limites de geopolítica para nuvens nacionais
A tabela a seguir fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região Geopolítica** | **Regiões do Azure** | **Locais de ExpressRoute** |
| --- | --- | --- |
| **Nuvem do Governo dos EUA** |Gov. EUA - Arizona, US Gov Iowa, US Gov - Texas, US Gov Virginia, US DoD Central, US DoD Leste  |Chicago, Dallas, Nova York, Phoenix, San Antonio, Seattle, Vale do Silício, Washington DC |
| **Leste da China** |Leste da China, Leste da China2 |Shanghai, Shanghai2 |
| **Norte da China** |Norte da China, Norte da China2 |Beijing, Beijing2 |
| **Alemanha** |Alemanha Central, Alemanha Oriental |Berlim+, Frankfurt |

Não há suporte para conectividade entre regiões geopolíticas no SKU de ExpressRoute padrão. Você precisará habilitar o complemento premium de ExpressRoute para dar suporte a conectividade global. Não há suporte a conectividade para ambientes de nuvem nacionais. Você pode trabalhar com seu provedor de conectividade se surgir necessidade de fazê-lo.

## <a name="partners"></a>Provedores de conectividade do ExpressRoute

A tabela a seguir mostra os locais de conectividade e os provedores de serviço para cada local. Se você quiser exibir provedores de serviços e os locais para os quais podem fornecer serviço, confira [Locais pelo provedor de serviços](expressroute-locations.md).

* As **regiões locais do Azure** são aquelas que o [ExpressRoute local](expressroute-faqs.md) em cada local de emparelhamento pode acessar. **n/a** indica que o ExpressRoute local não está disponível nesse local de emparelhamento.

* **Zona** refere-se aos [preços](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Azure comercial global
| **Localidade** | **Endereço** | **Zona** | **Regiões locais do Azure** | **ER direto** | **Provedores de serviço** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdã** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Oeste da Europa | 10G, 100G | Redes aryaka Networks, em & T netbonde, British Telecom, Colt, Equinix, euNetworks, GÉANT, entre nuvens, Interxion, KPN, IX REACH, comunicações de nível 3, Megaport, comunicações de NTT, Orange, Tata Communications, Telefonica +, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdã2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Oeste da Europa | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, laranja, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | N/D | N/D | Equinix, Megaport |
| **Auckland** | [Albany Vocus de NZ de grupo](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | N/D | Database | Devoli, Kordia, Megaport, Spark NZ, Vocus grupo NZ |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Sul da Coreia | N/D | LG CNS |
| **Camberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central | 10G, 100G | CDC |
| **Camberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central 2| 10G, 100G | CDC |
| **Cidade do Cabo** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Oeste da África do Sul | Database | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Sul da Índia | Database | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Sul da Índia | N/D | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Centro-Norte dos EUA | 10G, 100G | Redes aryaka Networks, em & T netbonde, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, entre Cloud, Internet2, comunicações de nível 3, Megaport, PacketFabric, PCCW global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenhague** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | N/D | Database | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | N/D | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Centro-Oeste dos EUA | N/D | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Norte dos EAU | N/D | Etisalat dos EAU |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Norte dos EAU | N/D | du datamena, Megaport, laranja, Orixcom |
| **Dublim** | [DB3 Equinix](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa Setentrional | 10G, 100G | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Centro-oeste da Alemanha | 10G, 100G | Colt, CIX, GEANT, Interxion, Megaport, laranja, operadora DE Telia |
| **Estrutura** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Oeste da Suíça | 10G, 100G | Equinix |
| **Rae de Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Leste da Ásia | N/D | Redes aryaka Networks, British Telecom, CenturyLink Cloud Connect, diretor de telecomunicações, China Telecom global, Equinix, internuvem, Megaport, comunicação de NTT, laranja, PCCW global Limited, Tata Communications, Telia Carrier, Verizon |
| **Jacarta** | Indonésia Telkom | 4 | N/D | Database | |
| **Joanesburgo** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Norte da África do Sul | Database | British Telecom, soluções de Internet – conexão em nuvem, Liquid Telecom, laranja, teraco |
| **Kuala Lumpur** | [DotCom de tempo Menara objetivos](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | N/D | N/D | TIME dotCom |
| **Las Vegas** | [Switch LV](https://www.switch.com/las-vegas) | 1 | N/D | N/D | CenturyLink Cloud Connect, Megaport |
| **Londres** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Sul do Reino Unido | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telecity Group, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse norte dois](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Sul do Reino Unido | 10G, 100G | Colt, IX REACH, Equinix, Megaport, The Telehouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | N/D | N/D | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, transtelco, Zayo |
| **Marselha** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Sul da França | N/D | CIX, GEANT, Interxion, rede Jaguar |
| **Melbourne** | [M1 NextDC](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Sudeste da Austrália | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | N/D | Database | C3ntro, Equinix, Megaport, Neutrona Networks |
| **Milão** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | N/D | Database | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | N/D | N/D | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Índia Ocidental | N/D | CloudXchange global (GCX), dependência Jio, Sify, comunicações Tata, Verizon |
| **Mumbai2** | Airtel | 2 | Índia Ocidental | N/D | Airtel, Sify, Vodafone Idea |
| **Munique** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | N/D | 10G, 100G | |
| **Nova Iorque** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | N/D | N/D | CenturyLink Cloud Connect, Colt, Coresite, Equinix, MultiCloud, Megaport, pacote, Zayo |
| **Newport (País de Gales)** | [Dados da Próxima Geração](https://www.nextgenerationdata.co.uk) | 1 | Oeste do Reino Unido | N/D | Telecomunicações britânicas, Colt, comunicações de nível 3, dados da próxima geração |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Oeste do Japão | 10G, 100G | Colt, Equinix, Internet Initiative Japão Inc.-IIJ, comunicação NTT, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Leste da Noruega | 10G, 100G | Megaport, Telenor, Telia Carrier |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | França Central | N/D | CenturyLink Cloud Connect, Colt, Equinix, entre Cloud, Interxion, laranja, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | N/D | Database | Megaport, NextDC |
| **Cidade de Quebec** | [Privilegiando](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Leste do Canadá | N/D | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Centro-Sul dos EUA | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **São Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Sul do Brasil | N/D | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Oeste dos EUA 2 | 10G, 100G | Redes aryaka Networks, Equinix, comunicações de nível 3, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Coreia Central | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Vale do Silício** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Oeste dos EUA | 10G, 100G | Redes aryaka networkss, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, entre nuvens, Internet2, IX REACH, pacote, PacketFabric, comunicações de nível 3, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Valley2 de silício** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Oeste dos EUA | 10G, 100G | Colt, Coresite | 
| **Singapura** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Sudeste Asiático | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Cingapura2** | [Opção global Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Sudeste Asiático | 10G, 100G | Colt, Épsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Montanhas verdes DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Oeste da Noruega | 10G, 100G | |
| **Estocolmo** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | N/D | Database | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Leste da Austrália | 10G, 100G | AARNet, em & T netbonde, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Leste da Austrália | 10G, 100G | NextDC |
| **Taipé** | Chief Telecom | 2 | N/D | Database | Diretor de telecomunicações, FarEasTone |
| **Tóquio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Leste do Japão | 10G, 100G | Aryaka Networks Networks, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japão Inc.-IIJ, Megaport, NTT Communications, NTT leste, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canadá Central | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington, D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Leste dos EUA, leste dos EUA 2 | 10G, 100G | Redes aryaka Networks, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, entre Cloud, IX REACH, comunicações de nível 3, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES , Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Leste dos EUA, leste dos EUA 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **Zurique** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Norte da Suíça | N/D | Internuvem, Interxion, Megaport, Swisscom |

 **+** indica que haverá em breve

### <a name="national-cloud-environments"></a>Ambientes de nuvem nacionais

As nuvens nacionais do Azure são isoladas umas das outras e do Azure comercial global. O ExpressRoute para uma nuvem do Azure não pode se conectar às regiões do Azure nos outros.

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Localidade** | **Endereço** | **Regiões locais do Azure**| **ER direto** | **Provedores de serviço** |
| --- | --- | --- | --- | --- |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | N/D | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | N/D | 10G, 100G | Equinix, Megaport, Verizon |
| **Nova Iorque** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | N/D | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov - Arizona | N/D | EM & T netbonde, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov - Texas | N/D | CenturyLink Cloud Connect, Megaport |
| **Vale do Silício** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | N/D | 10G, 100G | EM & T, Equinix, comunicações de nível 3, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | N/D | N/D | Equinix, Megaport |
| **Washington, D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD Leste, US Gov-Virgínia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Localidade** | **Provedores de serviço** |
| --- | --- |
| **Pequim** |China Telecom |
| **Beijing2** | Telecomunicações da China, GDS |
| **Xangai** |China Telecom |
| **Shanghai2** | Telecomunicações da China, GDS |

Para saber mais, consulte [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Alemanha
| **Localidade** | **Provedores de serviço** |
| --- | --- |
| **Berlim** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Conectividade por meio de provedores do Exchange
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

## <a name="connectivity-through-satellite-operators"></a>Conectividade por meio de operadores satélite
Se você for remoto e não tiver conectividade de fibra ou se quiser explorar outras opções de conectividade, poderá verificar os seguintes operadores de satélite. 

* Intelsat
* [EAS](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Conectividade por meio de provedores de serviço adicionais
| **Localidade** | **Exchange** | **Provedores de conectividade** |
| --- | --- | --- |
| **Amsterdã** | Equinix, Interxion, comunicações de nível 3 | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, The Golfo Bridge International, Kalaam Telecom Bahrein B. S. C, MainOne, Nianet, POST Telecom Luxemburgo, Proximus, TDC erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle de coroa
| **Cidade do Cabo** | Teraco | MTN |
| **Chicago** | Equinix| Coroa Castle, espectro Enterprise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, coroa Castle, dados encontrados, espectro empresarial, transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburgo** | Equinix | Cinia |
| **Rae de Hong Kong** | Equinix | Chief, Macroview Telecom |
| **Joanesburgo** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Coroa Castle, espectro empresarial, transtelco |
| **Madrid** | Nível3 | Zertia |
| **Montreal** | Cologix, Equinix | Tecnologias de portão da porta, Inc. Aptum, Rogers, Zirro |
| **Nova Iorque** |Equinix, Megaport | Altice Business, coroa Castle, espectro Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Cidade de Quebec** | Megaport | Fibrenoire |
| **São Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Vale do Silício** |Coresite, Equinix | Cox Business, espectro Enterprise, windstream, X2nsat Inc. |
| **Singapura** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, telecomunicações de Épsilon limitado, LGA de telecomunicações, rodovia de informações United (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tóquio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Porta-portão Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington, D.C.** |Equinix | Altice Business, BICS, Cox Business, coroa Castle, GTT Communications Inc, Épsilon Communications Limited, Masergy, windstream |

## <a name="expressroute-system-integrators"></a>Integradores de sistema de ExpressRoute
Habilitar a conectividade privada para atender às suas necessidades pode ser desafiador, dependendo da escala de sua rede. Você pode trabalhar com qualquer dos integradores de sistema listados na tabela a seguir para ajudá-lo com integração ao ExpressRoute.

| **Continente** | **Integradores do sistema** |
| --- | --- |
| **Ásia** |Avanade Inc., OneAs1a |
| **Austrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **América do Norte** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **América do Sul** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Próximos passos
* Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
