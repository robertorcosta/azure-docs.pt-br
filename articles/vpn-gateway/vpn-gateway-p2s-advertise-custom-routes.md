---
title: 'Azure VPN Gateway: anuncie rotas personalizadas para clientes P2S VPN'
description: Etapas para anunciar rotas personalizadas para seus clientes ponto a ponto
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151900"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anuncie rotas personalizadas para clientes P2S VPN

Você pode querer anunciar rotas personalizadas para todos os seus clientes VPN point-to-site. Por exemplo, quando você tiver ativado pontos finais de armazenamento em seu VNet e quiser que os usuários remotos possam acessar essas contas de armazenamento através da conexão VPN. Você pode anunciar o endereço IP do ponto final do armazenamento para todos os seus usuários remotos para que o tráfego para a conta de armazenamento ultrapasse o túnel VPN, e não a Internet pública.

![Exemplo de conexão Multissite do Gateway de VPN do Azure](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Para anunciar rotas personalizadas

Para anunciar rotas personalizadas, use o `Set-AzVirtualNetworkGateway cmdlet`. O exemplo a seguir mostra como anunciar o IP para as tabelas de [conta de armazenamento contoso](https://contoso.table.core.windows.net).

1. Ping *contoso.table.core.windows.net* e anote o endereço IP. Por exemplo: 

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Execute os seguintes comandos do PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Para adicionar várias rotas personalizadas, use um coma e espaços para separar os endereços. Por exemplo: 

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Para visualizar rotas personalizadas

Use o seguinte exemplo para exibir rotas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Para excluir rotas personalizadas

Use o seguinte exemplo para excluir rotas personalizadas:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais de roteamento P2S, consulte [Sobre o roteamento ponto a local](vpn-gateway-about-point-to-site-routing.md).
