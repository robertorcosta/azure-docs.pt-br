---
title: Trabalhando remotamente usando serviços de rede Do Azure
description: Esta página descreve como você pode usar os serviços de rede do Azure disponíveis para permitir o trabalho remoto e como mitigar problemas de tráfego resultantes do aumento do número de pessoas trabalhando remotamente.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 78081329b70edff5ed2c728cb2f7c3e562a44e56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346283"
---
# <a name="working-remotely-using-azure-networking-services"></a>Trabalhando remotamente usando serviços de rede Do Azure

>[!NOTE]
> Este artigo descreve como você pode aproveitar os serviços de rede do Azure, a rede Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e mitigar problemas de rede que você pode estar enfrentando por causa da crise do COVID-19.

Este artigo descreve as opções disponíveis às organizações para configurar acesso remoto para seus usuários ou para complementar suas soluções existentes com capacidade adicional durante períodos de pico de utilização. Os arquitetos de rede enfrentam os seguintes desafios:

- Endereçar um aumento na utilização da rede.
- Fornecer conectividade confiável para mais funcionários de sua empresa e clientes.
- Fornecer conectividade a locais remotos em todo o mundo.

Nem todas as redes (por exemplo, WAN privadas e redes centrais corporativas) experimentam congestionamento sustal da carga de trabalhadores remotos de pico. Os gargalos são comumente relatados apenas em redes de banda larga doméstica e gateways VPN de redes locais de corporações.

Os planejadores de rede podem ajudar a aliviar os gargalos e aliviar o congestionamento da rede, tendo em mente que diferentes tipos de tráfego precisam de diferentes prioridades de tratamento de rede e por algum redirecionamento/distribuição de carga inteligente. Por exemplo, o tráfego telemecito em tempo real da interação médico-paciente é de alta importância e sensível ao atraso/nervosismo. Considerando que a replicação do mesmo tráfego entre os armazenamentos não é sensível ao atraso. O tráfego anterior deve ser roteado através do caminho de rede mais ideal com maior qualidade de serviço; que é aceitável encaminhar o tráfego posterior por via abaixo do ideal.

>[!NOTE] 
>No final deste artigo, links para artigos que aproveitem diferentes recursos de rede do Azure e ecossistemas para suportar usuários que trabalham remotamente são listados.


## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Compartilhando nossas melhores práticas - A rede Azure foi projetada para elasticidade e alta disponibilidade

O Azure foi projetado para suportar mudanças bruscas na utilização dos recursos e pode ajudar muito durante períodos de pico de utilização. Além disso, a Microsoft mantém e opera uma das maiores redes do mundo. A rede da Microsoft foi projetada para alta disponibilidade que pode suportar diferentes tipos de falha: de uma única falha no elemento de rede à falha de toda uma região.

A rede Microsoft foi projetada para atender aos requisitos e fornecer desempenho ideal para diferentes tipos de tráfego de rede, incluindo tráfego multimídia sensível ao atraso para Skype e Equipes, CDN, análise de big data em tempo real, armazenamento Azure, Bing e Xbox. Para fornecer um desempenho ideal para diferentes tipos de tráfego, a rede Microsoft atrai todo o tráfego destinado ou que deseja transitar pelo seu tráfego o mais próximo possível da origem do tráfego.

>[!NOTE] 
>O uso dos recursos de rede do Azure descritos abaixo aproveita o comportamento de atração de tráfego da rede global da Microsoft para fornecer uma melhor experiência de rede de clientes. O comportamento de atração de tráfego da rede Microsoft ajuda a descarregar o tráfego o mais rápido possível das redes de primeira/última milha que podem sofrer congestionamento durante períodos de pico de utilização.
>

## <a name="enable-employees-to-work-remotely"></a>Permitir que os funcionários trabalhem remotamente

