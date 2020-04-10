---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008312"
---
## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Implantar discos compartilhados

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Implantar um SSD premium como um disco compartilhado

Para implantar um disco gerenciado com o recurso de disco `maxShares` compartilhado ativado, use a nova propriedade e defina um valor maior que 1. Isso torna o disco compartilhável em várias VMs.

> [!IMPORTANT]
> O valor `maxShares` de só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de](#disk-sizes) disco `maxShares`para os valores permitidos para .

Antes de usar o `[parameters('dataDiskName')]` `[resourceGroup().location]`modelo `[parameters('dataDiskSizeGB')]`a `[parameters('maxShares')]` seguir, substitua , e com seus próprios valores.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Implantar um disco ultra como um disco compartilhado

#### <a name="cli"></a>CLI

Para implantar um disco gerenciado com o recurso de `maxShares` disco compartilhado ativado, altere o parâmetro para um valor maior que 1. Isso torna o disco compartilhável em várias VMs.

> [!IMPORTANT]
> O valor `maxShares` de só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de](#disk-sizes) disco `maxShares`para os valores permitidos para .

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Para implantar um disco gerenciado com o recurso de `maxShares` disco compartilhado ativado, use a propriedade e defina um valor maior que 1. Isso torna o disco compartilhável em várias VMs.

> [!IMPORTANT]
> O valor `maxShares` de só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de](#disk-sizes) disco `maxShares`para os valores permitidos para .

Antes de usar o `[parameters('dataDiskName')]` `[resourceGroup().location]`seguinte `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]`modelo, `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]`substitua `[parameters('diskMBpsReadOnly')]` , , , `[parameters('diskIOPSReadWrite')]`, , , , , e com seus próprios valores.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Usando discos compartilhados do Azure com suas VMs

Depois de implantar um disco `maxShares>1`compartilhado com, você pode montar o disco em uma ou mais de suas VMs.

> [!IMPORTANT]
> Todas as VMs que compartilham um disco devem ser implantadas no mesmo [grupo de colocação de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Comandos SCSI PR suportados

Depois de montar o disco compartilhado em suas VMs no cluster, você pode estabelecer quórum e ler/gravar no disco usando o SCSI PR. Os seguintes comandos de RP estão disponíveis ao usar discos compartilhados do Azure:

Para interagir com o disco, comece com a lista de ação de reserva persistente:

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

Você também precisa fornecer uma chave de reserva persistente ao usar PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION ou PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Próximas etapas

Se você estiver interessado em experimentar discos compartilhados, [inscreva-se na nossa pré-visualização](https://aka.ms/AzureSharedDiskPreviewSignUp).