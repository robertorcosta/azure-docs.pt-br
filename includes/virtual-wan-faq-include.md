---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 01ed6d836e5d6bfe139e4a21a0ff6a9708c261d3
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977902"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>O usuário precisa ter hub e spoke com dispositivos SD-WAN/VPN para usar a WAN virtual do Azure?

A WAN virtual fornece muitas funcionalidades incorporadas em um único painel de vidro, como conectividade VPN site a site/site, conectividade de Usuário/P2S, conectividade do ExpressRoute, conectividade de rede virtual, Interconectividade de VPN ExpressRoute, conectividade transitiva VNET a VNET, roteamento centralizado, firewall do Azure e segurança do gerenciador de firewall, Monitoramento, Criptografia do ExpressRoute e muitos outros recursos. Você não precisa ter todos esses casos de uso para começar a usar a WAN Virtual. Você pode simplesmente começar com apenas um caso de uso. A arquitetura da WAN virtual é uma arquitetura de hub e spoke com dimensionamento e desempenho internos em que as ramificações (dispositivos VPN/SD-WAN), os usuários (clientes VPN do Azure, clientes openVPN ou IKEv2), os circuitos de ExpressRoute, as Redes Virtuais servem como spokes para os Hubs Virtuais. Todos os hubs são conectados em malha completa em uma WAN Virtual Padrão, o que facilita para o usuário usar o backbone da Microsoft para conectividade de qualquer um para qualquer um (qualquer spoke). Para o hub e spoke com dispositivos SD-WAN/VPN, os usuários podem configurá-lo manualmente no portal da WAN Virtual do Azure ou usar o CPE do Parceiro da WAN Virtual (SD-WAN/VPN) para configurar a conectividade com o Azure. Os parceiros de WAN virtuais fornecem automação para conectividade, que é a capacidade de exportar as informações do dispositivo para o Azure, baixar a configuração do Azure e estabelecer a conectividade com o hub da WAN Virtual do Azure. Para a conectividade de VPN do usuário/ponto a site, damos suporte [cliente de VPN do Azure](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN ou IKEv2. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Com qual cliente a VPN de Usuário da WAN Virtual do Azure (ponto a site) é compatível?

A WAN virtual é compatível com [cliente da VPN do Azure](https://go.microsoft.com/fwlink/?linkid=2117554), cliente OpenVPN ou qualquer cliente IKEv2. A autenticação do Azure AD é compatível com o cliente VPN do Azure. É necessário um sistema operacional cliente Windows 10 versão 17763.0 ou superior.  Os clientes OpenVPN podem ser compatíveis com autenticação baseada em certificado. Depois que a autenticação baseada em certificado for selecionada no gateway, você verá o arquivo .ovpn para baixar em seu dispositivo. O certificado e a autenticação RADIUS têm suporte com IKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Para VPN de usuário (ponto a site) – por que o pool de clientes P2S é dividido em duas rotas?

Cada gateway tem duas instâncias, a divisão acontece para que cada instância de gateway possa alocar IPs de cliente de maneira independente para clientes conectados e o tráfego da rede virtual seja roteado de volta para a instância de gateway correta para evitar o salto de instância entre gateways.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Como faço para adicionar servidores DNS a clientes P2S?

Há duas opções para adicionar servidores DNS aos clientes do P2S.

1. Abrir um tíquete de suporte com a Microsoft para solicitar a adição de seus servidores DNS ao hub
2. Ou, se você estiver usando o cliente VPN do Azure para Windows 10, poderá modificar o arquivo XML do perfil baixado e adicionar as marcas **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** antes de importá-lo.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Para VPN de usuário (ponto a site) – há suporte para quantos clientes?

Cada gateway P2S VPN de usuário tem duas instâncias e cada instância é compatível com até um número de usuários determinado à medida que a unidade de escala muda. A unidade de escala 1-3 é compatível com 500 conexões, a unidade de escala 4-6 é compatível com 1 mil conexões, a unidade de escala 7-12 é compatível com 5 mil conexões e a unidade de escala 13-20 é compatível com até 10 mil conexões. Como exemplo, digamos que o usuário escolha uma unidade de escala. Cada unidade de escala implica um gateway ativo-ativo implantado, e cada uma das instâncias (neste caso, 2) seria compatível com até 500 conexões. Como você pode obter 500 conexões * 2 por gateway, isso não significa que você planeja 1.000, em vez de 500, para essa unidade de escala, pois as instâncias podem precisar ser atendidas, e durante esse tempo a conectividade para as 500 extras poderá ser interrompida se você ultrapassar a contagem de conexões recomendada. Além disso, não se esqueça de planejar o tempo de inatividade caso você decida escalar ou reduzir verticalmente na unidade de escala ou alterar a configuração de ponto a site no gateway de VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um gateway de VPN da WAN Virtual do Azure?

A WAN Virtual fornece conectividade site a site em larga escala e é criada visando produtividade, escalabilidade e facilidade de uso. Quando você conecta um site a um gateway de VPN de WAN Virtual, ele é diferente de um gateway de rede virtual comum que usa um tipo de gateway 'VPN'. Da mesma forma, quando você conecta um circuito de ExpressRoute a um hub de WAN Virtual, ele usa para o gateway de ExpressRoute um recurso diferente daquele usado pelo gateway de rede virtual regular, o qual usa o tipo de gateway 'ExpressRoute'. A WAN Virtual dá suporte a uma taxa de transferência de agregação de até 20 Gbps para VPN e ExpressRoute. A WAN Virtual também tem automação para conectividade com um ecossistema de parceiros de dispositivo de ramificação CPE. Os dispositivos da ramificação CPE têm automação interna que provisiona automaticamente e se conecta à WAN Virtual do Azure. Esses dispositivos estão disponíveis em um ecossistema crescente de SD-WAN e parceiros de VPN. Confira a [Lista de Parceiros Preferenciais](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Como a WAN Virtual é diferente de um gateway de rede virtual do Azure existente?

A VPN do Gateway de Rede Virtual é limitada a 30 túneis. Para conexões, você deve usar a WAN Virtual para VPN em larga escala. Você pode conectar até 1.000 conexões de ramificação por região (hub virtual) com agregação de 20 Gbps por hub. Uma conexão é um túnel de ativo-ativo do dispositivo VPN local para o hub virtual. Pode haver um hub por região, o que significa que você pode conectar a mais de 1.000 branches entre os hubs.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>O que é uma Unidade de Escala de Gateway da WAN Virtual
Uma unidade de escala é uma unidade definida para escolher uma taxa de transferência agregada de um gateway no Hub virtual. 1 unidade de escala de VPN = 500 Mbps. 1 unidade de escala do ExpressRoute = 2 Gbps. Exemplo: 10 unidades de escala de VPN implicariam 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quais provedores de dispositivo (parceiros de WAN Virtual) são compatíveis?

Neste momento, muitos parceiros dão suporte à experiência de WAN Virtual totalmente automatizada. Para saber mais, confira [Parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são as etapas de automação de parceiro de WAN Virtual?

Para conhecer as etapas de automação de parceiro, confira [Automação de parceiro de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Eu sou obrigado a usar um dispositivo de parceiro preferido?

Não. Você pode usar qualquer dispositivo com capacidade para VPN que siga os requisitos do Azure para suporte a IPsec IKEv2/IKEv1.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

Soluções de conectividade definidas pelo software normalmente gerenciam seus dispositivos de branch usando um controlador ou um centro de provisionamento de dispositivos. O controlador pode usar as APIs do Azure para automatizar a conectividade com a WAN Virtual do Azure. A automação inclui o upload de informações de ramificação, o download da configuração do Azure, a configuração de túneis IPsec em hubs virtuais do Azure e a configuração automática da conectividade do dispositivo de ramificação para a WAN Virtual do Azure. Quando você tem centenas de ramificações, é fácil conectar-se usando parceiros CPE da WAN Virtual, pois a experiência de integração elimina a necessidade de configurar e gerenciar a conectividade IPsec em larga escala. Para saber mais, confira [Automação de parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como o WAN Virtual dá suporte aos dispositivos SD-WAN?

Parceiros WAN Virtuais automatizam a conectividade IPsec com pontos de extremidade de VPN do Azure. Se o parceiro WAN Virtual é um provedor de SD-WAN, está implícito que o controlador de SD-WAN gerencia a automação e conectividade IPsec aos pontos de extremidade do Azure VPN. Se o dispositivo de SD-WAN requer seu próprio ponto de extremidade de VPN do Azure para qualquer funcionalidade SD-WAN proprietária, você pode implantar o ponto de extremidade SD-WAN em uma Rede Virtual do Azure e coexiste com o WAN Virtual do Azure.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>A WAN Virtual altera quaisquer recursos de conectividade existentes?

Não há alterações nos recursos de conectividade do Azure existentes.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Há novos recursos do Gerenciador de recursos disponíveis para WAN Virtual?
  
Sim, a WAN Virtual apresenta novos recursos do Gerenciador de Recursos. Para saber mais, confira a [Visão geral](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem se conectar a um único hub?

O hub virtual é compatível com até 1.000 conexões. Cada conexão é composta por quatro links, cada um dos quais dá suporte a dois túneis que estão em uma configuração ativo/ativo. Os túneis terminam em um vpngateway do hub virtual do Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN local pode se conectar a vários hubs?

Sim. O fluxo de tráfego ao começar é do dispositivo local para a borda de rede da Microsoft mais próxima e, em seguida, para o hub virtual.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implantar e usar minha solução de virtualização de rede favorita (em uma VNet NVA) com a WAN Virtual do Azure?

Sim, você pode conectar a VNET da sua NVA (solução de virtualização de rede) favorita à WAN Virtual do Azure. Primeiro, conecte a VNET da sua solução de virtualização de rede virtual ao hub com uma conexão de rede virtual do hub. Em seguida, crie uma rota do hub virtual com um próximo salto apontando para a solução de virtualização. Você pode aplicar várias rotas à tabela de rotas do hub virtual. Além disso, todos os spokes conectados à VNET da NVA também precisam ser conectados ao hub virtual para garantir que as rotas da VNET do spoke sejam propagadas para os sistemas locais.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso criar uma solução de virtualização de rede dentro do hub virtual?

Uma NVA (solução de virtualização de rede) não pode ser implantada dentro de um hub virtual. No entanto, você pode criá-la em uma VNet spoke conectada ao hub virtual e habilitar uma rota no hub para direcionar o tráfego para a VNet de destino por meio do endereço IP da NVA (da NIC).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Uma VNet spoke pode ter um gateway de rede virtual?

Não. A VNet spoke não poderá ter um gateway de rede virtual se estiver conectada ao hub virtual.

### <a name="is-there-support-for-bgp"></a>Há suporte para BGP?

Sim, há suporte para BGP. Quando você cria um site VPN, você pode fornecer os parâmetros de BGP nele. Isso implicará que todas as conexões criadas no Azure para o site serão habilitadas para BGP. Além disso, se você tiver uma rede virtual com uma NVA e se essa rede virtual de NVA foi anexada a um hub de WAN Virtual, para garantir que as rotas de uma rede virtual de NVA sejam anunciadas apropriadamente, spokes que estão anexados à rede virtual de NVA devem desabilitar o BGP. Além disso, conecte essas redes virtuais do spoke à rede hub virtual do hub para garantir que as rotas de rede virtual do spoke sejam propagadas nos sistemas locais.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego usando UDR no hub virtual?

Sim, você pode direcionar o tráfego para uma VNET usando a tabela de rotas do hub virtual. Isso permite que você defina rotas para VNets de destino no Azure por meio de um endereço IP específico (normalmente da NIC NVA).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Há quaisquer informações de licenciamento ou preços para a WAN Virtual?

Sim. Confira a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Como fazer para calcular o preço de um hub?

* Você pagaria pelos serviços no hub. Por exemplo, suponha que você tem dez branches ou dispositivos locais que precisam se conectar à WAN Virtual do Azure implicaria conectar-se aos pontos de extremidade VPN no hub. Digamos que trata-se de VPN de unidade de escala 1 = 500 Mbps, isso é cobrado USD 0,361/hora. Cada conexão é cobrada a US$ 0,05/hora. Para 10 conexões, o preço total de serviço/hora seria US$ 0,361 + US$ 0,5/hora. São aplicados encargos de dados para o tráfego que sai do Azure.

* Há uma cobrança de hub adicional. Confira a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Se você tivesse o gateway do ExpressRoute devido à conexão de circuitos do ExpressRoute a um hub virtual, você pagaria pelo preço da unidade de escala. Cada unidade de escala no ER é de 2 Gbps e cada unidade de conexão é cobrada com a mesma taxa que a unidade de conexão de VPN.

* Se você tivesse VNETs de spoke conectadas ao hub, os encargos de emparelhamento nas VNETs de spoke ainda seriam aplicados. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como integrar novos parceiros que não estejam na sua lista de parceiros de lançamento?

Todas as APIs de WAN Virtual são de API aberta. Você pode examinar a documentação para avaliar a viabilidade técnica. Se você tiver alguma pergunta, envie um email para azurevirtualwan@microsoft.com. Um parceiro ideal é aquele que tem um dispositivo que pode ser provisionado para conectividade IPsec IKEv1 ou IKEv2.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou usando não estiver na lista de parceiros da WAN Virtual? Ainda posso usá-lo para se conectar à VPN de WAN Virtual do Azure?

Sim, desde que o dispositivo seja compatível com IPsec IKEv1 ou IKEv2. Os parceiros WAN virtuais automatizam a conectividade do dispositivo aos pontos de extremidade de VPN do Azure. Isso implica automatizar etapas como “upload de informações de branch”, “IPsec e configuração” e “conectividade”. Uma vez que o dispositivo não é proveniente de um ecossistema de parceiro de WAN Virtual, você precisará fazer o trabalho pesado de realizar manualmente a configuração do Azure e atualizar seu dispositivo para configurar a conectividade IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir a WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de uma WAN Virtual com um hub e um vpnsite pode ser criada usando um [modelo de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). A WAN Virtual é basicamente um serviço controlado por REST ou pelo portal.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O emparelhamento VNet Global é compatível com a WAN Virtual do Azure? 

Você pode conectar uma VNet em uma região diferente da WAN Virtual.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>VNets spoke conectadas a um hub virtual podem se comunicar umas com as outras (Trânsito do V2V)?

Sim. A WAN Virtual padrão é compatível com a conectividade transitiva Vnet para Vnet por meio do hub da WAN Virtual ao qual as Vnets estão conectadas. Na terminologia da WAN Virtual, esses caminhos são denominados "trânsito local de VNet da WAN Virtual" para VNets conectadas a um Hub da WAN Virtual dentro de uma única região e "trânsito global de VNet da WAN Virtual" para VNets conectadas por meio de vários Hubs da WAN Virtual entre duas ou mais regiões. O trânsito de VNet é compatível com até 3 Gbps de taxa de transferência durante a versão prévia pública. A taxa de transferência será expandida quando o trânsito global entrar em disponibilidade para o público geral.

OBSERVAÇÃO:  Atualmente, a versão prévia de trânsito do V2V exige que um VPN GW seja implantado em um Hub virtual para disparar os elementos de roteamento a serem iniciados. Este VPN GW não é usado para o caminho de trânsito do V2V. Essa é uma limitação conhecida e será removida no momento da disponibilização geral do V2V. Você pode excluir o Gateway de VPN nos hubs após o lançamento completo, pois ele não é necessário para a funcionalidade de trânsito da V2V. 

Em alguns cenários, Vnets spoke também podem ser diretamente emparelhadas entre si usando o [Emparelhamento de Rede Virtual](../articles/virtual-network/virtual-network-peering-overview.md), além do trânsito local ou global de VNet da WAN Virtual. Nesse caso, o Emparelhamento VNET tem precedência sobre a conexão transitiva por meio do Hub da WAN Virtual. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma conexão de ramificação WAN Virtual do Azure?

Uma conexão de um dispositivo de ramificação na WAN Virtual do Azure dá suporte a até quatro links. Um link é o vínculo de conectividade física na localização do branch (por exemplo: ATT, Verizon etc.). Cada conexão de link é composta por dois túneis IPsec ativos/ativos.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade de branch para branch é permitida na WAN Virtual?

Sim, a conectividade de branch a branch está disponível na WAN Virtual para VPN e da VPN para o ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de branch para branch atravessa a WAN Virtual do Azure?

Sim.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>A WAN Virtual exige o ExpressRoute de cada site?

Não, a WAN Virtual não exige o ExpressRoute de cada site. Ela usa a conectividade de site a site IPsec padrão por meio de links da Internet do dispositivo para um hub de WAN Virtual do Azure. Seus sites podem estar conectados a uma rede do provedor usando um circuito do ExpressRoute. Para os sites conectados usando o ExpressRoute em um hub virtual, os sites podem ter um fluxo de tráfego branch a branch entre a VPN e o ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há um limite de taxa de transferência de rede ao usar a WAN Virtual do Azure?

O número de branches é limitado a 1000 conexões por hub/região e um total de 20 Gbps no hub. Você pode ter um hub por região.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Um hub de WAN Virtual dá suporte a quantas conexões de VPN?

Um hub de WAN Virtual do Azure pode oferecer suporte a até 1.000 conexões site a site, 10.000 conexões ponto a site e quatro conexões ExpressRoute simultaneamente.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>O que é a taxa de transferência total do VPN de um túnel VPN e de uma conexão?

A taxa de transferência total do VPN de um hub é de até 20 Gbps com base na unidade de escala escolhida. Taxa de transferência é compartilhada por todas as conexões existentes. Cada túnel em uma conexão pode oferecer suporte a até 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Não vejo a configuração de 20 Gbps para o hub virtual no portal. Como fazer para configurar isso?

Navegue até o gateway de VPN dentro de um hub no portal e clique na unidade de escala para alterá-la para a configuração apropriada.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo local utilize vários ISPs em paralelo ou sempre será um único túnel VPN?
As soluções de dispositivo local podem aplicar políticas de tráfego para direcionar o tráfego entre vários túneis para o Azure.


### <a name="what-is-global-transit-architecture"></a>O que é a arquitetura de trânsito global?

Para obter informações sobre a arquitetura de trânsito global, confira [arquitetura de rede de trânsito global e WAN Virtual](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como o tráfego é roteado no backbone do Azure?

O tráfego segue o padrão: dispositivo branch -> ISP -> Borda da rede Microsoft -> Microsoft DC (hub VNet) -> Borda da rede Microsoft -> ISP -> dispositivo branch

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Nesse modelo, o que você precisa em cada site? Apenas uma conexão com a Internet?

Sim. Uma conexão de Internet e um dispositivo físico que é compatível com IPsec, preferencialmente nossos [parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md) integrados. Opcionalmente, você pode gerenciar manualmente a configuração e a conectividade com o Azure usando seu dispositivo preferido.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Como fazer habilitar a rota padrão (0.0.0.0/0) em uma conexão (VPN, ExpressRoute ou Rede Virtual):

Um hub virtual poderá propagar uma rota padrão aprendida para uma conexão VPN/ExpressRoute de rede virtual/site a site se o sinalizador for 'Habilitado' na conexão. Esse sinalizador fica visível quando o usuário edita uma conexão de rede virtual, uma conexão VPN ou uma conexão ExpressRoute. Por padrão, esse sinalizador é desabilitado quando um site ou um circuito ExpressRoute é conectado a um hub. Ele é habilitado por padrão quando uma conexão de rede virtual é adicionada para conectar uma VNet a um hub virtual. A rota padrão não é originada no hub de WAN Virtual; a rota padrão é propagada se já foi aprendida pelo hub de WAN Virtual como resultado da implantação de um firewall no hub ou se a opção de túnel forçado está habilitada em outro site conectado.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Como o hub virtual em uma WAN Virtual seleciona o melhor caminho para uma rota de vários hubs

Se um hub virtual aprende a mesma rota de vários hubs remotos, a ordem na qual ele decide é a mostrada a seguir
1. Correspondência de prefixo mais longa
2. Rotas locais em interhub
3. Rotas estáticas por BGP
4. ER (ExpressRoute) por VPN
5. Comprimento do caminho de sistema autônomo

O trânsito entre ER e ER é sempre por meio do alcance Global porque, se a solicitação vier por ER em um hub e houver uma VPN e um ER em um hub remoto, a VPN terá preferência ao ER de um hub remoto para alcançar um ponto de extremidade conectado por VPN ou ER no hub remoto


### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Há suporte para IPv6 na WAN Virtual?

Não há suporte para IPv6 no hub WAN Virtual e seus gateways. Se você tiver uma VNET compatível com IPv6 e quiser conectar a VNET à WAN Virtual, esse cenário também não terá suporte. 

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quais são as diferenças entre os tipos de WAN Virtual (Básico e Standard)?

O tipo de WAN Básico permite criar um hub Básico (SKU = Básico). Um tipo de WAN Standard permite criar um hub Standard (SKU = Standard). Os hubs Básicos são limitados à funcionalidade de VPN site a site. Os hubs Standard permitem que você tenha o ExpressRoute, a VPN de usuário (P2S), o hub de malha completa e o trânsito de VNet a VNet por meio dos hubs. Você paga um preço base de US$ 0,25/h para os hubs Standard e uma taxa de processamento de dados para o tráfego por meio dos hubs durante a conectividade de VNet para VNet, bem como o processamento de dados para o tráfego de hub para hub. Para obter mais informações, confira autenticação [WANs Virtuais Básicas e Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Para obter os preços, confira a página de [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).
