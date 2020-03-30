---
title: 'Azure ExpressRoute: Configure Global Reach'
description: Este artigo ajuda você a vincular circuitos do ExpressRoute para criar uma rede privada entre suas redes locais e habilitar o Alcance Global.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083459"
---
# <a name="configure-expressroute-global-reach"></a>Configurar o Alcance Global do ExpressRoute

Este artigo ajuda você a configurar o Alcance Global do ExpressRoute usando o PowerShell. Para obter mais informações, confira [Alcance Global do ExpressRouteRoute](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Você entende os fluxos de trabalho de provisionamento de [circuitos](expressroute-workflows.md)ExpressRoute .
* Os circuitos ExpressRoute estão em um estado provisionado.
* O peering privado do Azure está configurado em seus circuitos ExpressRoute.
* Se você quiser executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada no seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identificar circuitos

1. Para iniciar a configuração, faça login na sua conta do Azure e selecione a assinatura que deseja usar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifique os circuitos ExpressRoute que você deseja usar. Você pode habilitar o ExpressRoute Global Reach entre qualquer dois circuitos ExpressRoute, desde que eles estejam localizados nos países/regiões suportadas e foram criados em diferentes locais de peering. 

   * Se sua assinatura tiver os dois circuitos, será possível escolher qualquer circuito para executar a configuração nas seções a seguir.
   * Se os dois circuitos estiverem em diferentes assinaturas do Azure, você precisará de autorização de uma assinatura do Azure. Em seguida, você passa a chave de autorização quando executa o comando de configuração em outra assinatura do Azure.

## <a name="enable-connectivity"></a>Habilitar a conectividade

Habilite a conectividade entre suas redes locais. Existem conjuntos separados de instruções para circuitos que estão na mesma assinatura do Azure, e circuitos que são assinaturas diferentes.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Circuitos ExpressRoute na mesma assinatura do Azure

1. Use os seguintes comandos para obter o circuito 1 e o circuito 2. Os dois circuitos estão na mesma assinatura.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Execute o comando a seguir no circuito 1 e passe a ID de emparelhamento privado do circuito 2. Ao executar o comando, observe o seguinte:

   * A ID de emparelhamento privado é semelhante ao exemplo a seguir: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-O AddressPrefix* deve ser uma sub-rede IPv4 de 29, por exemplo, "10.0.0.0/29". Usamos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Você não deve usar os endereços nessa sub-rede em suas redes virtuais do Azure nem em sua rede local.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Salve a configuração no circuito 1 da seguinte maneira:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando a operação anterior for concluída, você terá conectividade entre suas redes locais em ambos os lados através de seus dois circuitos ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, a autorização é gerada na assinatura do circuito 2 e a chave de autorização é passada para o circuito 1.

1. Gere uma chave de autorização.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Anote a ID de emparelhamento privada do circuito 2, bem como a chave de autorização.
2. Execute o seguinte comando no circuito 1. Passe a ID de emparelhamento privada do circuito 2 e a chave de autorização.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Salve a configuração no circuito 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Quando a operação anterior for concluída, você terá conectividade entre suas redes locais em ambos os lados através de seus dois circuitos ExpressRoute.

## <a name="verify-the-configuration"></a>Verificar a configuração

Use o seguinte comando para verificar a configuração no circuito em que a configuração foi realizada (por exemplo, circuito 1 no exemplo anterior).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se você simplesmente executar *$ckt1* no PowerShell, verá *CircuitConnectionStatus* na saída. Ele informa se a conectividade foi estabelecida, "Conectada" ou "Desconectada". 

## <a name="disable-connectivity"></a>Desabilitar a conectividade

Para desativar a conectividade entre suas redes locais, execute os comandos contra o circuito onde a configuração foi feita (por exemplo, circuito 1 no exemplo anterior).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Você pode executar a operação Get para verificar o status.

Depois de concluir a operação anterior, você deixará de ter conectividade entre suas redes locais por meio de seus circuitos do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
1. [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-global-reach.md)
2. [Verifique a conectividade ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
