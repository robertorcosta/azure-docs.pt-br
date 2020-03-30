---
title: Configurar e validar conexões de rede virtual ou VPN
description: Orientação passo a passo para configurar e validar várias implantações de VPN e rede virtual do Azure
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099070"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Configurar e validar conexões de rede virtual ou VPN

Este passo a passo fornece orientação passo a passo para configurar e validar várias implantações de VPN e rede virtual do Azure. Os cenários incluem roteamento de trânsito, conexões de rede para rede, BGP (Border Gateway Protocol), conexões multisite e conexões ponto a ponto.

Os gateways Azure VPN permitem flexibilidade na organização de quase qualquer tipo de topologia de rede virtual conectada no Azure. Por exemplo, você pode conectar redes virtuais:

- Através de regiões.
- Entre tipos de rede virtuais (Azure Resource Manager versus classic).
- Dentro do Azure ou dentro de um ambiente híbrido local.
- Em diferentes assinaturas. 

## <a name="network-to-network-vpn-connection"></a>Conexão VPN de rede para rede

Conectar uma rede virtual a outra rede virtual (rede-rede) via VPN é semelhante à conexão de uma rede virtual a um local no local. Ambos os tipos de conectividade usam um gateway VPN para fornecer um túnel seguro através do IPsec e do IKE. As redes virtuais podem estar na mesma região ou em regiões diferentes, e com a mesma assinatura ou em assinaturas diferentes.

