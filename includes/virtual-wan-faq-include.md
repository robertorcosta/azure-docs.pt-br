---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 28ea1e68441a57d67fef1e78153e00eb1bd09211
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143926"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>O usuário precisa ter hub e spoke com dispositivos SD-WAN/VPN para usar a WAN virtual do Azure?

A WAN virtual fornece muitas funcionalidades incorporadas em um único painel de vidro, como conectividade VPN site a site/site, conectividade de usuário/P2S, conectividade de ExpressRoute, conectividade de rede virtual, interconectividade VPN ExpressRoute, conectividade transitiva VNet a VNet, roteamento centralizado, firewall do Azure e segurança do Gerenciador de firewall, monitoramento, criptografia ExpressRoute e muitos outros recursos. Você não precisa ter todos esses casos de uso para começar a usar a WAN virtual. Você pode começar com apenas um caso de uso. A arquitetura WAN virtual é uma arquitetura de Hub e spoke com escala e desempenho internos em que as ramificações (dispositivos VPN/SD-WAN), usuários (clientes VPN do Azure, openVPN ou clientes IKEv2), circuitos de ExpressRoute, redes virtuais servem como spokes para hubs virtuais. Todos os hubs são conectados em malha completa em uma WAN Virtual Padrão, o que facilita para o usuário usar o backbone da Microsoft para conectividade de qualquer um para qualquer um (qualquer spoke). Para o hub e spoke com dispositivos SD-WAN/VPN, os usuários podem configurá-lo manualmente no portal da WAN Virtual do Azure ou usar o CPE do Parceiro da WAN Virtual (SD-WAN/VPN) para configurar a conectividade com o Azure. Os parceiros de WAN virtuais fornecem automação para conectividade, que é a capacidade de exportar as informações do dispositivo para o Azure, baixar a configuração do Azure e estabelecer a conectividade com o Hub de WAN virtual do Azure. Para a conectividade de VPN ponto a site/usuário, damos suporte ao cliente [VPN do Azure](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN ou cliente IKEv2. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Você pode desabilitar hubs totalmente entrelaçados em uma WAN virtual?

A WAN virtual vem em dois tipos: básico e Standard. Na WAN virtual básica, os hubs não são entrelaçados. Em uma WAN virtual padrão, os hubs são malhados e conectados automaticamente quando a WAN virtual é configurada pela primeira vez. O usuário não precisa fazer nada específico. O usuário também não precisa desabilitar ou habilitar a funcionalidade para obter hubs em malha. A WAN virtual fornece várias opções de roteamento para direcionar o tráfego entre qualquer spoke (VNet, VPN ou ExpressRoute). Ele fornece a facilidade de hubs totalmente entrelaçados e também a flexibilidade de roteamento de tráfego de acordo com suas necessidades. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Como Zonas de Disponibilidade e resiliência são manipuladas na WAN virtual?

A WAN virtual é uma coleção de hubs e serviços disponibilizados dentro do Hub. O usuário pode ter tantas WANs virtuais de acordo com suas necessidades. Em um hub de WAN virtual, há vários serviços como VPN, ExpressRoute, etc. Cada um desses serviços (exceto o Firewall do Azure) é implantado em uma região Zonas de Disponibilidade, ou seja, se a região oferece suporte a Zonas de Disponibilidade. Se uma região se tornar uma zona de disponibilidade após a implantação inicial no Hub, o usuário poderá recriar os gateways, o que irá disparar uma implantação de zona de disponibilidade. Todos os gateways são provisionados em um hub como ativo-ativo, o que significa que há resiliência interna dentro de um Hub. Os usuários podem se conectar a vários hubs se desejarem resiliência entre regiões. Embora o conceito de WAN virtual seja global, o recurso de WAN virtual real é baseado no Resource Manager e implantado de forma regional. Se a região da WAN virtual em si tiver um problema, todos os hubs nessa WAN virtual continuarão a funcionar como estão, mas o usuário não poderá criar novos hubs até que a região WAN virtual esteja disponível.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Com qual cliente a VPN de Usuário da WAN Virtual do Azure (ponto a site) é compatível?

A WAN virtual é compatível com [cliente da VPN do Azure](https://go.microsoft.com/fwlink/?linkid=2117554), cliente OpenVPN ou qualquer cliente IKEv2. A autenticação do Azure AD é compatível com o cliente VPN do Azure. É necessário um sistema operacional cliente Windows 10 versão 17763.0 ou superior.  Os clientes OpenVPN podem dar suporte à autenticação baseada em certificado. Depois que a autenticação baseada em certificado for selecionada no gateway, você verá o arquivo *. ovpn* para baixar em seu dispositivo. O IKEv2 dá suporte a autenticação de certificado e RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Para VPN de usuário (ponto a site) – por que o pool de clientes P2S é dividido em duas rotas?

Cada gateway tem duas instâncias, a divisão acontece para que cada instância de gateway possa alocar IPs de cliente de maneira independente para clientes conectados e o tráfego da rede virtual seja roteado de volta para a instância de gateway correta para evitar o salto de instância entre gateways.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Como faço para adicionar servidores DNS a clientes P2S?

Há duas opções para adicionar servidores DNS aos clientes do P2S. O primeiro método é preferencial, pois adiciona os servidores DNS personalizados ao gateway em vez do cliente.

1. Use o seguinte script do PowerShell para adicionar os servidores DNS personalizados. Substitua os valores do seu ambiente.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Ou, se você estiver usando o cliente VPN do Azure para Windows 10, poderá modificar o arquivo XML do perfil baixado e adicionar as marcas **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** antes de importá-lo.

   ```powershell
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

Cada gateway P2S VPN de usuário tem duas instâncias e cada instância é compatível com até um número de usuários determinado à medida que a unidade de escala muda. A unidade de escala 1-3 dá suporte a 500 conexões, unidade de escala 4-6 dá suporte a conexões 1000, unidade de escala 7-12 dá suporte a conexões de 5000 e unidade de escala 13-20 dá suporte a até 10.000 conexões. 

Como exemplo, digamos que o usuário escolha uma unidade de escala. Cada unidade de escala implica que um gateway ativo-ativo implantado e cada uma das instâncias (neste caso, 2) ofereceria suporte a até 500 conexões. Como você pode obter 500 conexões * 2 por gateway, isso não significa que você planeje 1000 em vez de 500 para essa unidade de escala. As instâncias podem precisar ser atendidas durante a qual a conectividade para o 500 extra pode ser interrompida se você ultrapassar a contagem de conexões recomendada. Além disso, não se esqueça de planejar o tempo de inatividade caso você decida expandir ou reduzir verticalmente na unidade de escala ou alterar a configuração de ponto a site no gateway de VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um gateway de VPN da WAN Virtual do Azure?

A WAN Virtual fornece conectividade site a site em larga escala e é criada visando produtividade, escalabilidade e facilidade de uso. Quando você conecta um site a um gateway de VPN de WAN Virtual, ele é diferente de um gateway de rede virtual comum que usa um tipo de gateway 'VPN'. Da mesma forma, quando você conecta um circuito de ExpressRoute a um hub de WAN Virtual, ele usa para o gateway de ExpressRoute um recurso diferente daquele usado pelo gateway de rede virtual regular, o qual usa o tipo de gateway 'ExpressRoute'. 

A WAN Virtual dá suporte a uma taxa de transferência de agregação de até 20 Gbps para VPN e ExpressRoute. A WAN Virtual também tem automação para conectividade com um ecossistema de parceiros de dispositivo de ramificação CPE. Os dispositivos da ramificação CPE têm automação interna que AutoProvisiona e se conecta à WAN virtual do Azure. Esses dispositivos estão disponíveis em um ecossistema crescente de SD-WAN e parceiros de VPN. Confira a [Lista de Parceiros Preferenciais](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Como a WAN Virtual é diferente de um gateway de rede virtual do Azure existente?

A VPN do Gateway de Rede Virtual é limitada a 30 túneis. Para conexões, você deve usar a WAN Virtual para VPN em larga escala. Você pode conectar até 1.000 conexões de ramificação por região (hub virtual) com agregação de 20 Gbps por hub. Uma conexão é um túnel de ativo-ativo do dispositivo VPN local para o hub virtual. Pode haver um hub por região, o que significa que você pode conectar a mais de 1.000 branches entre os hubs.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>O que é uma Unidade de Escala de Gateway da WAN Virtual

Uma unidade de escala é uma unidade definida para escolher uma taxa de transferência agregada de um gateway no Hub virtual. 1 unidade de escala de VPN = 500 Mbps. 1 unidade de escala do ExpressRoute = 2 Gbps. Exemplo: 10 unidade de escala de VPN significaria 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quais provedores de dispositivo (parceiros de WAN Virtual) são compatíveis?

Neste momento, muitos parceiros dão suporte à experiência de WAN Virtual totalmente automatizada. Para saber mais, confira [Parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são as etapas de automação de parceiro de WAN Virtual?

Para conhecer as etapas de automação de parceiro, confira [Automação de parceiro de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Eu sou obrigado a usar um dispositivo de parceiro preferido?

Não. Você pode usar qualquer dispositivo com capacidade para VPN que siga os requisitos do Azure para suporte a IPsec IKEv2/IKEv1. A WAN virtual também tem soluções de parceiros de CPE que automatizam a conectividade com a WAN virtual do Azure, facilitando a configuração de conexões VPN IPsec em escala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

Soluções de conectividade definidas pelo software normalmente gerenciam seus dispositivos de branch usando um controlador ou um centro de provisionamento de dispositivos. O controlador pode usar as APIs do Azure para automatizar a conectividade com a WAN Virtual do Azure. A automação inclui o upload de informações de ramificação, o download da configuração do Azure, a configuração de túneis IPsec em hubs virtuais do Azure e a configuração automática da conectividade do dispositivo de ramificação para a WAN Virtual do Azure. Quando você tem centenas de ramificações, é fácil conectar-se usando parceiros CPE da WAN Virtual, pois a experiência de integração elimina a necessidade de configurar e gerenciar a conectividade IPsec em larga escala. Para saber mais, confira [Automação de parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou usando não estiver na lista de parceiros da WAN Virtual? Ainda posso usá-lo para se conectar à VPN de WAN Virtual do Azure?

Sim, desde que o dispositivo seja compatível com IPsec IKEv1 ou IKEv2. Os parceiros WAN virtuais automatizam a conectividade do dispositivo aos pontos de extremidade de VPN do Azure. Isso implica automatizar etapas como “upload de informações de branch”, “IPsec e configuração” e “conectividade”. Uma vez que o dispositivo não é proveniente de um ecossistema de parceiro de WAN Virtual, você precisará fazer o trabalho pesado de realizar manualmente a configuração do Azure e atualizar seu dispositivo para configurar a conectividade IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como integrar novos parceiros que não estejam na sua lista de parceiros de lançamento?

Todas as APIs de WAN Virtual são de API aberta. Você pode passar pela documentação de [automação de parceiros de WAN virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) para avaliar a viabilidade técnica. Um parceiro ideal é aquele que tem um dispositivo que pode ser provisionado para conectividade IPsec IKEv1 ou IKEv2. Depois que a empresa tiver concluído o trabalho de automação para o dispositivo CPE com base nas diretrizes de automação fornecidas acima, você poderá entrar em contato com a azurevirtualwan@microsoft.com [conectividade por meio de parceiros]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Se você for um cliente que gostaria que uma determinada solução da empresa fosse listada como um parceiro de WAN virtual, faça com que a empresa entre em contato com a WAN virtual enviando um email para azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como o WAN Virtual dá suporte aos dispositivos SD-WAN?

Parceiros WAN Virtuais automatizam a conectividade IPsec com pontos de extremidade de VPN do Azure. Se o parceiro WAN Virtual é um provedor de SD-WAN, está implícito que o controlador de SD-WAN gerencia a automação e conectividade IPsec aos pontos de extremidade do Azure VPN. Se o dispositivo de SD-WAN requer seu próprio ponto de extremidade de VPN do Azure para qualquer funcionalidade SD-WAN proprietária, você pode implantar o ponto de extremidade SD-WAN em uma Rede Virtual do Azure e coexiste com o WAN Virtual do Azure.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem se conectar a um único hub?

O hub virtual é compatível com até 1.000 conexões. Cada conexão é composta por quatro links, cada um dos quais dá suporte a dois túneis que estão em uma configuração ativo/ativo. Os túneis terminam em um gateway de VPN do Hub virtual do Azure. Os links representam o link do ISP físico no dispositivo de filial/VPN.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma conexão de ramificação WAN Virtual do Azure?

Uma conexão de um dispositivo de filial ou VPN na WAN virtual do Azure é nada menos uma conexão VPN que se conecta virtualmente ao site de VPN e ao gateway de VPN do Azure em um hub virtual.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN local pode se conectar a vários hubs?

Sim. O fluxo de tráfego ao começar é do dispositivo local para a borda de rede da Microsoft mais próxima e, em seguida, para o hub virtual.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Há novos recursos do Gerenciador de recursos disponíveis para WAN Virtual?
  
Sim, a WAN virtual tem novos recursos do Resource Manager. Para saber mais, confira a [Visão geral](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implantar e usar minha solução de virtualização de rede favorita (em uma VNet NVA) com a WAN Virtual do Azure?

Sim, você pode conectar a VNET da sua NVA (solução de virtualização de rede) favorita à WAN Virtual do Azure.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso criar uma solução de virtualização de rede dentro do hub virtual?

Uma NVA (solução de virtualização de rede) não pode ser implantada dentro de um hub virtual. No entanto, você pode criá-lo em uma VNet spoke que está conectada ao Hub virtual e habilitar o roteamento apropriado para o tráfego direto de acordo com suas necessidades.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Uma VNet spoke pode ter um gateway de rede virtual?

Não. A VNet spoke não poderá ter um gateway de rede virtual se estiver conectada ao hub virtual.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Há suporte para BGP na conectividade VPN?

Sim, há suporte para BGP. Quando você cria um site VPN, você pode fornecer os parâmetros de BGP nele. Isso implicará que todas as conexões criadas no Azure para o site serão habilitadas para BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Há quaisquer informações de licenciamento ou preços para a WAN Virtual?

Sim. Confira a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir a WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de uma WAN Virtual com um hub e um vpnsite pode ser criada usando um [modelo de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). A WAN Virtual é basicamente um serviço controlado por REST ou pelo portal.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>VNets spoke conectadas a um hub virtual podem se comunicar umas com as outras (Trânsito do V2V)?

Sim. A WAN virtual padrão dá suporte à conectividade transitiva VNet a VNet por meio do Hub WAN virtual ao qual os VNets estão conectados. Na terminologia da WAN Virtual, esses caminhos são denominados "trânsito local de VNet da WAN Virtual" para VNets conectadas a um Hub da WAN Virtual dentro de uma única região e "trânsito global de VNet da WAN Virtual" para VNets conectadas por meio de vários Hubs da WAN Virtual entre duas ou mais regiões. Para alguns cenários, o spoke VNets também pode ser diretamente emparelhado entre si usando o [emparelhamento de rede virtual](../articles/virtual-network/virtual-network-peering-overview.md) , além do trânsito local ou global da VNET de WAN virtual. Nesse caso, o emparelhamento VNet tem precedência sobre a conexão transitiva por meio do Hub WAN virtual.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade de branch para branch é permitida na WAN Virtual?

Sim, a conectividade de branch para branch está disponível na WAN Virtual. O Branch é conceitualmente aplicável ao site de VPN, circuitos de ExpressRoute ou usuários de VPN de ponto a site/usuário. A habilitação da ramificação para o Branch é habilitada por padrão e pode ser localizada em definições de configuração de WAN. Isso permite que os usuários/branches de VPN se conectem a outras ramificações de VPN, bem como a conectividade de trânsito é habilitada entre os usuários de VPN e ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de branch para branch atravessa a WAN Virtual do Azure?

Sim.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>A WAN Virtual exige o ExpressRoute de cada site?

Não. A WAN virtual não requer o ExpressRoute de cada site. Seus sites podem estar conectados a uma rede do provedor usando um circuito do ExpressRoute. Para sites conectados usando o ExpressRoute a um hub virtual, bem como VPN IPsec no mesmo Hub, o Hub virtual fornece conectividade de trânsito entre o usuário de VPN e do ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Há uma taxa de transferência de rede ou um limite de conexão ao usar a WAN virtual do Azure?

A taxa de transferência de rede é por serviço em um hub de WAN virtual. Embora você possa ter tantas WANs virtuais quanto desejar, cada WAN virtual permite 1 hub por região. Em cada Hub, a taxa de transferência de agregação de VPN é de até 20 Gbps, a taxa de transferência agregada do ExpressRoute é de até 20 Gbps e a taxa de transferência agregada VPN do usuário/ponto a site é de até 20 Gbps. O roteador no Hub virtual dá suporte a até 50 Gbps para fluxos de tráfego de VNet a VNet e assume um total de 2000 de carga de trabalho de VM em todos os VNets em hubs de WAN virtual.

Quando os sites VPN se conectam a um Hub, eles fazem isso com conexões. A WAN virtual dá suporte a até 1000 conexões ou 2000 túneis IPsec por Hub virtual. Quando os usuários remotos se conectam ao Hub virtual, eles se conectam ao gateway de VPN P2S, que dá suporte a até 10.000 usuários, dependendo da unidade de escala (largura de banda) escolhida para o gateway de VPN P2S no Hub virtual.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>O que é a taxa de transferência total do VPN de um túnel VPN e de uma conexão?

A taxa de transferência de VPN total de um hub é de até 20 Gbps com base na unidade de escala escolhida do gateway de VPN. Taxa de transferência é compartilhada por todas as conexões existentes. Cada túnel em uma conexão pode oferecer suporte a até 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Não vejo a configuração de 20 Gbps para o Hub virtual no Portal. Como fazer para configurar isso?

Navegue até o gateway de VPN dentro de um hub no portal e clique na unidade de escala para alterá-la para a configuração apropriada.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo local utilize vários ISPs em paralelo ou sempre será um único túnel VPN?

As soluções de dispositivo local podem aplicar políticas de tráfego para direcionar o tráfego entre vários túneis no Hub de WAN virtual do Azure (gateway de VPN no Hub virtual).

### <a name="what-is-global-transit-architecture"></a>O que é a arquitetura de trânsito global?

Para obter informações sobre a arquitetura de trânsito global, confira [arquitetura de rede de trânsito global e WAN Virtual](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como o tráfego é roteado no backbone do Azure?

O tráfego segue o padrão: dispositivo branch -> ISP -> Borda da rede Microsoft -> Microsoft DC (hub VNet) -> Borda da rede Microsoft -> ISP -> dispositivo branch

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Nesse modelo, o que você precisa em cada site? Apenas uma conexão com a Internet?

Sim. Uma conexão de Internet e um dispositivo físico que é compatível com IPsec, preferencialmente nossos [parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md) integrados. Opcionalmente, você pode gerenciar manualmente a configuração e a conectividade com o Azure usando seu dispositivo preferido.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Como fazer habilitar a rota padrão (0.0.0.0/0) em uma conexão (VPN, ExpressRoute ou Rede Virtual):

Um hub virtual poderá propagar uma rota padrão aprendida para uma conexão VPN/ExpressRoute de rede virtual/site a site se o sinalizador for 'Habilitado' na conexão. Esse sinalizador fica visível quando o usuário edita uma conexão de rede virtual, uma conexão VPN ou uma conexão ExpressRoute. Por padrão, esse sinalizador é desabilitado quando um site ou um circuito ExpressRoute é conectado a um hub. Ele é habilitado por padrão quando uma conexão de rede virtual é adicionada para conectar uma VNet a um hub virtual. A rota padrão não é originada no hub de WAN Virtual; a rota padrão é propagada se já foi aprendida pelo hub de WAN Virtual como resultado da implantação de um firewall no hub ou se a opção de túnel forçado está habilitada em outro site conectado.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Como o hub virtual em uma WAN Virtual seleciona o melhor caminho para uma rota de vários hubs

Se um hub virtual aprende a mesma rota de vários hubs remotos, a ordem na qual ele decide é a seguinte:

1. Correspondência de prefixo mais longa.
2. Rotas locais em interhub.
3. Rotas estáticas sobre BGP: esse é um contexto para a decisão que está sendo tomada pelo roteador do Hub virtual. No entanto, se o tomador de decisões for o gateway de VPN em que um site anuncia rotas via BGP ou fornece prefixos de endereço estático, rotas estáticas podem ser preferidas sobre rotas BGP.
4. ExpressRoute (ER) sobre VPN: o ER é preferencial sobre VPN quando o contexto é um hub local. A conectividade de trânsito entre circuitos do ExpressRoute só está disponível por meio de Alcance Global. Portanto, em cenários em que o circuito do ExpressRoute está conectado a um Hub e há outro circuito do ExpressRoute conectado a um Hub diferente com conexão VPN, a VPN pode ser preferida para cenários entre hubs.
5. COMO comprimento do caminho.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>O Hub de WAN virtual permite a conectividade entre os circuitos do ExpressRoute.

O trânsito entre ER-to-ER é sempre por meio do alcance global. Os gateways de Hub virtual são implantados no controlador de domínio ou nas regiões do Azure. Quando dois circuitos de ExpressRoute se conectam por meio do alcance global, não há necessidade de que o tráfego chegue a todo o caminho dos roteadores de borda para o controlador de domínio do Hub virtual.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-circuits-or-vpn-connections"></a>Há um conceito de peso em conexões VPN ou circuitos de WAN virtual do Azure

Quando vários circuitos de ExpressRoute estão conectados a um hub virtual, o peso de roteamento na conexão fornece um mecanismo para o ExpressRoute no Hub virtual para preferir um circuito em relação ao outro. Não há nenhum mecanismo para definir um peso em uma conexão VPN. O Azure sempre prefere uma conexão de ExpressRoute em uma conexão VPN em um único Hub.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Quando dois hubs (hub 1 e 2) estiverem conectados e houver um circuito de ExpressRoute conectado como uma ligação de arco para ambos os hubs, qual será o caminho para uma VNet conectada ao hub 1 para alcançar uma VNet conectada no Hub 2?

O comportamento atual é preferir o caminho do circuito do ExpressRoute em relação ao Hub para o Hub para conectividade VNet a VNet. No entanto, isso não é incentivado em uma configuração de WAN virtual. A equipe de WAN virtual está trabalhando em uma correção para habilitar a preferência de Hub para Hub no caminho do ExpressRoute. A recomendação é para vários circuitos de ExpressRoute (provedores diferentes) para se conectar a um Hub e usar a conectividade Hub para Hub fornecida pela WAN virtual para fluxos de tráfego entre regiões.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Há suporte para IPv6 na WAN Virtual?

Não há suporte para IPv6 no Hub WAN virtual e em seus gateways. Se você tiver uma VNet com suporte a IPv6 e quiser conectar a VNet à WAN virtual, esse cenário não tem suporte no momento.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quais são as diferenças entre os tipos de WAN Virtual (Básico e Standard)?

Consulte [WANs virtuais básicas e Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Para obter os preços, confira a página de [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).
