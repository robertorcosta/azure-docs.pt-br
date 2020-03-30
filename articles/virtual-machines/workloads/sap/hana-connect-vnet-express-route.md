---
title: Configuração de conectividade da rede virtual ao SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Configuração de conectividade a partir de rede virtual para usar SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7ac8e69c4e149fdd0f365e19f7a0282a547af43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617196"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Conecte uma rede virtual a instâncias grandes do HANA

Depois de criar uma rede virtual do Azure, você pode conectar essa rede ao SAP HANA em instâncias grandes do Azure. Crie um gateway do Azure ExpressRoute na rede virtual. Este gateway permite que você vincule a rede virtual ao circuito ExpressRoute que se conecta ao inquilino do cliente no selo HANA Large Instance.

> [!NOTE] 
> Essa etapa pode levar até 30 minutos para ser concluída. O novo gateway é criado na assinatura do Azure designada e, em seguida, conectado à rede virtual do Azure especificada.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Se um gateway já existir, verifique se é um gateway do ExpressRoute ou não. Se não for um gateway ExpressRoute, exclua o gateway e recrie-o como um gateway ExpressRoute. Se um gateway da Rota Expressa já estiver estabelecido, consulte a seção a seguir deste artigo, "Vincular redes virtuais". 

- Use o [Portal do Azure](https://portal.azure.com/) ou o PowerShell para criar um gateway de VPN do ExpressRoute conectado à sua rede virtual.
  - Se você usar o portal do Azure, adicione um novo **Gateway de Rede Virtual** e, em seguida, selecione **ExpressRoute** como o tipo de gateway.
  - Se você usar o PowerShell, primeiro Baixe e use a versão mais recente [SDK do Azure PowerShell](https://azure.microsoft.com/downloads/). 
 
Os comandos a seguir criam um gateway de ExpressRoute. Os textos precedidos por um _$_ são variáveis definidas pelo usuário que devem ser atualizadas com suas informações específicas.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Neste exemplo, a SKU de gateway HighPerformance foi usada. Suas opções são HighPerformance ou UltraPerformance como as únicas SKUs de gateway suportadas para o SAP HANA no Azure (grandes instâncias).

> [!IMPORTANT]
> Para instâncias grandes do HANA do SKU de classe Tipo II, você deve usar o SKU do Gateway UltraPerformance.

## <a name="link-virtual-networks"></a>Vincular redes virtuais

A rede virtual do Azure agora tem um gateway da Rota Expressa. Use as informações de autorização fornecidas pela Microsoft para conectar o gateway ExpressRoute ao circuito SAP HANA Large Instances ExpressRoute. Você pode se conectar usando o portal do Azure ou o PowerShell. As instruções do PowerShell são as seguintes. 

Execute os seguintes comandos para cada gateway ExpressRoute usando um AuthGUID diferente para cada conexão. As duas primeiras entradas mostradas no script a seguir são provenientes das informações fornecidas pela Microsoft. Além disso, o AuthGUID é específico para cada rede virtual e seu gateway. Se você quiser adicionar outra rede virtual Azure, você precisa obter outro AuthID para o seu circuito ExpressRoute que conecta grandes instâncias HANA ao Azure da Microsoft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> O último parâmetro no comando New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** é um novo parâmetro que permite o Caminho Rápido expressRoute. Uma funcionalidade que reduz a latência da rede entre suas unidades HANA Large Instance e VMs Azure. A funcionalidade foi adicionada em maio de 2019. Para obter mais detalhes, verifique a arquitetura de [rede SAP HANA (Instâncias Grandes) do artigo .](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Certifique-se de que está executando a versão mais recente dos cmdlets PowerShell antes de executar os comandos.

Para conectar o gateway a mais de um circuito do ExpressRoute associado à sua assinatura, talvez seja necessário executar essa etapa mais de uma vez. Por exemplo, você provavelmente conectará o mesmo gateway de rede virtual ao circuito da Rota Expressa que conecta a rede virtual à sua rede local.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Aplicando o Caminho Rápido ExpressRoute aos circuitos expressos de grande instância existentes do HANA
A documentação até agora explicou como conectar um novo circuito ExpressRoute que foi criado com uma implantação hana grande instância a um gateway Azure ExpressRoute de uma de suas redes virtuais Azure. Mas muitos clientes já têm a configuração de seus circuitos ExpressRoute e já têm suas redes virtuais conectadas ao HANA Large Instances. Como o novo Caminho Rápido ExpressRoute está reduzindo a latência da rede, recomenda-se que você aplique a alteração para usar essa funcionalidade. Os comandos para conectar um novo circuito ExpreesRoute e alterar um Circuito ExpressRoute existente são os mesmos. Como resultado, você precisa executar esta seqüência de comandos PowerShell para alterar um circuito existente para usar 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

É importante adicionar o último parâmetro como exibido acima para ativar a funcionalidade do Caminho Rápido ExpressRoute


## <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
Como você deseja habilitar o Global Reach para um ou ambos os dois cenários:

 - Replicação do sistema HANA sem proxies ou firewalls adicionais
 - Copiando backups entre unidades HANA Large Instance em duas regiões diferentes para executar cópias do sistema ou atualizações do sistema

você precisa considerar isso:

- Você precisa fornecer um intervalo de espaço de endereço de um espaço de endereço /29. Esse intervalo de endereços pode não se sobrepor a nenhuma das outras faixas de espaço de endereço que você usou até agora conectando HANA Large Instances ao Azure e pode não se sobrepor a nenhuma das faixas de endereçoIP que você usou em outro lugar no Azure ou no local.
- Há uma limitação nas ASNs (Autonomous System Number) que pode ser usada para anunciar suas rotas no local para HANA Large Instances. Seus locais não devem anunciar rotas com ASNs privadas na faixa de 65000 – 65020 ou 65515. 
- Para o cenário de conexão no local de acesso direto a HANA Grandes instâncias, você precisa calcular uma taxa para o circuito que o conecta ao Azure. Para preços, verifique os preços do [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/).

Para obter um ou ambos os cenários aplicados à sua implantação, abra uma mensagem de suporte com o Azure conforme descrito no [Open uma solicitação de suporte para grandes instâncias hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

Dados necessários e palavras-chave que você precisa usar para a Microsoft para poder rodar e executar a seu pedido, parece:

- Serviço: SAP HANA Grande Instância
- Tipo de problema: Configuração e configuração
- Subtipo do problema: Meu problema não está listado acima
- Assunto 'Modificar minha rede - adicionar alcance global'
- Detalhes: 'Adicionar alcance global ao HANA Large Instance ao inquilino hana large instance ou 'Adicionar alcance global ao inquilino hana grande instância.
- Detalhes adicionais para o caso de inquilino HANA Large Instance para HANA Large Instance: Você precisa definir as **duas regiões do Azure** onde os dois inquilinos para se conectar estão localizados **e** você precisa enviar a **faixa de endereço IP /29**
- Detalhes adicionais para o caso de inquilino hana grande instância: Você precisa definir a **região azure** onde o inquilino HANA Large Instance é implantado você deseja se conectar diretamente. Além disso, você precisa fornecer o **Auth GUID** e **o Circuit Peer ID** que você recebeu quando estabeleceu seu circuito ExpressRoute entre o local e o Azure. Além disso, você precisa nomear seu **ASN**. O último a ser entregue é uma **faixa de endereço IP /29** para ExpressRoute Global Reach.

> [!NOTE]
> Se você quiser ter ambos os casos tratados, você precisa fornecer duas faixas de endereçoIP diferentes /29 que não se sobrepõem a qualquer outro intervalo de endereçoIP usado até agora. 




## <a name="next-steps"></a>Próximas etapas

- [Requisitos de rede adicionais para HLI](hana-additional-network-requirements.md)
