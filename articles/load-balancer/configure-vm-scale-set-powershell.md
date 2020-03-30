---
title: Configure o conjunto de escala da máquina virtual com um Azure Load Balancer existente - Azure PowerShell
description: Aprenda a configurar um conjunto de escala de máquina virtual com um Balanceador de carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349985"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Configure um conjunto de escala de máquina virtual com um Azure Load Balancer existente usando o Azure PowerShell

Neste artigo, você aprenderá como configurar um conjunto de escala de máquina virtual com um Azure Load Balancer existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Um balanceador de carga sku padrão existente na assinatura onde o conjunto de escala da máquina virtual será implantado.
- Uma Rede Virtual Azure para o conjunto de escala de máquinavirtual.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure

Inscreva-se no Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implantar um conjunto de escala de máquina virtual com balanceador de carga existente

Substitua os valores entre parênteses com os nomes dos recursos em sua configuração.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

O exemplo abaixo implanta um conjunto de escala de máquina virtual com:

- Conjunto de escala de máquina virtual chamado **myVMSS**
- Azure Load Balancer chamado **myLoadBalancer**
- Pool de back-end do balanceador de carga chamado **myBackendPool**
- Rede Virtual Azure chamada **myVnet**
- Sub-rede chamada **mySubnet**
- Grupo de recursos chamado **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Depois que o conjunto de escalas for criado, a porta backend não pode ser modificada para uma regra de balanceamento de carga usada por uma sonda de saúde do balanceador de carga. Para alterar a porta, você pode remover o teste de saúde atualizando o conjunto de escala da máquina virtual do Azure, atualizar a porta e, em seguida, configurar o teste de saúde novamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um conjunto de escala de máquina virtual com um Balanceador de carga Azure existente.  Para saber mais sobre conjuntos de escala de máquinas virtuais e balanceador de carga, consulte:

- [O que é o Azure Load Balancer?](load-balancer-overview.md)
- [O que são conjuntos de escala de máquina virtual?](../virtual-machine-scale-sets/overview.md)
                                