O gateway Azure VPN suporta conexões VPN ponto a ponto (local) e site-para-site (S2S). Usando o gateway Azure VPN, você pode dimensionar as conexões de seus funcionários para acessar com segurança os recursos implantados pelo Azure e seus recursos no local. Para obter mais informações, consulte [Como permitir que os usuários trabalhem remotamente](https://go.microsoft.com/fwlink/?linkid=2123770). 

Se você estiver usando o Secure Sockets Tunneling Protocol (SSTP), o número de conexões simultâneas é limitado a 128. Para obter um número maior de conexões, sugerimos a transição para OpenVPN ou IKEv2. Para obter mais informações, consulte [Transition to OpenVPN ou IKEv2 da SSTP](https://go.microsoft.com/fwlink/?linkid=2124112).

Para acessar seus recursos implantados no Azure, os desenvolvedores remotos poderiam usar a solução Azure Bastion, em vez de conexão VPN para obter acesso seguro ao Shell (RDP ou SSH) sem exigir que os IPs públicos nas VMs sejam acessados. Para obter mais informações, consulte [Trabalhar remotamente usando o Azure Bastion](https://go.microsoft.com/fwlink/?linkid=2123939).

Para agregar conexão VPN em larga escala, para suportar conexões entre recursos em diferentes locais globais, em diferentes hubs regionais e redes virtuais faladas, e otimizar a utilização de múltiplas redes de banda larga doméstica você pode usar o Azure Virtual WAN. Para obter mais informações, consulte [Lutando para atender às necessidades domésticas? Aqui é onde o Azure Virtual WAN pode ajudar](https://go.microsoft.com/fwlink/?linkid=2123769).

Outra maneira de suportar uma força de trabalho remota é implantar uma VDI (Virtual Desktop Infrastructure, infra-estrutura virtual de desktop) hospedada em sua rede virtual Azure, protegida com um Firewall Azure. Por exemplo, o Windows Virtual Desktop (WVD) é um serviço de virtualização de desktop e aplicativos que é executado no Azure. Com o Windows Virtual Desktop, você pode configurar um ambiente escalável e flexível em sua assinatura do Azure sem a necessidade de executar quaisquer servidores de gateway adicionais. Você só é responsável pelas máquinas virtuais WVD em sua rede virtual. Para obter mais informações, consulte [o suporte de trabalho remoto do Azure Firewall](../firewall/remote-work-support.md). 

O Azure também tem um rico conjunto de parceiros do sistema ecológico. Nossos parceiros Network Virtual Appliances no Azure também podem ajudar a dimensionar a conectividade VPN. Para obter mais informações, consulte [as considerações do Network Virtual Appliance (NVA) para o trabalho remoto](https://go.microsoft.com/fwlink/?linkid=2123771).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Amplie a conexão dos funcionários para acessar recursos distribuídos globalmente

Os seguintes serviços do Azure podem ajudar a permitir que os funcionários acessem seus recursos distribuídos globalmente. Seus recursos podem estar em qualquer uma das regiões do Azure, redes locais ou mesmo em outras nuvens públicas ou privadas. 

- **Peering de rede virtual do Azure**: Se você implantar seus recursos em mais de uma região do Azure e/ou se agregar a conectividade de funcionários que trabalham remotamente usando várias redes virtuais, você poderá estabelecer conectividade entre as múltiplas redes virtuais do Azure usando peering de rede virtual. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure][VNet-peer].

- **Solução baseada em VPN do Azure**: Para seus funcionários remotos conectados ao Azure via P2S ou S2S VPN, você pode habilitar o acesso a redes locais configurando o S2S VPN entre suas redes locais e o gateway Azure VPN. Para obter mais informações, consulte [Criar uma conexão Site-to-Site][S2S].

- **ExpressRoute**: Usando o peering privado ExpressRoute, você pode habilitar conectividade privada entre suas implantações do Azure e a infra-estrutura local ou sua infra-estrutura em uma instalação de co-localização. O ExpressRoute, via peering da Microsoft, também permite acessar pontos finais públicos na Microsoft a partir de sua rede local. As conexões de ExpressRoute não passam pela Internet pública. Eles oferecem conectividade segura, confiabilidade, maior rendimento, com latências mais baixas e consistentes do que as conexões típicas pela Internet. Para obter mais informações, consulte [Visão geral de ExpressRoute][ExR]. Aproveitar o provedor de rede existente que já faz parte do nosso ecossistema de [parceiros ExpressRoute][ExR-eco] pode ajudar a reduzir o tempo para obter grandes conexões de largura de banda para a Microsoft.  Usando [o ExpressRoute Direct,][ExR-D] você pode conectar diretamente sua rede local ao backbone da Microsoft. O ExpressRoute Direct oferece duas opções diferentes de taxa de linha de 10 Gbps ou 100 Gbps. 

- **Azure Virtual WAN**: Azure Virtual WAN permite interoperabilidade perfeita entre suas conexões VPN e circuitos ExpressRoute. Como mencionado anteriormente, o Azure Virtual WAN também suporta conexões entre recursos em diferentes locais globais on-prem, em diferentes hubs regionais e redes virtuais faladas

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Dimensione a conectividade do cliente com os recursos frontend

Durante os momentos em que mais pessoas entram online, muitos sites corporativos experimentam um aumento no tráfego de clientes. O Azure Application Gateway pode ajudar a gerenciar essa carga de trabalho de frontend aumentada. Para obter mais informações, consulte [o suporte de tráfego alto do Application Gateway](https://go.microsoft.com/fwlink/?linkid=2123940).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Suporte da Microsoft para tráfego em várias nuvens

Para suas implantações em outras nuvens públicas, a Microsoft pode fornecer conectividade global. A Wan Virtual, VPN ou ExpressRoute do Azure virtual podem ajudar nesse sentido. Para estender a conectividade do Azure para outras nuvens, você pode configurar o S2S VPN entre as duas nuvens. Você também pode estabelecer conectividade do Azure para outras nuvens públicas usando o ExpressRoute. A nuvem Oracle faz parte do ecossistema de parceiros ExpressRoute. Você pode [configurar uma interconexão direta entre o Azure e o Oracle Cloud Infrastructure][Az-OCI]. A maioria dos provedores de serviços que fazem parte do ecossistema de parceiros ExpressRoute também oferece conectividade privada para outras nuvens públicas. Aproveitando esses provedores de serviços, você pode estabelecer conectividade privada entre suas implantações no Azure e outras nuvens via ExpressRoute.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir discutem como diferentes recursos de rede do Azure podem ser usados para dimensionar os usuários para trabalhar remotamente:

| **Artigo** | **Última atualização** |
| --- | --- |
| [Como permitir que os usuários trabalhem remotamente](https://go.microsoft.com/fwlink/?linkid=2123770) | 23 de março de 2020 |
| [Lutando para atender às necessidades domésticas? Aqui é onde o Azure Virtual WAN pode ajudar](https://go.microsoft.com/fwlink/?linkid=2123769) | 23 de março de 2020 |
| [Suporte de tráfego de alto tráfego do Application Gateway](https://go.microsoft.com/fwlink/?linkid=2123940) | 23 de março de 2020 |
| [Considerações do Network Virtual Appliance (NVA) para o trabalho remoto](https://go.microsoft.com/fwlink/?linkid=2123771)| 23 de março de 2020 |
| [Transição para o protocolo OpenVPN ou IKEv2 do SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | 23 de março de 2020 |
| [Trabalhando remotamente usando o Azure Bastion](https://go.microsoft.com/fwlink/?linkid=2123939) | 23 de março de 2020 |
| [Usando o Azure ExpressRoute para criar conectividade híbrida para suportar usuários remotos](https://go.microsoft.com/fwlink/?linkid=2123768) | 23 de março de 2020 |
| [Suporte de trabalho remoto do Azure Firewall](../firewall/remote-work-support.md)|25 de março de 2020|

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
