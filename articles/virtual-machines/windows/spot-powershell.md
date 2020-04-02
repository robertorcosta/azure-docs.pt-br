---
title: Use o PowerShell para implantar VMs azure Spot
description: Aprenda a usar o Azure PowerShell para implantar VMs Spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 234cf3f51173c53ef8ca15af4ca6f24881be3109
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547277"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Implantar VMs spot usando o Azure PowerShell


O uso [de VMs Spot](spot-vms.md) permite que você aproveite nossa capacidade não utilizada com uma economia significativa de custos. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará VMs spot. Portanto, as VMs spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lote, ambientes de dev/teste, grandes cargas de trabalho de computação e muito mais.

Os preços das VMs spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços da VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [Spot VMs - Pricing](spot-vms.md#pricing).

Você tem opção de definir um preço máximo que você está disposto a pagar, por hora, para a VM. O preço máximo de um VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, `0.98765`o valor seria um preço máximo de US$ 0,98765 por hora. Se você definir o `-1`preço máximo para ser, a VM não será despejada com base no preço. O preço da VM será o preço atual para spot ou o preço de uma VM padrão, que seja menor, desde que haja capacidade e cota disponíveis.


## <a name="create-the-vm"></a>Criar a VM

Crie um spotVM usando [o New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) para criar a configuração. Incluir `-Priority Spot` e `-MaxPrice` definir para:
- `-1`para que a VM não seja despejada com base no preço.
- um valor de dólar, até 5 dígitos. Por exemplo, `-MaxPrice .98765` significa que a VM será desalocada uma vez que o preço de um spotVM vai cerca de US $ 0,98765 por hora.


Este exemplo cria um spotVM que não será desalocado com base em preços (apenas quando o Azure precisa da capacidade de volta).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Depois que a VM é criada, você pode consultar para ver o preço máximo de todas as VMs no grupo de recursos.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Próximas etapas

Você também pode criar uma VM spot usando o [Azure CLI](../linux/spot-cli.md) ou um [modelo](../linux/spot-template.md).

Se você encontrar um erro, consulte [Códigos de erro](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).