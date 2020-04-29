---
title: Intervalos de endereços IP privados de SNAT do firewall do Azure
description: Você pode configurar intervalos privados de endereço IP para que o firewall não SNAT o tráfego para esses endereços IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064806"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Intervalos de endereços IP privados de SNAT do firewall do Azure

O Firewall do Azure não SNAT com regras de rede quando o endereço IP de destino está em um intervalo de endereços IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras de aplicativo sempre são aplicadas usando um [proxy transparente](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) , independentemente do endereço IP de destino.

Se sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure SNATs o tráfego para um dos endereços IP privados do firewall em AzureFirewallSubnet. No entanto, você pode configurar o Firewall do Azure para **não** SNAT de seu intervalo de endereços IP públicos.

## <a name="configure-snat-private-ip-address-ranges"></a>Configurar intervalos de endereços IP privados SNAT

Você pode usar Azure PowerShell para especificar um intervalo de endereços IP que o firewall não irá SNAT.

### <a name="new-firewall"></a>Novo firewall

Para um novo firewall, o comando Azure PowerShell é:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> O IANAPrivateRanges é expandido para os padrões atuais no firewall do Azure, enquanto os outros intervalos são adicionados a ele.

Para obter mais informações, consulte [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Firewall existente

Para configurar um firewall existente, use os seguintes comandos de Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Modelos

Você pode adicionar o seguinte à `additionalProperties` seção:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).