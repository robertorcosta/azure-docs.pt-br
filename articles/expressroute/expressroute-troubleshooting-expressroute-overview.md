---
title: 'Azure ExpressRoute: Verifique a conectividade - Guia de solução de problemas'
description: Esta página fornece instruções sobre solução de problemas e validação de conectividade de ponta a ponta de um circuito ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330950"
---
# <a name="verifying-expressroute-connectivity"></a>Verificando a conectividade do ExpressRoute
Este artigo ajuda você a verificar e solucionar problemas de conectividade do ExpressRoute. O ExpressRoute estende uma rede on-premises na nuvem da Microsoft por uma conexão privada que é comumente facilitada por um provedor de conectividade. A conectividade ExpressRoute tradicionalmente envolve três zonas de rede distintas, da seguinte forma:

-   Rede do Cliente
-   Rede do Provedor
-   Microsoft Datacenter

> [!NOTE]
> No modelo de conectividade direta ExpressRoute (oferecido a 10/100 Gbps de largura de banda), os clientes podem se conectar diretamente à porta dos roteadores Microsoft Enterprise Edge (MSEE). Portanto, no modelo de conectividade direta, existem apenas zonas de rede de clientes e Microsoft.
>


O objetivo deste documento é ajudar o usuário a identificar se e onde existe um problema de conectividade. Assim, para ajudar a buscar apoio da equipe apropriada para resolver um problema. Se o suporte da Microsoft for necessário para resolver um problema, abra um tíquete de suporte com [Suporte da Microsoft][Support].

> [!IMPORTANT]
> Este documento tem como intenção ajudar você a diagnosticar e corrigir problemas simples. Ela não deve ser usado como uma substituição ao suporte da Microsoft. Abra um tíquete de suporte com o [Suporte da Microsoft][Support] se não conseguir resolver o problema usando as diretrizes fornecidas.
>
>

## <a name="overview"></a>Visão geral
O diagrama a seguir mostra a conectividade lógica de uma rede do cliente de rede da Microsoft usando o ExpressRoute.
[![1]][1]

No diagrama acima, os números indicam os principais pontos de rede. Esses pontos de rede são referenciados neste artigo às vezes pelo seu número associado. Dependendo do modelo de conectividade ExpressRoute - Co-localização do Cloud Exchange, Conexão Ethernet ponto a ponto ou IpVPN (Qualquer-para-qualquer)-- os pontos de rede 3 e 4 podem ser switches (dispositivos de camada 2) ou roteadores (dispositivos de camada 3). No modelo de conectividade direta, não há pontos de rede 3 e 4; em vez disso, os Esc (2) estão diretamente conectados aos MSEEs através de fibra escura. Os principais pontos de rede ilustrados são os seguintes:

1.  Dispositivo de computação de cliente (por exemplo, um servidor ou PC)
2.  CEs: roteadores de borda do cliente 
3.  PEs (voltados para CE): roteadores/comutadores de borda do provedor que estão voltados para os roteadores de borda do cliente. Mencionados como PE CEs neste documento.
4.  PEs (voltados para MSEE): roteadores/comutadores de borda do provedor que estão voltados para MSEEs. Mencionados como PE-MSEEs neste documento.
5.  MSEEs: roteadores ExpressRoute do MSEE (Microsoft Enterprise Edge)
6.  Gateway de Rede Virtual (VNet)
7.  Dispositivo de computação na VNet do Azure

Se forem usados modelos de co-localização, ethernet ponto a ponto ou conectividade direta, os CEs (2) estabelecerão o peering BGP com os MSEEs (5). 

Se o modelo de conectividade Any-to-any (IPVPN) for usado, PE-MSEEs (4) estabelecerá o peering BGP com MSEEs (5). Os PE-MSEEs propagam as rotas recebidas da Microsoft de volta à rede de clientes através da rede de provedores de serviços IPVPN.

> [!NOTE]
>Para alta disponibilidade, a Microsoft estabelece uma conectividade paralela totalmente redundante entre os pares MSEEs (5) e PE-MSEEs (4). Um caminho de rede paralelo totalmente redundante também é incentivado entre a rede de clientes e o par PE-CEs. Para obter mais informações sobre alta disponibilidade, consulte o artigo [Design for high availability com expressRoute][HA]
>
>

A seguir estão as etapas lógicas, na solução de problemas do circuito ExpressRoute:

