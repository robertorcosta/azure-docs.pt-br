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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008312"
---
## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Implantar discos compartilhados

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Implantar um SSD Premium como um disco compartilhado

Para implantar um disco gerenciado com o recurso de disco compartilhado habilitado, use a nova `maxShares` Propriedade e defina um valor maior que 1. Isso torna o disco compartilhável entre várias VMs.

> [!IMPORTANT]
> O valor de `maxShares` só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de disco](#disk-sizes) para os valores permitidos `maxShares`para.

Antes de usar o modelo a seguir `[parameters('dataDiskName')]`, `[resourceGroup().location]`substitua `[parameters('dataDiskSizeGB')]`,, `[parameters('maxShares')]` e pelos seus próprios valores.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Implantar um ultra Disk como um disco compartilhado

#### <a name="cli"></a>CLI

Para implantar um disco gerenciado com o recurso de disco compartilhado habilitado, altere `maxShares` o parâmetro para um valor maior que 1. Isso torna o disco compartilhável entre várias VMs.

> [!IMPORTANT]
> O valor de `maxShares` só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de disco](#disk-sizes) para os valores permitidos `maxShares`para.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Para implantar um disco gerenciado com o recurso de disco compartilhado habilitado, use a `maxShares` Propriedade e defina um valor maior que 1. Isso torna o disco compartilhável entre várias VMs.

> [!IMPORTANT]
> O valor de `maxShares` só pode ser definido ou alterado quando um disco é desmontado de todas as VMs. Consulte os [tamanhos de disco](#disk-sizes) para os valores permitidos `maxShares`para.

Antes de usar o modelo a seguir `[parameters('dataDiskName')]`, `[resourceGroup().location]`substitua `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]`,,,,, `[parameters('diskMBpsReadOnly')]` e pelos seus próprios valores.

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

Depois de implantar um disco compartilhado com `maxShares>1`o, você pode montar o disco em uma ou mais de suas VMs.

> [!IMPORTANT]
> Todas as VMs que compartilham um disco devem ser implantadas no mesmo [grupo de posicionamento de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).

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

Se você estiver interessado em tentar discos compartilhados, [Inscreva-se para nossa versão prévia](https://aka.ms/AzureSharedDiskPreviewSignUp).