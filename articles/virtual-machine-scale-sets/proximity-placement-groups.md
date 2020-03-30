---
title: Visualização de grupos de colocação de proximidade para conjuntos de escala de máquinas virtuais
description: Aprenda a criar e usar grupos de colocação de proximidade para conjuntos de escala de máquinas virtuais do Windows no Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273612"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Visualização: Criando e usando grupos de colocação de proximidade usando o PowerShell

Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantar sua escala definida dentro de um [grupo de colocação de proximidade](co-location.md#preview-proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados perto um do outro. Grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.

> [!IMPORTANT]
> Os Grupos de Colocação de Proximidade estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Os grupos de colocação de proximidade não estão disponíveis nessas regiões durante a pré-visualização: **Japão Leste**, **Austrália Oriental** e Índia **Central**.


## <a name="create-a-proximity-placement-group"></a>Criar um grupo de posicionamento de proximidade
Crie um grupo de colocação de proximidade usando o cmdlet [New-AzProximityPlacementGroup.](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Grupos de colocação de proximidade de lista

Você pode listar todos os grupos de colocação de proximidade usando o [cmdlet Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Crie uma escala no grupo `-ProximityPlacementGroup $ppg.Id` de colocação de proximidade usando para se referir ao ID do grupo de colocação de proximidade quando você usar [o New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) para criar o conjunto de escalas.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Você pode ver a instância no grupo de colocação usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Próximas etapas

Você também pode usar o [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) para criar grupos de colocação de proximidade.
