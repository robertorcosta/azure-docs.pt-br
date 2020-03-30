---
title: Reserve endereços iPv6 públicos e faixas de endereços em uma rede virtual Azure
titlesuffix: Azure Virtual Network
description: Aprenda a reservar endereços e endereços públicos IPv6 em uma rede virtual Do Zure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72595079"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Prefixo de endereço iPv6 público de reserva
O IPv6 for Azure Virtual Network (VNet) permite hospedar aplicativos no Azure com conectividade IPv6 e IPv4, tanto dentro de uma rede virtual quanto de e para a Internet. Além de reservar endereços IPv6 individuais, você pode reservar faixas contíguas de endereços Azure IPv6 (conhecidos como Prefixo IP) para seu uso. Esteartigo descreve como criar endereços IP públicos IPv6 e faixas de endereços usando o Azure PowerShell e o CLI.

> [!Important]
> IPv6 for Azure Virtual Network está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Crie um IP público IPv6 único reservado

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Você pode criar um único endereço IP Público IPv6 reservado (estático) usando o Azure PowerShell com [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) da seguinte forma:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

 Você pode criar um único endereço IP ip público (estático) reservado (estático) Azure CLI com [a rede az public-ip criar](/cli/azure/network/public-ip) da seguinte forma:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Crie um prefixo IPv6 reservado (intervalo)

Para reservar um prefixo IPv6, adicione a família de endereços IP do IPv6 ao mesmo comando usado para criar prefixos IPv4. Os comandos a seguir criam um prefixo de tamanho /125 (8 endereços IPv6).  

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Você pode criar um endereço IPv6 público usando o Azure CLI com [a rede az public-ip criar](/powershell/module/az.network/new-azpublicipprefix) da seguinte forma:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Você pode criar um endereço IPv6 público usando o Azure CLI da seguinte forma:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Aloque um endereço IP público a partir de um prefixo IPv6 reservado

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

 Você cria um IP público IPv6 estático `-PublicIpPrefix` a partir de um prefixo reservado adicionando o argumento ao criar o IP público usando o Azure PowerShell. O exemplo a seguir assume que um prefixo foi criado e armazenado em uma variável PowerShell chamada: *$myOwnIPv6Prefix*.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Usando a CLI do Azure
 
O exemplo a seguir assume que um prefixo foi criado e armazenado em uma variável CLI chamada: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o prefixo de endereço IPv6](ipv6-public-ip-address-prefix.md).
- Saiba mais sobre [endereços IPv6](ipv6-overview.md).
