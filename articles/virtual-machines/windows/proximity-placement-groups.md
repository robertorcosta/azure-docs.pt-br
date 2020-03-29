---
title: 'PowerShell: Use grupos de colocação de proximidade'
description: Aprenda a criar e usar grupos de colocação de proximidade usando o Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208518"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Implantar VMs para grupos de colocação de proximidade usando o PowerShell


Para obter VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-los dentro de um [grupo de colocação de proximidade](co-location.md#proximity-placement-groups).

Um grupo de colocação de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados perto um do outro. Grupos de colocação de proximidade são úteis para cargas de trabalho onde a baixa latência é um requisito.


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


## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma VM no grupo `-ProximityPlacementGroup $ppg.Id` de colocação de proximidade usando para se referir ao ID do grupo de colocação de proximidade quando você usar [o New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar a VM.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Você pode ver a VM no grupo de colocação usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Mova uma VM existente para um grupo de colocação de proximidade

Você também pode adicionar uma VM existente a um grupo de colocação de proximidade. Você precisa parar\dealocar a VM primeiro, depois atualizar a VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Mova uma VM existente para fora de um grupo de colocação de proximidade

Para remover uma VM de um grupo de colocação de proximidade, você precisa parar\dealocar a VM primeiro, depois atualizar a VM e reiniciar.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Você também pode criar um conjunto de disponibilidade em seu grupo de colocação de proximidade. Use o `-ProximityPlacementGroup` mesmo parâmetro com o cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) para criar um conjunto de disponibilidade e todas as VMs criadas no conjunto de disponibilidade também serão criadas no mesmo grupo de colocação de proximidade.

Para adicionar ou remover um conjunto de disponibilidade existente para um grupo de colocação de proximidade, primeiro você precisa parar todas as VMs no conjunto de disponibilidade. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Mova um conjunto de disponibilidade existente em um grupo de colocação de proximidade

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Mover uma disponibilidade existente definida para fora de um grupo de colocação de proximidade

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Você também pode criar um conjunto de escalas em seu grupo de colocação de proximidade. Use o `-ProximityPlacementGroup` mesmo parâmetro com [o New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) para criar um conjunto de escalas e todas as instâncias serão criadas no mesmo grupo de colocação de proximidade.


Para adicionar ou remover uma escala existente definida para um grupo de colocação de proximidade, primeiro você precisa parar o conjunto de escalas. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Mova uma escala existente definida em um grupo de colocação de proximidade

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Mova uma escala existente definida para fora de um grupo de colocação de proximidade

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Você também pode usar o [Azure CLI](../linux/proximity-placement-groups.md) para criar grupos de colocação de proximidade.