![Conexão rede-rede com IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Se suas redes virtuais estiverem na mesma região, você pode querer considerar conectá-las usando peering de rede virtual. A peering de rede virtual não usa um gateway VPN. Aumenta a inadimplência e diminui a latência. Para configurar uma conexão de peering de rede virtual, **selecione Configurar e validar o VNet Peering**.

Se suas redes virtuais foram criadas através do modelo de implantação do Azure Resource Manger, **selecione Configurar e validar um VNet do Gerenciador de recursos para** configurar uma conexão VPN.

Se uma das redes virtuais foi criada através do modelo de implantação clássica do Azure e a outra foi criada através do Gerenciador de Recursos, **selecione Configurar e validar um VNet clássico para uma conexão VNet do Resource Manager** para configurar uma conexão VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Configure peering de rede virtual para duas redes virtuais na mesma região

Antes de começar a implementar e configurar o peering de rede virtual do Azure, certifique-se de atender aos seguintes pré-requisitos:

* As redes virtuais emparelhadas devem existir na mesma região do Azure.
* As redes virtuais peered devem ter espaços de endereço IP que não se sobreponham.
* O emparelhamento de rede virtual é entre duas redes virtuais. Não há nenhuma relação transitiva derivada entre os peerings. Por exemplo, se o VNetA for associado ao VNetB e o VNetB for associado ao VNetC, o VNetA *não* será associado ao VNetC.

Quando você atender aos requisitos, você pode seguir [tutorial: Conecte redes virtuais com peering de rede virtual usando o portal Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) para criar e configurar o peering.

Para verificar a configuração de peering, use o seguinte método:

1. Faça login no [portal Azure](https://portal.azure.com/) usando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. Na caixa que contém o texto **Pesquisar recursos** na parte superior do portal, digite **redes virtuais**. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
3. Na **lâmina de redes virtuais** que aparece, selecione a rede virtual para a qual você deseja criar um peering.
4. No painel que aparece para a rede virtual, **selecione Peerings** na seção **Configurações.**
5. Selecione um peering e visualize os resultados da configuração.

![Seleções para verificar a configuração de peering de rede virtual](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Para o Azure PowerShell, execute o comando [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) para obter o peering da rede virtual. Aqui está um exemplo:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Conecte uma rede virtual do Resource Manager a outra rede virtual do Resource Manager

Você pode configurar uma conexão de uma rede virtual do Resource Manager para outra rede virtual do Resource Manager diretamente. Ou você pode configurar a conexão usando IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Configure uma conexão VPN entre redes virtuais do Resource Manager

Para configurar uma conexão entre redes virtuais do Gerenciador de recursos sem IPsec, consulte [Configure uma conexão de gateway VPN de rede para rede usando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Para configurar uma conexão com o IPsec entre duas redes virtuais do Resource Manager, siga as etapas 1 a 5 em [Criar uma conexão site-a-site no portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) para cada rede virtual.

> [!Note]
> Essas etapas funcionam apenas para redes virtuais na mesma assinatura. Se suas redes virtuais estiverem em diferentes assinaturas, você deve usar o PowerShell para fazer a conexão. Consulte o artigo [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Valide a conexão VPN entre redes virtuais do Resource Manager

![Conexão clássica de rede virtual a uma rede virtual do Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Para verificar se sua conexão VPN está configurada corretamente, siga estas instruções.

> [!Note] 
> Os números após componentes de rede virtual nessas etapas correspondem aos números do diagrama anterior.

1. Certifique-se de que não há espaços de endereço sumidos nas redes virtuais conectadas.
2. Verifique se o intervalo de endereços da rede virtual do Azure Resource Manager (1) é definido com precisão na instância do **objeto Conexão** (4).
3. Verifique se o intervalo de endereços da rede virtual Do Azure Resource Manager (6) é definido com precisão na instância do **objeto Conexão** (3).
4. Verifique se as chaves pré-compartilhadas estão combinando nos objetos de conexão.
5. Verifique se o gateway de gateway de rede virtual do Azure Resource Manager VIP (2) é definido com precisão na instância do **objeto Conexão** (4).
6. Verifique se o gateway de gateway de rede virtual do Azure Resource Manager VIP (5) é definido com precisão na instância do **objeto Conexão** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Conecte uma rede virtual clássica a uma rede virtual do Resource Manager

Você pode criar uma conexão entre redes virtuais que estão em diferentes assinaturas e em diferentes regiões. Você também pode conectar redes virtuais que já possuem conexões com redes locais, desde que você tenha configurado o tipo de gateway como baseado em rota.

Para configurar uma conexão entre uma rede virtual clássica e uma rede virtual Resource Manager, consulte [Conectar redes virtuais de diferentes modelos de implantação usando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Conexão clássica de rede virtual a uma rede virtual do Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Para verificar a configuração quando conectar uma rede virtual clássica a uma rede virtual do Azure Resource Manager, siga estas instruções.

> [!Note] 
> Os números após componentes de rede virtual nessas etapas correspondem aos números do diagrama anterior. 

1. Certifique-se de que não há espaços de endereço sumidos nas redes virtuais conectadas.
2. Verifique se o intervalo de endereços para a rede virtual Do Azure Resource Manager (6) é definido com precisão na definição clássica da rede local (3).
3. Verifique se o intervalo de endereços da rede virtual clássica (1) é definido com precisão na instância do objeto Azure Resource Manager **Connection** (4).
4. Verifique se o VIP (2) do gateway de rede virtual clássico é definido com precisão na instância do objeto Azure Resource Manager **Connection** (4).
5. Verifique se o gateway de rede virtual do Azure Resource Manager (5) é definido com precisão na clássica instância **de definição de rede local** (3).
6. Verifique se as chaves pré-compartilhadas estão combinando em ambas as redes virtuais conectadas:
   - Rede virtual clássica: **Definição de Rede Local** (3)
   - Rede virtual do Azure Resource Manager: **objeto de conexão** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Crie uma conexão VPN ponto a ponto

Uma configuração*P2S* (point-to-site) no diagrama a seguir permite criar uma conexão segura de um computador cliente individual para uma rede virtual. Conexões ponto a ponto são úteis quando você deseja se conectar à sua rede virtual a partir de um local remoto, como de casa ou de uma conferência. Eles também são úteis quando você tem apenas alguns clientes que precisam se conectar a uma rede virtual. 

A conexão VPN ponto a ponto é iniciada a partir do computador cliente através do cliente nativo do Windows VPN. Os clientes de conexão utilizam certificados para autenticação.

![Conexão ponto a site](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

As conexões ponto a ponto não requerem um dispositivo VPN. Eles criam a conexão VPN através do Secure Socket Tunneling Protocol (SSTP). Você pode conectar uma conexão ponto a ponto a uma rede virtual usando várias ferramentas de implantação e modelos de implantação:

* [Configure uma conexão ponto a ponto para uma rede virtual usando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configure uma conexão ponto a ponto para uma rede virtual usando o portal Azure (clássico)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Configure uma conexão ponto a ponto a uma rede virtual usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Valide sua conexão ponto a ponto

O artigo [Solução de problemas: problemas de conexão ponto a ponto do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) atravessam problemas comuns com conexões ponto a ponto.

## <a name="create-a-multisite-vpn-connection"></a>Crie uma conexão VPN multisite

Você pode adicionar uma conexão*s2S* no diagrama a seguir a uma rede virtual que já tenha uma conexão site-a-site, conexão ponto a ponto ou conexão de rede para rede. Esse tipo de conexão é muitas vezes chamada de configuração *multisite.* 

![Conexão multisite](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

O Azure funciona atualmente com dois modelos de implantação: o Gerenciador de recursos e o clássico. Os dois modelos não são completamente compatíveis um com o outro. Para configurar uma conexão multisite com diferentes modelos, consulte os seguintes artigos:

* [Adicione uma conexão site-a-site a uma rede virtual com uma conexão de gateway VPN existente](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Adicione uma conexão site-a-site a uma rede virtual com uma conexão de gateway VPN (clássica) existente](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> As etapas nesses artigos não se aplicam às configurações de conexão coexistentes do Azure ExpressRoute e local-a-site. Para obter mais informações, consulte [ExpressRoute e conexões coexistentes site-to-site](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="configure-transit-routing"></a>Configurar roteamento de trânsito

O roteamento de trânsito é um cenário específico de roteamento onde você conecta várias redes em uma topologia de cadeia de margaridas. Esse roteamento permite que recursos em redes virtuais em ambos os lados da cadeia se comuniquem entre si através de redes virtuais no meio. Sem roteamento de trânsito, redes ou dispositivos espiados através de um hub não podem alcançar um ao outro.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Configure o roteamento de trânsito em uma conexão ponto a ponto

Imagine um cenário no qual você deseja configurar uma conexão VPN entre VNetA e VNetB. Você também deseja configurar uma VPN ponto a ponto para que o cliente se conecte ao gateway do VNetA. Em seguida, você deseja habilitar o roteamento de trânsito para que os clientes ponto a ponto se conectem ao VNetB, que passa pelo VNetA. 

Esse cenário é suportado quando o BGP é habilitado na VPN site-site entre vneta e vnetb. Para obter mais informações, consulte [Sobre o roteamento de VPN ponto a ponto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Configure o roteamento de trânsito em uma conexão ExpressRoute

O Azure ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365. Para obter mais informações, consulte [Visão geral de ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Conexão de peering privado ExpressRoute para redes virtuais Do Zure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Recomendamos que se o VNetA e o VNetB estiverem na mesma região geopolítica, você [vincula ambas as redes virtuais ao circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) em vez de configurar o roteamento de trânsito. Se suas redes virtuais estiverem em diferentes regiões geopolíticas, você também pode vinculá-las diretamente ao seu circuito se você tiver [O ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Se você tiver o ExpressRoute e a coexistência local-a-local, o roteamento de trânsito não é suportado. Para obter mais informações, consulte [Configurar ExpressRoute e site-a-site usando o PowerShell](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Se você habilitou o ExpressRoute para conectar suas redes locais a uma rede virtual Do Zure, você pode habilitar o peering entre as redes virtuais onde você deseja ter roteamento de trânsito. Para permitir que suas redes locais se conectem à rede virtual remota, você deve configurar [peering de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> O peering de rede virtual está disponível apenas para redes virtuais na mesma região.

Para verificar se você configurou o roteamento de trânsito para peering de rede virtual, siga estas instruções:

1. Faça login no [portal Azure](https://portal.azure.com/) usando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. [Crie um peering entre VNetA e VNetB](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) como mostrado no diagrama anterior. 
3. No painel que aparece para a rede virtual, **selecione Peerings** na seção **Configurações.**
4. Selecione o peering que deseja visualizar. Em seguida, selecione **Configuração** para validar que você habilitou **Permitir o Trânsito de Gateway** na rede VNetA conectada ao circuito ExpressRoute e use o Remote **Gateway** na rede VNetB remota não conectada ao circuito ExpressRoute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Configure o roteamento de trânsito em uma conexão de peering de rede virtual

Quando as redes virtuais estiverem emparelhadas, você também poderá configurar o gateway na rede virtual emparelhada como um ponto de trânsito para uma rede local. Para configurar uma rota de trânsito em peering de rede virtual, consulte [conexões de rede para rede](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> O gateway transit não é suportado na relação de peering entre redes virtuais criadas através de diferentes modelos de implantação. Ambas as redes virtuais na relação de peering devem ter sido criadas através do Resource Manager para o transporte de gateway para o trabalho.

Para verificar se você configurou uma rota de trânsito para peering de rede virtual, siga estas instruções:

1. Faça login no [portal Azure](https://portal.azure.com/) usando uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions)
2. Na caixa que contém o texto **Pesquisar recursos** na parte superior do portal, digite **redes virtuais**. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
3. Na **lâmina de redes virtuais** que aparece, selecione a rede virtual para a qual deseja verificar a configuração de peering.
4. No painel que aparece para a rede virtual selecionada, **selecione Peerings** na seção **Configurações.**
5. Selecione o peering que deseja visualizar. Valide se você habilitou **Permitir o trânsito de gateways** e usar **gateways remotos** em **Configuração**.

![Seleções para verificar se você configurou uma rota de trânsito para peering de rede virtual](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Configure o roteamento de trânsito em uma conexão rede-rede

Para configurar o roteamento de trânsito entre redes virtuais, você deve habilitar o BGP em todas as conexões intermediárias de rede para rede usando o modelo de implantação do Gerenciador de Recursos e o PowerShell. Para obter instruções, [consulte Como configurar o BGP nos gateways Azure VPN usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

O tráfego de trânsito através de gateways Azure VPN é possível através do modelo clássico de implantação, mas que conta com espaços de endereço sestáticamente definidos no arquivo de configuração da rede. O BGP ainda não é suportado com redes virtuais do Azure e gateways VPN através do modelo clássico de implantação. Sem o BGP, definir manualmente espaços de endereços de trânsito é propenso a erros, e não recomendamos isso.

> [!Note]
> Você configura conexões clássicas de rede para rede usando o portal clássico do Azure ou usando um arquivo de configuração de rede no portal clássico. Você não pode criar ou modificar uma rede virtual clássica através do modelo de implantação do Azure Resource Manager ou do portal Azure. Para obter mais informações sobre o roteamento de trânsito para redes virtuais clássicas, consulte o [blog Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Configure o roteamento de trânsito em uma conexão local-local

Para configurar o roteamento de trânsito entre sua rede local e uma rede virtual com uma conexão site-a-site, você deve habilitar o BGP em todas as conexões intermediárias de site para local usando o modelo de implantação do Gerenciador de Recursos e o PowerShell. Veja [como configurar o BGP nos gateways Azure VPN usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) para obter instruções.

O tráfego de trânsito através de gateways Azure VPN é possível através do modelo clássico de implantação, mas que conta com espaços de endereço sestáticamente definidos no arquivo de configuração da rede. O BGP ainda não é suportado com redes virtuais do Azure e gateways VPN através do modelo clássico de implantação. Sem o BGP, definir manualmente espaços de endereços de trânsito é propenso a erros, e não recomendamos isso.

> [!Note]
> Você configura conexões clássicas de site para local usando o portal clássico do Azure ou usando um arquivo de configuração de rede no portal clássico. Você não pode criar ou modificar uma rede virtual clássica através do modelo de implantação do Azure Resource Manager ou do portal Azure. Para obter mais informações sobre o roteamento de trânsito para redes virtuais clássicas, consulte o [blog Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Configurar o BGP para um gateway de VPN

BGP é o protocolo de roteamento padrão usado na internet para trocar informações de roteamento e alcance entre duas ou mais redes. Quando o BGP é usado no contexto das redes virtuais do Azure, ele habilita os gateways Azure VPN e seus dispositivos VPN locais, conhecidos como pares BGP ou vizinhos. Eles trocam "rotas" que informarão ambos os gateways sobre a disponibilidade e a capacidade de alcance para que esses prefixos passem pelos gateways ou roteadores envolvidos. 

O BGP também pode habilitar o roteamento de trânsito entre várias redes, propagando rotas que um gateway BGP aprende de um ponto BGP para todos os outros pares BGP. Para obter mais informações, consulte [Visão geral do BGP com o Gateway Azure VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configure o BGP para uma conexão VPN

Para configurar uma conexão VPN que usa BGP, consulte [Como configurar o BGP nos gateways azure VPN usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Habilite o BGP no gateway de rede virtual criando um número de sistema autônomo (AS) para ele. Os gateways básicos não suportam BGP. Para verificar o SKU do gateway, vá para a seção **Visão geral** da lâmina **VPN Gateway** no portal Azure. Se o seu SKU for **básico,** você terá que alterar o SKU (ver [Redimensionamento do gateway)](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)para **VpnGw1**. 

Verificar o SKU causará de 20 a 30 minutos de inatividade. Assim que o gateway tiver o SKU correto, você pode adicionar o número AS usando o comando [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell. Depois de configurar o número AS, um IP de peer BGP para o gateway será fornecido automaticamente.

Você deve fornecer `LocalNetworkGateway` manualmente um número AS e um endereço de ponto BGP. Você pode `ASN` definir `-BgpPeeringAddress` os valores usando o [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) ou o [comando Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell. Alguns números AS são reservados para o Azure, e você não pode usá-los como descrito em [About BGP com o Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

O objeto de conexão deve ter o BGP ativado. Você pode `-EnableBGP` definir `$True` o valor para através do [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) ou [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Validar a configuração BGP

Para verificar se o BGP está configurado `get-AzureRmVirtualNetworkGateway` `get-AzureRmLocalNetworkGateway` corretamente, você pode executar os comandos e. Em seguida, você notará a `BgpSettingsText` saída relacionada ao BGP na parte. Por exemplo: 

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Crie uma conexão VPN ativa/ativa altamente disponível

As principais diferenças entre os gateways ativos/ativos e ativos/em standby são:

* Você deve criar duas configurações ip de gateway com dois endereços IP públicos.
* Você deve definir o sinalizador **EnableActiveActiveFeature.**
* O gateway SKU deve ser **VpnGw1,** **VpnGw2**ou **VpnGw3**.

Para obter alta disponibilidade para instalações cruzadas e conectividade de rede para rede, você deve implantar vários gateways VPN e estabelecer várias conexões paralelas entre suas redes e o Azure. Para obter uma visão geral das opções de conectividade e topologia, consulte [locais interfronteiriços altamente disponíveis e conectividade de rede para rede.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)

Para criar conexões comerciais ativas/ativas e de rede para rede, siga as instruções em [Configurar conexões VPN Ativas/ativas com gateways Azure VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) para configurar um gateway Azure VPN no modo ativo/ativo.

> [!Note]  
> * Quando você adicionar endereços ao gateway de rede local para o modo ativo/ativo habilitado para BGP, *adicione apenas os endereços /32 dos pares BGP*. Se você adicionar mais endereços, eles serão considerados rotas estáticas e terão precedência sobre as rotas BGP.
> * Você deve usar diferentes números BGP AS para suas redes locais que estão se conectando ao Azure. (Se eles forem iguais, você terá que alterar o número AS da sua rede virtual se o seu dispositivo VPN no local já usar o ASN para espiar com outros vizinhos bgp.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Alterar um tipo de gateway Azure VPN após a implantação

Você não pode alterar um tipo de gateway de rede virtual do Azure de uma política baseada em diretivas para diretamente. Você deve primeiro excluir o gateway. Depois disso, o endereço IP e a chave pré-compartilhada não serão preservados. Então você pode criar um novo gateway do tipo desejado. 

Para excluir e criar um gateway, siga estas etapas:

1. Exclua quaisquer conexões associadas ao gateway original.
2. Exclua o gateway usando o portal Azure, PowerShell ou powershell clássico: 
   * [Exclua um gateway de rede virtual usando o portal Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Exclua um gateway de rede virtual usando o PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Exclua um gateway de rede virtual usando o PowerShell (clássico)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Siga as etapas em [Criar o gateway VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) para criar o novo gateway do tipo desejado e concluir a configuração vpn.

> [!Note]
> Esse processo levará cerca de 60 minutos.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conectividade entre VMs do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

