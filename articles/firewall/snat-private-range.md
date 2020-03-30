---
title: Azure Firewall SNAT faixas de endereços IP privados
description: Você pode configurar faixas privadas de endereço IP para que o firewall não seja tráfego SNAT para esses endereços IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064806"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall SNAT faixas de endereços IP privados

O Azure Firewall não snat com regras de rede quando o endereço IP de destino está em uma faixa de endereço IP privada por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras do aplicativo são sempre aplicadas usando um [proxy transparente,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) independentemente do endereço IP de destino.

Se sua organização usar um intervalo de endereçoIP público para redes privadas, o Azure Firewall SNATs será o tráfego para um dos endereços IP privados de firewall no AzureFirewallSubnet. No entanto, você pode configurar o Firewall Azure para **não** SNAT sua faixa pública de endereçoIP.

## <a name="configure-snat-private-ip-address-ranges"></a>Configurar faixas de endereço IP privado SNAT

Você pode usar o Azure PowerShell para especificar um intervalo de endereçoIP que o firewall não snat.

### <a name="new-firewall"></a>Novo firewall

Para um novo firewall, o comando Azure PowerShell é:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> O IANAPrivateRanges é expandido para os padrões atuais no Azure Firewall, enquanto as outras faixas são adicionadas a ele.

Para obter mais informações, consulte [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall existente

Para configurar um firewall existente, use os seguintes comandos do Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelos

Você pode adicionar o `additionalProperties` seguinte à seção:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).