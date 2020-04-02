---
title: Melhores práticas para segurança de rede - Microsoft Azure
description: Este artigo fornece um conjunto de práticas recomendadas de segurança de rede usando recursos internos do Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 5e155758d19b45d977fcd087bff0ceb85898f8f8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548295"
---
# <a name="azure-best-practices-for-network-security"></a>Práticas recomendadas do Azure para segurança de rede
Este artigo discute uma coleção de práticas recomendadas do Azure para melhorar a segurança da sua rede. Essas práticas recomendadas derivam da nossa experiência de rede do Azure e da experiência de clientes como você.

Para cada prática recomendada, este artigo explica:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Essas práticas recomendadas são baseadas em uma opinião de consenso, e recursos e conjuntos de recursos da plataforma Azure, como eles existem no momento em que este artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="use-strong-network-controls"></a>Use controles de rede fortes
Você pode conectar [máquinas virtuais (VMs) do Azure](https://azure.microsoft.com/services/virtual-machines/) e dispositivos a outros dispositivos em rede, colocando-os em [redes virtuais do Azure](../../virtual-network/index.yml). Ou seja, você pode conectar cartões de interface de rede virtual a uma rede virtual para permitir comunicações baseadas em TCP / IP entre dispositivos habilitados para rede. Máquinas virtuais conectadas a uma rede virtual do Azure podem se conectar a dispositivos na mesma rede virtual, em diferentes redes virtuais, na Internet ou em suas próprias redes locais.

Ao planejar sua rede e a segurança de sua rede, recomendamos que você centralize:

- Gerenciamento de funções de rede principais como ExpressRoute, provisionamento de rede virtual e sub-rede e endereçamento IP.
- Governança de elementos de segurança de rede, como funções de dispositivo virtual de rede, como ExpressRoute, provisionamento de rede virtual e sub-rede e endereçamento IP.

Se você usar um conjunto comum de ferramentas de gerenciamento para monitorar sua rede e a segurança de sua rede, você terá uma visibilidade clara em ambos. Uma estratégia de segurança simples e unificada reduz erros porque aumenta a compreensão humana e a confiabilidade da automação.

## <a name="logically-segment-subnets"></a>Segmentar logicamente as sub-redes
As redes virtuais do Azure são semelhantes às LANs em sua rede local. A ideia por trás de uma rede virtual do Azure é que você crie uma rede, baseada em um único espaço de endereço IP privado, no qual você pode colocar todas as suas máquinas virtuais Do Azure. Os espaços de endereço IP privados disponíveis estão nos intervalos de Classe A (10.0.0.0/8), de Classe B (172.16.0.0/12) e de Classe C (192.168.0.0/16).

As práticas recomendadas para segmentar logicamente sub-redes incluem:

**Melhores práticas**: Não atribua regras que permitam regras com amplas faixas (por exemplo, permitam 0.0.0.0 a 255.255.255.255).  
**Detalhe**: Certifique-se de que os procedimentos de solução de problemas desanimem ou proíbam a criação desses tipos de regras. Essas regras permitem que as regras levem a uma falsa sensação de segurança e são frequentemente encontradas e exploradas por equipes vermelhas.

**Prática recomendada**: segmente o espaço de endereço maior em sub-redes.   
**Detalhe**: Use [princípios de sub-redes baseados no CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para criar suas sub-redes.

**Prática recomendada**: crie controles de acesso à rede entre sub-redes. O roteamento entre sub-redes acontece automaticamente, e você não precisa configurar manualmente tabelas de roteamento. Por padrão, não há controles de acesso de rede entre as sub-redes que você cria em uma rede virtual Do Zure.   
**Detalhe**: Use um [grupo de segurança de rede](/azure/virtual-network/virtual-networks-nsg) para proteger contra tráfego não solicitado em sub-redes do Azure. Os grupos de segurança de rede são dispositivos de inspeção de pacotes simples e estaduais que usam a abordagem de 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino e protocolo de camada 4) para criar regras de permissão/negação para o tráfego de rede. Você permite ou nega o tráfego de e para um único endereço IP, de e para vários endereços IP ou para e de sub-redes inteiras.

Quando você usa grupos de segurança de rede para controle de acesso de rede entre sub-redes, você pode colocar recursos que pertencem à mesma zona de segurança ou função em suas próprias sub-redes.

**Melhores práticas**: Evite pequenas redes virtuais e sub-redes para garantir a simplicidade e flexibilidade.   
**Detalhe**: A maioria das organizações adiciona mais recursos do que o planejado inicialmente, e a realocação de endereços é intensiva em mão-de-obra. O uso de pequenas sub-redes adiciona valor de segurança limitado e o mapeamento de um grupo de segurança de rede em cada sub-rede adiciona sobrecarga. Defina sub-redes amplamente para garantir que você tenha flexibilidade para o crescimento.

**Melhores práticas**: Simplifique o gerenciamento de regras do grupo de segurança de rede definindo [grupos de segurança de aplicativos](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Detalhe**: Defina um Grupo de Segurança de Aplicativos para listas de endereços IP que você acha que podem mudar no futuro ou ser usados em muitos grupos de segurança de rede. Certifique-se de nomear grupos de segurança de aplicativos claramente para que outros possam entender seu conteúdo e propósito.

## <a name="adopt-a-zero-trust-approach"></a>Adote uma abordagem zero trust
As redes baseadas em perímetro operam com a suposição de que todos os sistemas dentro de uma rede podem ser confiáveis. Mas os funcionários de hoje acessam os recursos de sua organização de qualquer lugar em uma variedade de dispositivos e aplicativos, o que torna os controles de segurança do perímetro irrelevantes. Políticas de controle de acesso que se concentram apenas em quem pode acessar um recurso não são suficientes. Para dominar o equilíbrio entre segurança e produtividade, os admins de segurança também precisam considerar *como* um recurso está sendo acessado.

As redes precisam evoluir das defesas tradicionais porque as redes podem ser vulneráveis a violações: um invasor pode comprometer um único ponto final dentro do limite confiável e, em seguida, expandir rapidamente uma base em toda a rede. [As](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) redes Zero Trust eliminam o conceito de confiança com base na localização da rede dentro de um perímetro. Em vez disso, as arquiteturas Zero Trust usam dispositivos e reivindicações de confiança do usuário para portar acesso a dados e recursos organizacionais. Para novas iniciativas, adote abordagens do Zero Trust que validem a confiança no momento do acesso.

As melhores práticas são:

**Melhores práticas**: Dar acesso condicional aos recursos com base no dispositivo, identidade, garantia, localização da rede e muito mais.  
**Detalhe**: [O Azure AD Conditional Access](/azure/active-directory/conditional-access/overview) permite que você aplique os controles de acesso certos implementando decisões automatizadas de controle de acesso com base nas condições necessárias. Para obter mais informações, consulte [Gerenciar o acesso ao gerenciamento do Azure com acesso condicional](../../role-based-access-control/conditional-access-azure-management.md).

**Práticas recomendadas**: Habilitar o acesso à porta somente após a aprovação do fluxo de trabalho.  
**Detalhe**: Você pode usar [o acesso just-in-time da VM no Azure Security Center](../../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada em suas VMs do Azure, reduzindo a exposição a ataques e fornecendo acesso fácil para se conectar às VMs quando necessário.

**Melhores práticas**: Conceda permissões temporárias para realizar tarefas privilegiadas, o que impede que usuários mal-intencionados ou não autorizados tenham acesso após o término das permissões. O acesso é concedido somente quando os usuários precisam dele.  
**Detalhe**: Use o acesso just-in-time no Azure AD Privileged Identity Management ou em uma solução de terceiros para conceder permissões para executar tarefas privilegiadas.

A Zero Trust é a próxima evolução na segurança da rede. O estado dos ataques cibernéticos leva as organizações a tomar a mentalidade de "assumir violação", mas essa abordagem não deve ser limitante. As redes Zero Trust protegem dados e recursos corporativos, garantindo que as organizações possam construir um local de trabalho moderno usando tecnologias que capacitam os funcionários a serem produtivos a qualquer hora, em qualquer lugar, de qualquer forma.

## <a name="control-routing-behavior"></a>Controlar o comportamento de roteamento
Quando você coloca uma máquina virtual em uma rede virtual do Azure, a VM pode se conectar a qualquer outra VM na mesma rede virtual, mesmo se as outras VMs estiverem em sub-redes diferentes. Isso é possível porque uma coleção de rotas do sistema ativadas por padrão permite esse tipo de comunicação. Essas rotas padrão permitem que as VMs na mesma rede virtual iniciem conexões umas com as outras e com a Internet (somente para comunicações de saída com a Internet).

Embora as rotas do sistema padrão sejam úteis para muitos cenários de implantação, há momentos em que você deseja personalizar a configuração de roteamento para suas implantações. Você pode configurar o endereço do próximo salto para alcançar destinos específicos.

Recomendamos que você configure [rotas definidas pelo usuário](../../virtual-network/virtual-networks-udr-overview.md) ao implantar um dispositivo de segurança para uma rede virtual. Podemos falar sobre isso em uma seção posterior intitulada [proteger seus recursos essenciais do serviço do Azure para apenas suas redes virtuais](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Rotas definidas pelo usuário não são obrigatórias e as rotas de sistema padrão funcionam normalmente.
>
>

## <a name="use-virtual-network-appliances"></a>Usar dispositivos de rede virtual
Grupos de segurança de rede e roteamento definido pelo usuário podem fornecer uma certa medida de segurança de rede nas camadas de rede e transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Mas, em algumas situações, você quer ou precisa ativar a segurança em níveis altos da pilha. Em tais situações, é recomendável que você implante dispositivos de segurança de rede virtual fornecidos por parceiros do Azure.

Os dispositivos de segurança de rede do Azure podem oferecer uma segurança melhor do que os controles no nível da rede. Os recursos de segurança de rede dos dispositivos de segurança de rede virtual incluem:


* Firewall
* Prevenção de intrusão/detecção de intrusões
* Gerenciamento de vulnerabilidades
* Controle de aplicativo
* Detecção de anomalias baseada em rede
* Filtragem da Web
* Antivírus
* Proteção botnet

Para encontrar os dispositivos de segurança de rede virtuais Azure disponíveis, vá ao [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procure por "segurança" e "segurança de rede".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Implantação de redes de perímetro para zonas de segurança
Uma [rede de perímetro](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (também conhecida como DMZ) é um segmento de rede físico ou lógico que fornece uma camada adicional de segurança entre seus ativos e a Internet. Os dispositivos especializados de controle de acesso à rede na extremidade de uma rede de perímetro permitem apenas o tráfego desejado na sua rede virtual.

As redes de perímetro são úteis porque você pode concentrar seu gerenciamento, monitoramento, registro e geração de relatórios de controle de acesso de rede nos dispositivos na borda da rede virtual do Azure. Uma rede de perímetro é onde você normalmente habilita a prevenção de negação distribuída de serviço (DDoS), sistemas de detecção/prevenção de intrusões (IDS/IPS), regras e políticas de firewall, filtragem da Web, antimalware de rede e muito mais. Os dispositivos de segurança de rede ficam entre a Internet e sua rede virtual do Azure e têm uma interface nas duas redes.

Embora este seja o design básico de uma rede de perímetro, existem muitos designs diferentes, como back-to-back, tri-homed e multi-homed.

Com base no conceito Zero Trust mencionado anteriormente, recomendamos que você considere usar uma rede de perímetro para todas as implantações de alta segurança para melhorar o nível de segurança da rede e controle de acesso para seus recursos do Azure. Você pode usar o Azure ou uma solução de terceiros para fornecer uma camada adicional de segurança entre seus ativos e a internet:

- Controles nativos do Azure. [O Firewall do Azure](/azure/firewall/overview) e o firewall de [aplicativos da Web no Application Gateway](../../application-gateway/features.md#web-application-firewall) oferecem segurança básica com um firewall totalmente imponente como serviço, alta disponibilidade incorporada, escalabilidade irrestrita da nuvem, filtragem de FQDN, suporte para conjuntos de regras principais OWASP e configuração e configuração simples.
- Oferendas de terceiros. Pesquise no [Azure Marketplace](https://azuremarketplace.microsoft.com/) para obter firewall de última geração (NGFW) e outras ofertas de terceiros que fornecem ferramentas de segurança familiares e níveis significativamente aprimorados de segurança de rede. A configuração pode ser mais complexa, mas uma oferta de terceiros pode permitir que você use recursos e habilidades existentes.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evite a exposição à internet com links WAN dedicados
Muitas organizações escolheram a rota de TI híbrida. Com a TI híbrida, alguns dos ativos de informação da empresa estão no Azure, e outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão sendo executados no Azure, enquanto outros componentes permanecem no local.

Em um cenário híbrido de TI, geralmente há algum tipo de conectividade entre premissas. A conectividade entre instalações permite que a empresa conecte suas redes locais às redes virtuais do Azure. Duas soluções de conectividade entre locais estão disponíveis:

* [VPN site a site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). É uma tecnologia confiável, confiável e estabelecida, mas a conexão ocorre pela internet. A largura de banda é limitada a um máximo de cerca de 1,25 Gbps. VPN site-to-site é uma opção desejável em alguns cenários.
* **Azure ExpressRoute**. é recomendável usar o [ExpressRoute](../../expressroute/expressroute-introduction.md) para a conectividade entre locais. O ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de conexão privada facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com serviços de nuvem da Microsoft, como Azure, Office 365 e Dynamics 365. ExpressRoute é um link WAN dedicado entre sua localização no local ou um provedor de hospedagem do Microsoft Exchange. Por se trata de uma conexão telco, seus dados não viajam pela internet, por isso não estão expostos aos potenciais riscos das comunicações de internet.

A localização da conexão ExpressRoute pode afetar a capacidade de firewall, escalabilidade, confiabilidade e visibilidade do tráfego de rede. Você precisará identificar onde terminar o ExpressRoute em redes existentes (no local). Você pode:

- Termine fora do firewall (o paradigma da rede de perímetro) se você precisar de visibilidade no tráfego, se você precisar continuar uma prática existente de isolar data centers ou se você estiver colocando apenas recursos extranet no Azure.
- Terminar dentro do firewall (o paradigma de extensão de rede). Esta é a recomendação padrão. Em todos os outros casos, recomendamos tratar o Azure como um nésimo datacenter.

## <a name="optimize-uptime-and-performance"></a>Otimizar o desempenho e o tempo de atividade
Se um serviço está em baixa, as informações não podem ser acessadas. Se o desempenho é tão ruim que os dados não podem ser usados, você pode considerar os dados como inacessíveis. Do ponto de vista de segurança, você precisa fazer o que puder para garantir que seus serviços tenham tempo de atividade e desempenho ideais.

Um método popular e eficaz para melhorar a disponibilidade e o desempenho é o balanceamento de carga. O balanceamento de carga é um método de distribuição de tráfego de rede entre servidores que fazem parte de um serviço. Por exemplo, se você tiver servidores Web de front-end como parte de seu serviço, poderá usar o balanceamento de carga para distribuir o tráfego entre vários servidores Web de front-end.

Essa distribuição de tráfego aumenta a disponibilidade porque, se um dos servidores Web ficar indisponível, o balanceador de carga interromperá o tráfego de envio para esse servidor e redirecionará o tráfego para os servidores que ainda estiverem online. O balanceamento de carga também ajuda no desempenho porque a sobrecarga do processador, da rede e da memória para atender às solicitações é distribuída por todos os servidores com carga balanceada.

É recomendável implantar o balanceamento de carga sempre que possível e conforme apropriado para seus serviços. A seguir estão os cenários no nível da rede virtual do Azure e no nível global, juntamente com as opções de balanceamento de carga para cada um.

**Cenário**: você tem um aplicativo que:

- Requer solicitações da mesma sessão de usuário / cliente para alcançar a mesma máquina virtual de back-end. Exemplos disso são aplicativos de carrinho de compras e servidores de webmail.
- Aceita apenas uma conexão segura, portanto, a comunicação não criptografada com o servidor não é uma opção aceitável.
- Exige que várias solicitações HTTP na mesma conexão TCP de longa duração sejam roteadas ou carregadas com balanceamento para diferentes servidores de back-end.

**Opção de balanceamento de carga**: use o [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction), um balanceador de carga de tráfego da Web HTTP. O Application Gateway suporta criptografia TLS de ponta a ponta e [terminação TLS](/azure/application-gateway/application-gateway-introduction) no gateway. Os servidores da Web podem então ser aliviados da sobrecarga de criptografia e descriptografia e do fluxo de tráfego não criptografado para os servidores de back-end.

**Cenário**: você precisa carregar as conexões de entrada da Internet entre seus servidores localizados em uma rede virtual do Azure. Os cenários são quando você:

- Tiver aplicativos sem monitoração de estado que aceitam solicitações de entrada da internet.
- Não exija sessões pegajosas ou descarregamento do TLS. As sessões temporárias é um método usado com balanceamento de carga do aplicativo, para alcançar a afinidade do servidor.

**Opção de balanceamento de carga**: use o portal do Azure para[ criar um balanceador de carga externo](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: você precisa carregar conexões de balanceamento de VMs que não estão na Internet. Na maioria dos casos, as conexões aceitas para balanceamento de carga são iniciadas por dispositivos em uma rede virtual do Azure, como instâncias do SQL Server ou servidores da Web internos.   
**Opção de balanceamento de carga**: use o portal do Azure para [criar um balanceador de carga interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: você precisa de balanceamento de carga global porque:

- Tenha uma solução em nuvem amplamente distribuída em várias regiões e que exija o mais alto nível de disponibilidade (disponibilidade) possível.
- É necessário o mais alto nível de tempo de atividade possível para garantir que seu serviço esteja disponível mesmo se um datacenter inteiro ficar indisponível.

**Opção de balanceamento de carga**: use o Gerenciador de Tráfego do Azure. O Gerenciador de Tráfego possibilita o balanceamento de carga das conexões para seus serviços com base na localização do usuário.

Por exemplo, se o usuário fizer uma solicitação ao seu serviço a partir da UE, a conexão será direcionada para os serviços localizados em um datacenter da UE. Esta parte do balanceamento de carga global do Gerenciador de Tráfego ajuda a melhorar o desempenho porque a conexão com o datacenter mais próximo é mais rápida do que conectar-se a data centers que estejam distantes.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Desativar o acesso RDP / SSH às máquinas virtuais
É possível alcançar as máquinas virtuais do Azure usando rdp [(Remote Desktop Protocol)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) e o protocolo [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Esses protocolos permitem o gerenciamento de VMs de locais remotos e são padrão na computação de data center.

O problema de segurança em potencial ao usar esses protocolos pela Internet é que os invasores podem usar as técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obter acesso às máquinas virtuais do Azure. Depois que os invasores tiverem acesso, eles poderão usar sua VM como ponto de partida para comprometer outras máquinas em sua rede virtual ou até mesmo atacar dispositivos em rede fora do Azure.

Recomendamos que você desabilite o acesso RDP e SSH direto às máquinas virtuais do Azure a partir da Internet. Depois que o acesso RDP e SSH direto da Internet for desativado, você terá outras opções que podem ser usadas para acessar essas VMs para gerenciamento remoto.

**Cenário**: permite que um único usuário se conecte a uma rede virtual do Azure pela Internet.   
**Opção**: [VPN ponto-a-site](/azure/vpn-gateway/vpn-gateway-point-to-site-create) é outro termo para uma conexão cliente / servidor VPN de acesso remoto. Depois que a conexão ponto a ponto é estabelecida, o usuário pode usar RDP ou SSH para se conectar a qualquer VM localizada na rede virtual do Azure à qual o usuário se conectou por meio de VPN ponto a ponto. Isso pressupõe que o usuário está autorizado a alcançar essas VMs.

A VPN ponto-a-ponto é mais segura do que as conexões RDP ou SSH diretas porque o usuário precisa autenticar duas vezes antes de se conectar a uma VM. Primeiro, o usuário precisa autenticar (e ser autorizado) para estabelecer a conexão VPN ponto-a-ponto. Segundo, o usuário precisa autenticar (e ser autorizado) para estabelecer a sessão RDP ou SSH.

**Cenário**: permite que os usuários em sua rede local se conectem a VMs em sua rede virtual do Azure.   
**Opção**: Uma [VPN site-to-site](/azure/vpn-gateway/vpn-gateway-site-to-site-create) conecta uma rede inteira a outra rede pela Internet. Você pode usar uma VPN site a site para conectar sua rede local a uma rede virtual do Azure. Os usuários em sua rede local se conectam usando o protocolo RDP ou SSH pela conexão VPN site a site. Você não precisa permitir acesso direto a RDP ou SSH pela internet.

**Cenário**: use um link WAN dedicado para fornecer funcionalidade semelhante à VPN site a site.   
**Opção**: Use [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Ele fornece uma funcionalidade semelhante à VPN site a site. As principais diferenças são:

- O link WAN dedicado não atravessa a internet.
- Os links de WAN dedicados geralmente são mais estáveis e apresentam melhor desempenho.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Proteja seus recursos de serviço críticos do Azure somente para suas redes virtuais
Use pontos de extremidade de serviço de rede virtual para estender o espaço de endereço privado de sua rede virtual e a identidade de sua rede virtual para os serviços do Azure, por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego da sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure.

Os pontos de extremidade de serviço fornecem os seguintes benefícios:

- **Segurança aprimorada para os recursos do serviço do Azure**: com pontos de extremidade de serviço, os recursos do serviço do Azure podem ser protegidos em sua rede virtual. A proteção de recursos de serviço a uma rede virtual fornece segurança aprimorada, removendo totalmente o acesso público à Internet a recursos e permitindo o tráfego somente de sua rede virtual.
- **O roteamento ideal para o tráfego de serviço do Azure a partir de sua rede virtual**: quaisquer rotas em sua rede virtual que forcem o tráfego de Internet para seus dispositivos locais e / ou virtuais, conhecidas como encapsulamento forçado, também forçarão o tráfego de serviço do Azure a mesma rota que o tráfego da Internet. Os pontos de extremidade de serviço oferecem o roteamento ideal para o tráfego do Azure.

  Os pontos de extremidade sempre levam o tráfego de serviço diretamente de sua rede virtual para o serviço na rede do backbone do Azure. Manter o tráfego na rede de backbone do Azure permite continuar a auditar e monitorar o tráfego de saída da Internet de suas redes virtuais, por meio de encapsulamento forçado, sem afetar o tráfego de serviço. Saiba mais sobre [rotas definidas pelo usuário e encapsulamento forçado](../../virtual-network/virtual-networks-udr-overview.md).

- **Simples de configurar com menos sobrecarga de gerenciamento**: você não precisa mais de endereços IP públicos reservados em suas redes virtuais para proteger os recursos do Azure por meio de um firewall de IP. Não é obrigatório nenhum dispositivo NAT ou de gateway para configurar os pontos de extremidade de serviço. Pontos de extremidade de serviço são configurados por meio de um clique simples em uma sub-rede. Não há sobrecarga adicional para manter os terminais.

Para saber mais sobre os pontos de extremidade de serviços e os serviços e regiões do Azure aos quais os pontos de extremidade de serviço estão disponíveis, consulte [Pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Próximas etapas
Consulte [as práticas e padrões de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar quando você está projetando, implantando e gerenciando suas soluções em nuvem usando o Azure.
