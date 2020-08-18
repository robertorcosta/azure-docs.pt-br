---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 37d9cf0b0ac7145e389f9fc768ff0b593b27ae1a
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515184"
---
## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

Os discos compartilhados dão suporte a vários sistemas operacionais. Consulte as seções do [Windows](../articles/virtual-machines/windows/disks-shared.md#windows) e do [Linux](../articles/virtual-machines/linux/disks-shared.md#linux) do artigo conceitual para os sistemas operacionais com suporte.

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Implantar discos compartilhados

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Implantar um SSD Premium como um disco compartilhado

Para implantar um disco gerenciado com o recurso de disco compartilhado habilitado, use a nova propriedade `maxShares` e defina um valor maior que 1. Isso torna o disco compartilhável entre várias VMs.

> [!IMPORTANT]
> O valor de `maxShares` só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de disco](#disk-sizes) para os valores permitidos para `maxShares` .

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Modelo do Resource Manager](#tab/azure-resource-manager)

Antes de usar o modelo a seguir, substitua `[parameters('dataDiskName')]` ,, `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` e `[parameters('maxShares')]` pelos seus próprios valores.

[SSD Premium modelo de disco compartilhado](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Implantar um ultra Disk como um disco compartilhado

Para implantar um disco gerenciado com o recurso de disco compartilhado habilitado, altere o `maxShares` parâmetro para um valor maior que 1. Isso torna o disco compartilhável entre várias VMs.

> [!IMPORTANT]
> O valor de `maxShares` só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de disco](#disk-sizes) para os valores permitidos para `maxShares` .


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Exemplo de disco regional

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Exemplo de disco zonal

Este exemplo é quase igual ao anterior, exceto que ele cria um disco na zona de disponibilidade 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Exemplo de disco regional

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Exemplo de disco zonal

Este exemplo é quase igual ao anterior, exceto que ele cria um disco na zona de disponibilidade 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Modelo do Resource Manager](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Exemplo de disco regional

Antes de usar o modelo a seguir, substitua,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` e `[parameters('diskMBpsReadOnly')]` pelos seus próprios valores.

[Modelo de ultra discos compartilhados regionais](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Exemplo de disco zonal

Antes de usar o modelo a seguir, substitua,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` e `[parameters('diskMBpsReadOnly')]` pelos seus próprios valores.

[Modelo de ultra discos compartilhados de zona](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Usando discos compartilhados do Azure com suas VMs

Depois de implantar um disco compartilhado com `maxShares>1` o, você pode montar o disco em uma ou mais de suas VMs.

> [!NOTE]
> Se você estiver implantando um ultra Disk, verifique se ele corresponde aos requisitos necessários. Consulte a seção [PowerShell](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm-1) ou [CLI](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md#enable-ultra-disk-compatibility-on-an-existing-vm) do artigo ultra Disk para obter detalhes.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Comandos de RP do SCSI com suporte

Depois de montar o disco compartilhado em suas VMs em seu cluster, você pode estabelecer o quorum e ler/gravar no disco usando o SCSI PR. Os seguintes comandos de PR estão disponíveis ao usar os discos compartilhados do Azure:

Para interagir com o disco, comece com a lista de ações de reserva persistente:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Ao usar PR_RESERVE, PR_PREEMPT_RESERVATION ou PR_RELEASE_RESERVATION, forneça um dos seguintes tipos de reserva persistente:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Você também precisa fornecer uma chave de reserva persistente ao usar PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION ou reserva de PR_RELEASE.


## <a name="next-steps"></a>Próximas etapas

Se você preferir usar modelos de Azure Resource Manager para implantar o disco, os seguintes modelos de exemplo estarão disponíveis:
- [SSD Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Ultra discos regionais](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Ultra discos de zona](https://aka.ms/SharedUltraDiskARMtemplateZonal)