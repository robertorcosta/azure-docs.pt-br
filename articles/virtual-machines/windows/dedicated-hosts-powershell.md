---
title: Implantar hosts dedicados ao Azure usando o Azure PowerShell
description: Implante VMs para hosts dedicados usando o Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082842"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Implantar VMs para hosts dedicados usando o Azure PowerShell

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) ao Azure para hospedar suas máquinas virtuais (VMs). 

Certifique-se de que você instalou a versão 2.8.0 do Azure PowerShell ou `Connect-AzAccount`posterior, e você está conectado a uma conta do Azure com . 

## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em hosts dedicados.
- Os tamanhos e tipos de hardware disponíveis para hosts dedicados variam de acordo com a região. Consulte a [página de preços](https://aka.ms/ADHPricing) do host para saber mais.

## <a name="create-a-host-group"></a>Criar um grupo de hosts

Um **grupo anfitrião** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de host em uma região e uma região de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com seus hosts dedicados: 
- Span em várias zonas de disponibilidade. Neste caso, você é obrigado a ter um grupo de hospedagem em cada uma das zonas que deseja usar.
- Spane vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínio de falha para o seu grupo de host. Se você não quiser cobrir domínios de falha em seu grupo, use uma contagem de domínios de falha de 1. 

Você também pode decidir usar zonas de disponibilidade e domínios de falhas. Este exemplo cria um grupo de host na zona 1, com 2 domínios de falha. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Criar um host

Agora vamos criar um host dedicado no grupo anfitrião. Além de um nome para o host, você é obrigado a fornecer o SKU para o host. O Host SKU captura a série VM suportada, bem como a geração de hardware para o seu host dedicado.

Para obter mais informações sobre as SKUs host e preços, consulte [os preços do Host dedicado do Azure](https://aka.ms/ADHPricing).

Se você definir uma contagem de domínio de falha para o seu grupo de host, será solicitado que você especifique o domínio de falha para o host. Neste exemplo, definimos o domínio de falha para o host como 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma máquina virtual no host dedicado. 

Se você especificou uma zona de disponibilidade ao criar seu grupo de host, você será obrigado a usar a mesma região ao criar a máquina virtual. Para este exemplo, como nosso grupo de host está na zona 1, precisamos criar a VM na zona 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Se você criar uma máquina virtual em um host que não tenha recursos suficientes, a máquina virtual será criada em um estado FALHO. 

## <a name="check-the-status-of-the-host"></a>Verifique o status do host

Você pode verificar o estado de saúde do host e quantas máquinas virtuais `-InstanceView` você ainda pode implantar no host usando [getAzHost](/powershell/module/az.compute/get-azhost) com o parâmetro.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

A saída parecerá com o seguinte:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>Adicione uma VM existente 

Você pode adicionar uma VM existente a um host dedicado, mas a VM deve primeiro ser Stop\Deallocated. Antes de mover uma VM para um host dedicado, certifique-se de que a configuração da VM seja suportada:

- O tamanho da VM deve estar na mesma família do tamanho do host dedicado. Por exemplo, se o seu host dedicado for DSv3, então o tamanho da VM pode ser Standard_D4s_v3, mas não poderia ser um Standard_A4_v2. 
- A VM precisa estar localizada na mesma região do host dedicado.
- A VM não pode fazer parte de um grupo de colocação de proximidade. Remova a VM do grupo de colocação de proximidade antes de movê-la para um host dedicado. Para obter mais informações, consulte [Mover uma VM de um grupo de colocação de proximidade](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- A VM não pode estar em um conjunto de disponibilidade.
- Se a VM estiver em uma zona de disponibilidade, ela deve ser a mesma zona de disponibilidade do grupo de host. As configurações de zona de disponibilidade para a VM e o grupo de host devem coincidir.

Substitua os valores das variáveis por suas próprias informações.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Limpar

Você está sendo cobrado por seus hosts dedicados mesmo quando nenhuma máquina virtual é implantada. Você deve excluir todos os hosts que você não está usando no momento para economizar custos.  

Você só pode excluir um host quando não houver mais máquinas virtuais usando-o. Exclua as VMs usando [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Depois de excluir as VMs, você pode excluir o host usando [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Depois de excluir todos os hosts, você pode excluir o grupo host usando [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Você também pode excluir todo o grupo de recursos em um único comando usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Isso excluirá todos os recursos criados no grupo, incluindo todas as VMs, hosts e grupos de host.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Próximas etapas

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas quanto domínios de falha para máxima resiliência em uma região.

- Você também pode implantar hosts dedicados usando o [portal Azure](dedicated-hosts-portal.md).
