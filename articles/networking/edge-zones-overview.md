---
title: Sobre a Azure Edge Zones - Visualização
description: Conheça as ofertas de edge computing da Microsoft.
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 04/02/2020
ms.author: ganesr
ms.openlocfilehash: 2f2311adc231eac4260e26f0d5d1f2115f43d2eb
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618711"
---
# <a name="about-azure-edge-zones---preview"></a>Sobre a Azure Edge Zones - Visualização

O Azure Edge Zones é uma família de ofertas do Microsoft Azure que permite o processamento de dados perto do usuário. Você pode implantar VMs, contêineres e outros serviços selecionados do Azure em Regiões de Borda para atender aos requisitos de baixa latência e alto rendimento dos aplicativos.

Os cenários típicos de casos de uso para Regiões de Borda incluem:

- Comando e controle em tempo real em robótica
- Análise e inferência em tempo real com Inteligência Artificial e Machine Learning
- Visão da Máquina
- Renderização remota para realidade mista e cenários VDI
- Jogos multi-jogadores imersivos
- Streaming de mídia e entrega de conteúdo
- Vigilância e segurança

As Zonas de Borda do Azure vêm em três ofertas discretas:

- Zonas de borda do Azure
- Zonas de borda do Azure com portador
- Zonas de borda privada do Azure

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Zonas de borda do Azure

![Zonas de borda](./media/edge-zones-overview/edge-zones.png "Zonas de borda")

Azure Edge Zones são pequenas extensões de pegada do Azure colocadas em centros populacionais que estão longe das regiões do Azure. As Zonas de Borda do Azure suportam VMs, contêineres e um conjunto seleto de serviços Do Azure que permitem executar aplicativos intensivos de latência e de throughput perto dos usuários finais. As Azure Edge Zones fazem parte da rede global da Microsoft e oferecem conectividade segura, confiável e de alta largura de banda entre aplicativos em execução na End Zone perto do usuário e o conjunto completo de serviços do Azure em execução nas regiões do Azure. As Azure Edge Zones são de propriedade e operadas pela Microsoft e permitem que você use o mesmo conjunto de ferramentas e portal do Azure para gerenciar e implantar serviços nas Zonas de Borda.

Os casos típicos de uso incluem:

- Jogos e streaming de jogos
- Streaming de mídia e entrega de conteúdo
- Análise e inferência em tempo real usando inteligência artificial e machine learning
- Renderização para realidade mista

As Zonas de Borda do Azure estarão disponíveis nos seguintes metros:

- Nova Iorque, NY
- Los Angeles, CA
- Miami, FL

