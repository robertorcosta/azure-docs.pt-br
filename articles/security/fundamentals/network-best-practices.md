---
title: Práticas recomendadas para segurança de rede-Microsoft Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548295"
---
# <a name="azure-best-practices-for-network-security"></a>Práticas recomendadas do Azure para segurança de rede
Este artigo discute uma coleção de práticas recomendadas do Azure para aprimorar sua segurança de rede. Essas práticas recomendadas derivam da nossa experiência de rede do Azure e da experiência de clientes como você.

Para cada prática recomendada, este artigo explica:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Essas práticas recomendadas baseiam-se em uma opinião de consenso, bem como em recursos e conjuntos de recursos da plataforma Azure, como existem no momento em que este artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="use-strong-network-controls"></a>Usar controles de rede fortes
Você pode conectar [máquinas virtuais (VMs) do Azure](https://azure.microsoft.com/services/virtual-machines/) e dispositivos a outros dispositivos em rede, colocando-os em [redes virtuais do Azure](../../virtual-network/index.yml). Ou seja, você pode conectar cartões de interface de rede virtual a uma rede virtual para permitir comunicações baseadas em TCP / IP entre dispositivos habilitados para rede. Máquinas virtuais conectadas a uma rede virtual do Azure podem se conectar a dispositivos na mesma rede virtual, em diferentes redes virtuais, na Internet ou em suas próprias redes locais.

Ao planejar sua rede e a segurança de sua rede, recomendamos que você centralize:

- Gerenciamento de funções de rede de núcleo como ExpressRoute, rede virtual e provisionamento de sub-rede e endereçamento IP.
- Governança de elementos de segurança de rede, como funções de solução de virtualização de rede, como ExpressRoute, rede virtual e provisionamento de sub-rede e endereçamento IP.

Se você usar um conjunto comum de ferramentas de gerenciamento para monitorar sua rede e a segurança de sua rede, você terá visibilidade clara em ambos. Uma estratégia de segurança simples e unificada reduz os erros, pois aumenta a compreensão humana e a confiabilidade da automação.

## <a name="logically-segment-subnets"></a>Segmentar logicamente as sub-redes
As redes virtuais do Azure são semelhantes às LANs em sua rede local. A ideia por trás de uma rede virtual do Azure é que você cria uma rede, com base em um único espaço de endereço IP privado, no qual você pode posicionar todas as suas máquinas virtuais do Azure. Os espaços de endereço IP privados disponíveis estão nos intervalos de Classe A (10.0.0.0/8), de Classe B (172.16.0.0/12) e de Classe C (192.168.0.0/16).

As práticas recomendadas para segmentar logicamente sub-redes incluem:

**Prática recomendada**: não atribua regras de permissão com intervalos amplos (por exemplo, permitir 0.0.0.0 por meio de 255.255.255.255).  
**Detalhe**: Verifique se os procedimentos de solução de problemas desencorajam ou proíbam a configuração desses tipos de regras. Essas regras de permissão levam a uma falsa sensação de segurança e são encontradas e exploradas com frequência por equipes vermelhas.

**Prática recomendada**: segmente o espaço de endereço maior em sub-redes.   
**Detalhe**: Use [princípios de sub-redes baseados no CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para criar suas sub-redes.

**Prática recomendada**: crie controles de acesso à rede entre sub-redes. O roteamento entre sub-redes ocorre automaticamente e você não precisa configurar manualmente as tabelas de roteamento. Por padrão, não há nenhum controle de acesso à rede entre as sub-redes que você cria em uma rede virtual do Azure.   
**Detalhe**: Use um [grupo de segurança de rede](/azure/virtual-network/virtual-networks-nsg) para proteger contra tráfego não solicitado em sub-redes do Azure. Os grupos de segurança de rede são dispositivos de inspeção de pacote simples e com estado que usam a abordagem de 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino e protocolo de camada 4) para criar regras de permissão/negação para o tráfego de rede. Você permite ou nega o tráfego de e para um único endereço IP, de e para vários endereços IP ou para e de sub-redes inteiras.

Ao usar grupos de segurança de rede para controle de acesso à rede entre sub-redes, você pode colocar os recursos que pertencem à mesma zona ou função de segurança em suas próprias sub-redes.

**Prática recomendada**: Evite redes virtuais e sub-redes pequenas para garantir a simplicidade e a flexibilidade.   
**Detalhe**: a maioria das organizações adiciona mais recursos do que o planejado inicialmente e a realocação de endereços é trabalhoso. O uso de sub-redes pequenas adiciona um valor de segurança limitado e o mapeamento de um grupo de segurança de rede para cada sub-rede adiciona sobrecarga. Defina sub-redes em larga escala para garantir que você tenha flexibilidade para o crescimento.

**Prática recomendada**: Simplifique o gerenciamento de regras do grupo de segurança de rede definindo [grupos de segurança de aplicativo](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Detalhe**: defina um grupo de segurança de aplicativo para listas de endereços IP que você ache que podem ser alterados no futuro ou que sejam usados em vários grupos de segurança de rede. Não se esqueça de nomear os grupos de segurança de aplicativos claramente para que outras pessoas possam entender seu conteúdo e finalidade.

## <a name="adopt-a-zero-trust-approach"></a>Adote uma abordagem de confiança zero
As redes baseadas em perímetro operam de acordo com a suposição de que todos os sistemas em uma rede possam ser confiáveis. Mas os funcionários de hoje acessam os recursos de sua organização de qualquer lugar em uma variedade de dispositivos e aplicativos, o que torna os controles de segurança de perímetro irrelevantes. As políticas de controle de acesso que se concentram apenas em quem pode acessar um recurso não são suficientes. Para dominar o equilíbrio entre segurança e produtividade, os administradores de segurança também precisam considerar *como* um recurso está sendo acessado.

As redes precisam evoluir de defesas tradicionais, pois as redes podem estar vulneráveis a violações: um invasor pode comprometer um único ponto de extremidade dentro do limite confiável e, em seguida, expandir rapidamente um destaque em toda a rede. Redes de [confiança zero](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminam o conceito de confiança com base no local de rede em um perímetro. Em vez disso, as arquiteturas de confiança zero usam declarações de confiança de dispositivo e de usuário para portar o acesso a dados e recursos organizacionais. Para novas iniciativas, adote zero abordagens de confiança que validam a confiança no momento do acesso.

As práticas recomendadas são:

**Prática recomendada**: conceda acesso condicional a recursos com base em dispositivo, identidade, garantia, local de rede e muito mais.  
**Detalhe**: o [acesso condicional do Azure ad](/azure/active-directory/conditional-access/overview) permite aplicar os controles de acesso certos implementando decisões de controle de acesso automatizadas com base nas condições necessárias. Para obter mais informações, consulte [gerenciar o acesso ao gerenciamento do Azure com acesso condicional](../../role-based-access-control/conditional-access-azure-management.md).

**Prática recomendada**: habilite o acesso à porta somente após a aprovação do fluxo de trabalho.  
**Detalhe**: você pode usar o [acesso à VM just-in-time na central de segurança do Azure](../../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário.

**Prática recomendada**: conceder permissões temporárias para executar tarefas privilegiadas, o que impede que usuários mal-intencionados ou não autorizados obtenham acesso depois que as permissões expirarem. O acesso é concedido somente quando os usuários precisam dele.  
**Detalhe**: Use o acesso just-in-time no Azure ad Privileged Identity Management ou em uma solução de terceiros para conceder permissões para executar tarefas privilegiadas.

Confiança zero é a próxima evolução na segurança de rede. O estado do ataques cibernéticos orienta as organizações a tomar a mentalidade de "assumir a violação", mas essa abordagem não deve ser limitada. As redes de confiança zero protegem dados e recursos corporativos, garantindo que as organizações possam criar um local de trabalho moderno usando tecnologias que capacitam os funcionários a serem produtivos a qualquer momento, em qualquer lugar.

## <a name="control-routing-behavior"></a>Controlar o comportamento de roteamento
Quando você coloca uma máquina virtual em uma rede virtual do Azure, a VM pode se conectar a qualquer outra VM na mesma rede virtual, mesmo se as outras VMs estiverem em sub-redes diferentes. Isso é possível porque uma coleção de rotas do sistema ativadas por padrão permite esse tipo de comunicação. Essas rotas padrão permitem que as VMs na mesma rede virtual iniciem conexões umas com as outras e com a Internet (somente para comunicações de saída com a Internet).

Embora as rotas do sistema padrão sejam úteis para muitos cenários de implantação, há momentos em que você deseja personalizar a configuração de roteamento para suas implantações. Você pode configurar o endereço do próximo salto para alcançar destinos específicos.

Recomendamos que você configure [rotas definidas pelo usuário](../../virtual-network/virtual-networks-udr-overview.md) ao implantar um dispositivo de segurança para uma rede virtual. Podemos falar sobre isso em uma seção posterior intitulada [proteger seus recursos essenciais do serviço do Azure para apenas suas redes virtuais](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Rotas definidas pelo usuário não são obrigatórias e as rotas de sistema padrão funcionam normalmente.
>
>

## <a name="use-virtual-network-appliances"></a>Usar dispositivos de rede virtual
Os grupos de segurança de rede e o roteamento definido pelo usuário podem fornecer uma determinada medida de segurança de rede nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Mas, em algumas situações, você quer ou precisa ativar a segurança em níveis altos da pilha. Em tais situações, é recomendável que você implante dispositivos de segurança de rede virtual fornecidos por parceiros do Azure.

Os dispositivos de segurança de rede do Azure podem oferecer uma segurança melhor do que os controles no nível da rede. Os recursos de segurança de rede dos dispositivos de segurança de rede virtual incluem:


* Firewall
* Prevenção de intrusão/detecção de intrusões
* Gerenciamento de vulnerabilidades
* Controle de aplicativo
* Detecção de anomalias baseada em rede
* Filtragem da Web
* Antivírus
* Proteção botnet

Para localizar os dispositivos de segurança de rede virtual do Azure disponíveis, vá para o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e pesquise "segurança" e "segurança de rede".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Implantação de redes de perímetro para zonas de segurança
Uma [rede de perímetro](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (também conhecida como DMZ) é um segmento de rede físico ou lógico que fornece uma camada adicional de segurança entre seus ativos e a Internet. Os dispositivos especializados de controle de acesso à rede na extremidade de uma rede de perímetro permitem apenas o tráfego desejado na sua rede virtual.

As redes de perímetro são úteis porque você pode concentrar seu gerenciamento, monitoramento, registro e geração de relatórios de controle de acesso de rede nos dispositivos na borda da rede virtual do Azure. Uma rede de perímetro é onde você geralmente habilita A prevenção de DDoS (negação de serviço distribuído), a detecção de invasão/sistemas de prevenção de intrusão (IDS/IPS), regras de firewall e políticas, filtragem da Web, Antimalware de rede e muito mais. Os dispositivos de segurança de rede ficam entre a Internet e sua rede virtual do Azure e têm uma interface nas duas redes.

Embora esse seja o design básico de uma rede de perímetro, há muitos designs diferentes, como back-to-back, Tri-homed e multihomed.

Com base no conceito de confiança zero mencionado anteriormente, recomendamos que você considere o uso de uma rede de perímetro para todas as implantações de alta segurança para aprimorar o nível de segurança de rede e controle de acesso para seus recursos do Azure. Você pode usar o Azure ou uma solução de terceiros para fornecer uma camada adicional de segurança entre seus ativos e a Internet:

- Controles nativos do Azure. O [Firewall do Azure](/azure/firewall/overview) e o [Firewall do aplicativo Web no gateway de aplicativo](../../application-gateway/features.md#web-application-firewall) oferecem segurança básica com um firewall totalmente monitorado como um serviço, alta disponibilidade interna, escalabilidade de nuvem IRRESTRITA, filtragem de FQDN, suporte para conjuntos de regras principais do OWASP e instalação e configuração simples.
- Ofertas de terceiros. Pesquise no [Azure Marketplace](https://azuremarketplace.microsoft.com/) o firewall de próxima geração (NGFW) e outras ofertas de terceiros que fornecem ferramentas de segurança familiares e níveis significativamente aprimorados de segurança de rede. A configuração pode ser mais complexa, mas uma oferta de terceiros pode permitir que você use funcionalidades e habilidades existentes.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evite a exposição à Internet com links WAN dedicados
Muitas organizações escolheram a rota de TI híbrida. Com a ti híbrida, alguns dos ativos de informações da empresa estão no Azure e outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão sendo executados no Azure, enquanto outros componentes permanecem no local.

Em um cenário de ti híbrido, geralmente há algum tipo de conectividade entre locais. A conectividade entre instalações permite que a empresa conecte suas redes locais às redes virtuais do Azure. Duas soluções de conectividade entre locais estão disponíveis:

* [VPN site a site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). É uma tecnologia confiável, confiável e estabelecida, mas a conexão ocorre pela Internet. A largura de banda é restrita a um máximo de cerca de 1,25 Gbps. A VPN site a site é uma opção desejável em alguns cenários.
* **Azure ExpressRoute**. é recomendável usar o [ExpressRoute](../../expressroute/expressroute-introduction.md) para a conectividade entre locais. O ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de conexão privada facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Azure, o Office 365 e o Dynamics 365. O ExpressRoute é um link WAN dedicado entre seu local ou um provedor de hospedagem do Microsoft Exchange. Como essa é uma conexão Telco, seus dados não viajam pela Internet, portanto, não são expostos aos riscos potenciais das comunicações da Internet.

O local da conexão do ExpressRoute pode afetar a capacidade do firewall, a escalabilidade, a confiabilidade e a visibilidade do tráfego de rede. Você precisará identificar onde terminar o ExpressRoute em redes existentes (locais). Você pode:

- Termine fora do firewall (paradigma de rede de perímetro) se você precisar de visibilidade no tráfego, se precisar continuar uma prática existente de isolar datacenters ou se você estiver colocando apenas recursos de extranet no Azure.
- Termine dentro do firewall (o paradigma de extensão de rede). Essa é a recomendação padrão. Em todos os outros casos, é recomendável tratar o Azure como um enésimo datacenter.

## <a name="optimize-uptime-and-performance"></a>Otimizar o desempenho e o tempo de atividade
Se um serviço estiver inativo, as informações não poderão ser acessadas. Se o desempenho é tão ruim que os dados não podem ser usados, você pode considerar os dados como inacessíveis. Do ponto de vista de segurança, você precisa fazer o que puder para garantir que seus serviços tenham tempo de atividade e desempenho ideais.

Um método popular e eficaz para melhorar a disponibilidade e o desempenho é o balanceamento de carga. O balanceamento de carga é um método de distribuição de tráfego de rede entre servidores que fazem parte de um serviço. Por exemplo, se você tiver servidores Web de front-end como parte de seu serviço, poderá usar o balanceamento de carga para distribuir o tráfego entre vários servidores Web de front-end.

Essa distribuição de tráfego aumenta a disponibilidade porque, se um dos servidores Web ficar indisponível, o balanceador de carga interromperá o tráfego de envio para esse servidor e redirecionará o tráfego para os servidores que ainda estiverem online. O balanceamento de carga também ajuda no desempenho porque a sobrecarga do processador, da rede e da memória para atender às solicitações é distribuída por todos os servidores com carga balanceada.

É recomendável implantar o balanceamento de carga sempre que possível e conforme apropriado para seus serviços. A seguir estão os cenários no nível da rede virtual do Azure e no nível global, juntamente com as opções de balanceamento de carga para cada um.

**Cenário**: você tem um aplicativo que:

- Requer solicitações da mesma sessão de usuário / cliente para alcançar a mesma máquina virtual de back-end. Exemplos disso são aplicativos de carrinho de compras e servidores de webmail.
- Aceita apenas uma conexão segura, portanto, a comunicação não criptografada com o servidor não é uma opção aceitável.
- Exige que várias solicitações HTTP na mesma conexão TCP de longa duração sejam roteadas ou carregadas com balanceamento para diferentes servidores de back-end.

**Opção de balanceamento de carga**: use o [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction), um balanceador de carga de tráfego da Web HTTP. O gateway de aplicativo dá suporte à criptografia TLS de ponta a ponta e [terminação TLS](/azure/application-gateway/application-gateway-introduction) no gateway. Os servidores da Web podem então ser aliviados da sobrecarga de criptografia e descriptografia e do fluxo de tráfego não criptografado para os servidores de back-end.

**Cenário**: você precisa carregar as conexões de entrada da Internet entre seus servidores localizados em uma rede virtual do Azure. Os cenários são quando você:

- Tiver aplicativos sem monitoração de estado que aceitam solicitações de entrada da internet.
- Não exigir sessões adesivas ou descarregamento de TLS. As sessões temporárias é um método usado com balanceamento de carga do aplicativo, para alcançar a afinidade do servidor.

**Opção de balanceamento de carga**: use o portal do Azure para[ criar um balanceador de carga externo](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: você precisa carregar conexões de balanceamento de VMs que não estão na Internet. Na maioria dos casos, as conexões aceitas para balanceamento de carga são iniciadas por dispositivos em uma rede virtual do Azure, como instâncias do SQL Server ou servidores da Web internos.   
**Opção de balanceamento de carga**: use o portal do Azure para [criar um balanceador de carga interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: você precisa de balanceamento de carga global porque:

- Tenha uma solução em nuvem amplamente distribuída em várias regiões e que exija o mais alto nível de disponibilidade (disponibilidade) possível.
- É necessário o mais alto nível de tempo de atividade possível para garantir que seu serviço esteja disponível mesmo se um datacenter inteiro ficar indisponível.

**Opção de balanceamento de carga**: use o Gerenciador de Tráfego do Azure. O Gerenciador de Tráfego possibilita o balanceamento de carga das conexões para seus serviços com base na localização do usuário.

Por exemplo, se o usuário fizer uma solicitação ao seu serviço a partir da UE, a conexão será direcionada para os serviços localizados em um datacenter da UE. Esta parte do balanceamento de carga global do Gerenciador de Tráfego ajuda a melhorar o desempenho porque a conexão com o datacenter mais próximo é mais rápida do que conectar-se a data centers que estejam distantes.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Desativar o acesso RDP / SSH às máquinas virtuais
É possível alcançar as máquinas virtuais do Azure usando [protocolo RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e o protocolo [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Esses protocolos permitem o gerenciamento de VMs de locais remotos e são padrão na computação de data center.

O problema de segurança em potencial ao usar esses protocolos pela Internet é que os invasores podem usar as técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obter acesso às máquinas virtuais do Azure. Depois que os invasores tiverem acesso, eles poderão usar sua VM como ponto de partida para comprometer outras máquinas em sua rede virtual ou até mesmo atacar dispositivos em rede fora do Azure.

Recomendamos que você desabilite o acesso RDP e SSH direto às máquinas virtuais do Azure a partir da Internet. Depois que o acesso RDP e SSH direto da Internet for desativado, você terá outras opções que podem ser usadas para acessar essas VMs para gerenciamento remoto.

**Cenário**: permite que um único usuário se conecte a uma rede virtual do Azure pela Internet.   
**Opção**: [VPN ponto-a-site](/azure/vpn-gateway/vpn-gateway-point-to-site-create) é outro termo para uma conexão cliente / servidor VPN de acesso remoto. Depois que a conexão ponto a ponto é estabelecida, o usuário pode usar RDP ou SSH para se conectar a qualquer VM localizada na rede virtual do Azure à qual o usuário se conectou por meio de VPN ponto a ponto. Isso pressupõe que o usuário está autorizado a alcançar essas VMs.

A VPN ponto-a-ponto é mais segura do que as conexões RDP ou SSH diretas porque o usuário precisa autenticar duas vezes antes de se conectar a uma VM. Primeiro, o usuário precisa autenticar (e ser autorizado) para estabelecer a conexão VPN ponto-a-ponto. Segundo, o usuário precisa autenticar (e ser autorizado) para estabelecer a sessão RDP ou SSH.

**Cenário**: permite que os usuários em sua rede local se conectem a VMs em sua rede virtual do Azure.   
**Opção**: Uma [VPN site-to-site](/azure/vpn-gateway/vpn-gateway-site-to-site-create) conecta uma rede inteira a outra rede pela Internet. Você pode usar uma VPN site a site para conectar sua rede local a uma rede virtual do Azure. Os usuários em sua rede local se conectam usando o protocolo RDP ou SSH pela conexão VPN site a site. Você não precisa permitir acesso direto de RDP ou SSH pela Internet.

**Cenário**: use um link WAN dedicado para fornecer funcionalidade semelhante à VPN site a site.   
**Opção**: Use [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Ele fornece uma funcionalidade semelhante à VPN site a site. As principais diferenças são:

- O link WAN dedicado não atravessa a Internet.
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
Veja [padrões e práticas recomendadas de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.
