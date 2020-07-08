---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "73495765"
---
Você pode usar o `Resize-AzVirtualNetworkGateway` cmdlet do PowerShell para atualizar ou fazer downgrade de um SKU Generation1 ou Generation2 (todas as SKUs VpnGw podem ser redimensionadas, exceto SKUs básicos). Se você estiver usando a SKU de gateway básica [use estas instruções](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) para redimensionar o gateway.

O exemplo de PowerShell a seguir mostra um SKU de gateway sendo redimensionado para VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

Você também pode redimensionar um gateway no portal do Azure, vá para o **Configuração** página do seu gateway de rede virtual e selecione uma SKU diferente na lista suspensa.