[Entre em contato com a equipe de Zonas de Borda](https://aka.ms/EdgeZones) para obter mais informações.

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Zonas de borda do Azure com portador

![Zonas de borda com portador](./media/edge-zones-overview/edge-carrier.png "Zonas de borda com portador")

As Zonas de Borda do Azure com operadora são pequenas extensões de pegada do Azure colocadas nos data centers das operadoras móveis em centros populacionais. A Azure Edge Zones com infra-estrutura de operadoras é colocada a um salto da rede 5G da operadora móvel, oferecendo latência de sub-10 milissegundos para aplicações de dispositivos móveis. O Azure Edge Zones com a operadora é implantado nos data centers das operadoras móveis e está conectado à rede global da Microsoft. Eles oferecem conectividade segura, confiável e de alta largura de banda entre aplicativos que são executados perto do usuário e o conjunto completo de serviços do Azure em execução nas regiões do Azure. Os desenvolvedores podem usar o mesmo conjunto de ferramentas familiares para construir e implantar serviços nas Zonas de Borda.

Os casos típicos de uso incluem:

- Jogos e streaming de jogos
- Streaming de mídia e entrega de conteúdo
- Análise e inferência em tempo real usando inteligência artificial e machine learning
- Renderização para realidade mista
- Automóveis conectados
- Telemedicina

As Zonas de Borda serão oferecidas em parceria com os seguintes operadores:

- AT&T (Atlanta, Dallas e Los Angeles)

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Zonas de borda privada do Azure

![Zonas de borda privada](./media/edge-zones-overview/private-edge.png "Zonas de borda privada")

Azure Private Edge Zones são pequenas extensões de pegada do Azure colocadas no local. O Azure Private Edge Zones é baseado na plataforma [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) e permite acesso de baixa latência a serviços de computação e armazenamento implantados no local. O Private Edge Zones também permite que você implante funções de rede virtualizadas (VNFs), como núcleos de pacotes móveis, roteadores, firewalls e dispositivos SD-WAN, e aplicativos do ISVS à medida [que os aplicativos gerenciados do Azure](https://azure.microsoft.com/services/managed-applications/) estão lado a lado, juntamente com máquinas virtuais e contêineres no local. O Azure Private Edge Zones vem com uma solução de orquestração nativa na nuvem que permite gerenciar o ciclo de vida das Funções de Rede Virtualizadas (VNF) e aplicativos do portal Azure.

O Azure Private Edge Zones permite que você desenvolva e implante aplicativos no local usando as mesmas ferramentas familiares usadas para construir e implantar aplicativos no Azure. Você também pode executar redes móveis privadas (LTE privado, 5G privado), funções de segurança, como firewalls, e estender suas redes locais em várias filiais e Azure usando dispositivos SD-WAN nos mesmos aparelhos private Edge Zone e gerenciá-los a partir do Azure.

Os casos típicos de uso incluem:

- Comando e controle em tempo real em robótica
- Análise e inferência em tempo real com Inteligência Artificial e Machine Learning
- Visão da Máquina
- Renderização remota para realidade mista e cenários VDI
- Vigilância e segurança

Temos um rico ecossistema de fornecedores de VNF, ISVs e parceiros MSP para permitir soluções de ponta a ponta usando zonas de borda privadas. [Entre em contato com a equipe de Zonas de Borda Privada](https://aka.ms/EdgeZonesPartner) para obter mais informações.

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>Private Edge Zones - Parceiros

![Parceiros de zona de borda privada](./media/edge-zones-overview/partners.png "Parceiros de zona de borda privada")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>VNFs (Virtualized Network Functions, funções de rede virtualizadas)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Núcleo de pacote evoluído virtualizado (vEPC) para redes móveis

- [Redes Afirmadas](https://www.affirmednetworks.com/)
- [Software druida](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Rio Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nuvem de Automação Digital Nokia](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Parceiros de rádio móvel

- [Commscope Ruckus](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>Fornecedores SD-WAN

- [Resmando net](https://netfoundry.io/)
- [NuageNetworks da Nokia](https://www.nuagenetworks.net/)
- [VMware SD-WAN por Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>Fornecedores de roteador

- [Arista](https://www.arista.com/)

[Entre em contato com a equipe do Private Edge Zones](https://aka.ms/EdgeZonesPartner) para obter mais informações sobre como se tornar um parceiro.

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Fornecedores de firewall

- Redes Palo Alto

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>Provedores de Soluções Gerenciadas - Operadores Móveis e Integradores Globais de Sistemas

| SIs e operadores globais | Operadoras móveis |
| --- | --- |
| Amdocs                       | Etisalat             |
| Torre Americana               | Comunicações NTT   |
| Link do Século                 | Proximus             |
| Expeto                       | Rogers               |
| Sem fio federado           | SK Telecom           |
| Infosys                      | Telefonica           |
| Mahindra Tecnológica                | Telstra              |
|        *                     | Vodafone             |

[Entre em contato com a equipe do Private Edge Zones](https://aka.ms/EdgeZonesPartner) para obter mais informações sobre como se tornar um parceiro.

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>Private Edge Zones - Soluções

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Rede móvel privada em zonas de borda privada

![Rede móvel privada em zonas de borda privada](./media/edge-zones-overview/mobile-networks.png "Rede móvel privada em zonas de borda privada")

Agora você pode implantar uma rede móvel privada em zonas de borda privadas. As redes móveis privadas permitem latência ultra baixa, alta capacidade e uma rede sem fio confiável e segura que é necessária para aplicativos críticos de negócios. Redes móveis privadas podem permitir cenários como comando e controle de veículos guiados automatizados (AGV) em um armazém, comunicação em tempo real entre robôs em uma fábrica inteligente e aplicações de realidade aumentada e aplicações de borda de realidade virtual.

A função de rede de núcleo de pacotes evoluído virtualizado (vEPC) forma o cérebro de uma rede móvel privada. Agora você pode implantar um vEPC em Zonas de Borda Privada. Para obter uma lista de parceiros vEPC disponíveis em zonas de borda privadas, consulte [vEPC ISVs](#vEPC).

A implantação de uma solução de rede móvel privada em Zonas de Borda Privada requer outros componentes, como pontos de acesso móvel, cartões SIM e outros VNFs, como roteadores. O acesso a espectro licenciado ou não licenciado é fundamental para a criação de uma rede móvel privada. Além disso, você pode precisar de ajuda com planejamento de RF, layout físico, instalação e suporte. Para obter uma lista de parceiros, consulte [parceiros](#mobile-radio)de rádio móveis .

A Microsoft fornece um ecossistema de parceiros que pode ajudar em todos os aspectos desse processo – desde o planejamento da rede, a compra dos dispositivos necessários, a configuração de hardware, até o gerenciamento da configuração do Azure. Com um conjunto de parceiros validados que estão fortemente integrados com a Microsoft, você pode ter certeza de que a solução será confiável e fácil de usar. Você pode se concentrar em seus cenários principais, enquanto confia na Microsoft e seus parceiros para ajudar com o resto.

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN em zonas de borda privada

![SD-WAN em zonas de borda privada](./media/edge-zones-overview/sd-wan.png "SD-WAN em zonas de borda privada")
 
O SD-WAN como uma tecnologia permite criar WAN (Enterprise-Grade Wide Area Networks, redes de área larga) de nível corporativo com maior largura de banda, acesso de alto desempenho à nuvem, inserção de serviços, confiabilidade, gerenciamento de políticas e ampla visibilidade da rede. O SD-WAN fornece conectividade de filial perfeita orquestrada a partir de controladores centrais redundantes, a um custo menor de propriedade.
O SD-WAN em Private Edge Zones permite que você se afaste do modelo centrado em capex para um modelo SaaS (Software-as-a-service) para reduzir os orçamentos de TI. Você pode usar sua escolha de orquestrador ou controlador de parceiros SD-WAN para habilitar novos serviços e prolí-los em toda a rede imediatamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, entre em contato com as seguintes equipes:

* [Equipe de Zonas de Borda](https://aka.ms/EdgeZones)
* [Equipe private edge zones - para se tornar um parceiro](https://aka.ms/EdgeZonesPartner)