* [Verificar o provisionamento do circuito e o estado](#verify-circuit-provisioning-and-state)
  
* [Validar Configuração de Emparelhamento](#validate-peering-configuration)
  
* [Validar aARP](#validate-arp)
  
* [Validar BGP e rotas no MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Confirme o fluxo de tráfego](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verificar o provisionamento do circuito e o estado
O provisionamento de um circuito ExpressRoute estabelece uma camada redundante de conexões de camada 2 entre CEs/PE-MSEEs (2)/(4) e MSEEs (5). Para obter mais informações sobre como criar, modificar, provisionar e verificar um circuito do ExpressRoute, consulte o artigo [Create and modify an ExpressRoute circuit][CreateCircuit] (Criar e modificar um circuito do ExpressRoute).

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito do ExpressRoute. Se você precisar de assistência da Microsoft ou de um parceiro ExpressRoute para solucionar um problema do ExpressRoute, forneça a chave de serviço para identificar prontamente o circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificação por meio do Portal do Azure
No portal Azure, abra a lâmina do circuito ExpressRoute. Na ![seção 3][3] da lâmina, os itens essenciais expressRoute são listados como mostrado na captura de tela a seguir:

![4][4]    

Nas Informações Gerais do ExpressRoute, o *Status do circuito* indica o status do circuito no lado da Microsoft. *Status do provedor* indica se o circuito foi *Provisionado/Não provisionado* no lado do provedor de serviços. 

Para que um circuito do ExpressRoute esteja operacional, o *Status do circuito* deve ser *Habilitado* e o *Status do provedor* deve ser *Provisionado*.

> [!NOTE]
> Depois de configurar um circuito ExpressRoute, se o *status do Circuito* estiver atingido em status não habilitado, entre em contato com o Suporte da [Microsoft][Support]. Por outro lado, se o *status do Provedor* for atingido em status não provisionado, entre em contato com o provedor de serviços.
>
>

### <a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para listar todos os circuitos do ExpressRoute em um grupo de recursos, use o seguinte comando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Se você estiver procurando o nome de um grupo de recursos, você pode obtê-lo listando todos os grupos de recursos em sua assinatura, usando o comando *Get-AzResourceGroup*
>


Para selecionar um circuito do ExpressRoute específico em um grupo de recursos, use o seguinte comando:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Uma resposta de exemplo é:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Para confirmar se um circuito do ExpressRoute está funcionando, preste atenção especial aos seguintes campos:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Depois de configurar um circuito ExpressRoute, se o *status do Circuito* estiver atingido em status não habilitado, entre em contato com o Suporte da [Microsoft][Support]. Por outro lado, se o *status do Provedor* for atingido em status não provisionado, entre em contato com o provedor de serviços.
>
>

## <a name="validate-peering-configuration"></a>Validar Configuração de Emparelhamento
Depois que o provedor de serviços tiver concluído o provisionamento do circuito ExpressRoute, várias configurações de roteamento baseadas em eBGP podem ser criadas no circuito ExpressRoute entre CEs/MSEE-PEs (2)/(4) e MSEEs (5). Cada circuito do ExpressRoute pode ter: peering privado do Azure (tráfego para redes virtuais privadas no Azure) e/ou peering da Microsoft (tráfego para pontos finais públicos de PaaS e SaaS). Para obter mais informações sobre como criar e modificar a configuração de roteamento, consulte o artigo [Create and modify routing for an ExpressRoute circuit][CreatePeering] (Criar e modificar o roteamento para um circuito do ExpressRoute).

### <a name="verification-via-the-azure-portal"></a>Verificação por meio do Portal do Azure

> [!NOTE]
> No modelo de conectividade IPVPN, os provedores de serviços lidam com a responsabilidade de configurar os peerings (serviços de camada 3). Nesse modelo, após o provedor de serviço configurar um peering e se o peering estiver em branco no portal, tente atualizar a configuração do circuito usando o botão de atualização no portal. Esta operação puxará a configuração de roteamento atual do seu circuito. 
>

No portal Azure, o status de um peering do circuito ExpressRoute pode ser verificado a lâmina do circuito ExpressRoute. Na ![seção 3][3] da lâmina, os peerings expressRoute seriam listados como mostrado na captura de tela a seguir:

![5][5]

No exemplo anterior, como observado o peering privado do Azure é provisionado, enquanto os peerings públicos do Azure e da Microsoft não são provisionados. Um contexto de peering provisionado com sucesso também teria as sub-redes primárias e secundárias ponto a ponto listadas. As sub-redes /30 são usadas para o endereço IP da interface dos MSEEs e CEs/PE-MSEEs. Para os peerings que são provisionados, a listagem também indica quem modificou a configuração pela última vez. 

> [!NOTE]
> Se a habilitação de um peering falhar, verifique se as sub-redes primárias e secundárias atribuídas correspondem à configuração no CE/PE-MSEE vinculado. Verifique também se o *VlanId*correto, *AzureASN*e *PeerASN* são usados em MSEEs e se esses valores são mapeados para os utilizados no CE/PE-MSEE vinculado. Se o hashing MD5 for escolhido, a chave compartilhada deve ser a mesma no par MSEE e PE-MSEE/CE. A chave compartilhada configurada anteriormente não seria exibida por razões de segurança. Se você precisar alterar qualquer uma dessas configurações em um roteador MSEE, consulte [Criar e modificar o roteamento para um circuito ExpressRoute][CreatePeering].  
>

> [!NOTE]
> Em uma sub-rede /30 atribuída à interface, a Microsoft escolherá o segundo endereço IP utilizável da sub-rede para a interface MSEE. Portanto, certifique-se de que o primeiro endereço IP utilizável da sub-rede tenha sido atribuído no CE/PE-MSEE de peered.
>


### <a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para obter os detalhes de configuração de emparelhamento privado do Azure, use os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Uma resposta de exemplo para um emparelhamento privado configurado com êxito é:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Um contexto de emparelhamento habilitado com êxito teria os prefixos dos endereços primário e secundário listados. As sub-redes /30 são usadas para o endereço IP da interface dos MSEEs e CEs/PE-MSEEs.

Para obter os detalhes de configuração de emparelhamento público do Azure, use os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Para obter os detalhes de configuração de emparelhamento da Microsoft, use os seguintes comandos:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Se um emparelhamento não for configurado, haverá uma mensagem de erro. Uma resposta de exemplo, quando o emparelhamento indicado (emparelhamento público do Azure neste exemplo) não está configurado no circuito:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Se a habilitação de um peering falhar, verifique se as sub-redes primárias e secundárias atribuídas correspondem à configuração no CE/PE-MSEE vinculado. Verifique também se o *VlanId*correto, *AzureASN*e *PeerASN* são usados em MSEEs e se esses valores são mapeados para os utilizados no CE/PE-MSEE vinculado. Se o hashing MD5 for escolhido, a chave compartilhada deve ser a mesma no par MSEE e PE-MSEE/CE. A chave compartilhada configurada anteriormente não seria exibida por razões de segurança. Se você precisar alterar qualquer uma dessas configurações em um roteador MSEE, consulte [Criar e modificar o roteamento para um circuito ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> Em uma sub-rede /30 atribuída à interface, a Microsoft escolherá o segundo endereço IP utilizável da sub-rede para a interface MSEE. Portanto, certifique-se de que o primeiro endereço IP utilizável da sub-rede tenha sido atribuído no CE/PE-MSEE de peered.
>

## <a name="validate-arp"></a>Validar aARP

A tabela ARP fornece um mapeamento do endereço IP e do endereço MAC para um peering específico. A tabela ARP para um emparelhamento de circuito de ExpressRoute fornece as seguintes informações para cada interface (primária e secundária):
* Mapeamento do endereço IP da interface do roteador local para o endereço MAC
* Mapeamento do endereço IP da interface do roteador de ExpressRoute para o endereço MAC
* A idade das tabelas ARP de mapeamento pode ajudar a validar a configuração da camada 2 e a solução de problemas básicos de conectividade da camada 2.


Consulte [Obter tabelas ARP no][ARP] documento do modelo de implantação do Gerenciador de recursos, para ver como visualizar a tabela ARP de um peering ExpressRoute e para saber como usar as informações para solucionar problemas de conectividade da camada 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar BGP e rotas no MSEE

Para obter a tabela de roteamento do MSEE no caminho *Principal* para o contexto de roteamento *Privado,* use o seguinte comando:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Uma resposta de exemplo é:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Se o estado de um eBGP olhando entre um MSEE e um CE/PE-MSEE estiver em ativo ou ocioso, verifique se as sub-redes de pares primários e secundários atribuídos correspondem à configuração no CE/PE-MSEE vinculado. Verifique também se o *VlanId*correto, *OZureAsn*e *o PeerAsn* são usados em MSEEs e se esses valores são mapeados para os utilizados no PE-MSEE/CE vinculado. Se o hashing MD5 for escolhido, a chave compartilhada deve ser a mesma no par MSEE e CE/PE-MSEE. Se você precisar alterar qualquer uma dessas configurações em um roteador MSEE, consulte [Criar e modificar o roteamento para um circuito ExpressRoute][CreatePeering].
>


> [!NOTE]
> Se determinados destinos não forem acessíveis sobre um peering, verifique a tabela de rotas dos MSEEs para obter o contexto correspondente de peering. Se um prefixo correspondente (poderia ser IP NATed) estiver presente na tabela de roteamento, verifique se há firewalls/NSG/ACLs no caminho que estão bloqueando o tráfego.
>


O exemplo a seguir mostra a resposta do comando para um peering que não existe:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Confirme o fluxo de tráfego
Para obter as estatísticas de tráfego combinadas dos caminhos primário e secundário – bytes em entrada e em saída – de um contexto de emparelhamento, use o seguinte comando:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

Uma saída de exemplo do comando é:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Uma saída de exemplo do comando para um emparelhamento inexistente é:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações ou ajuda, confira os seguintes links:

- [Suporte da Microsoft][Support]
- [Criar e modificar um circuito do ExpressRoute][CreateCircuit]
- [Criar e modificar o roteamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Conectividade lógica do ExpressRoute"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ícone Todos os Recursos"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ícone Visão Geral"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Captura de tela de exemplo do ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Captura de tela de exemplo do ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





