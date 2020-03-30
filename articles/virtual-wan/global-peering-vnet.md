---
title: Configure o peering Global VNet para WAN Virtual do Azure | Microsoft Docs
description: Conecte um VNet em uma região diferente ao seu hub WAN virtual.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588220"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Configure o peering VNet global (VNet transversal) para WAN virtual

Você pode conectar um VNet em uma região diferente ao seu hub WAN virtual.

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios:

* O VNet inter-região (spoke) não está conectado a outro hub WAN virtual. Um spoke só pode ser conectado a um hub virtual.
* O VNet (spoke) não contém um gateway de rede virtual (por exemplo, um Gateway de REDE Azure VPN ou gateway de rede virtual ExpressRoute). Se o VNet contiver um gateway de rede virtual, você deve remover o gateway antes de conectar o VNet falado ao hub.

## <a name="register-this-feature"></a><a name="register"></a>Registrar este recurso

Você pode registrar este recurso usando o PowerShell. Se você selecionar "Experimentá-lo" no exemplo abaixo, o Azure Cloud-Shell será aberto e você não precisará instalar os cmdlets do PowerShell localmente no seu computador. Se necessário, você pode alterar as assinaturas usando o cmdlet 'Select-AzSubscription -SubscriptionId'. <subid>

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Verificar o registro

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Conecte um VNet ao hub

Nesta etapa, você cria a conexão de peering entre seu hub e o VNet inter-região. Repita as etapas para cada VNET que você deseja se conectar.

1. Na página da WAN virtual, clique em **Conexões de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão de emparelhamento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN virtual, consulte [Visão Geral de WAN Virtual](virtual-wan-about.md).