---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299441"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Regiões com suporte
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Você pode usar o Azure PowerShell para criar um instantâneo incremental. Você precisará da versão mais recente do Azure PowerShell, o seguinte comando irá instalá-lo ou atualizar sua instalação existente para o mais recente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Uma vez instalado, faça login na `az login`sessão powershell com .

Para criar um instantâneo incremental com o Azure PowerShell, defina a `-Incremental` configuração com [o New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) com o parâmetro e passe-o como uma variável para [O Novo-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) através do `-Snapshot` parâmetro.

Substituir `<yourDiskNameHere>` `<yourResourceGroupNameHere>`, `<yourDesiredSnapShotNameHere>` e com seus valores, então você pode usar o seguinte script para criar um instantâneo incremental:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Você pode identificar instantâneos incrementais `SourceResourceId` do `SourceUniqueId` mesmo disco com as propriedades dos snapshots. `SourceResourceId`é o ID de recurso do Azure Resource Manager do disco pai. `SourceUniqueId`é o valor herdado da `UniqueId` propriedade do disco. Se você quiser excluir um disco e criar um novo disco `UniqueId` com o mesmo nome, o valor da propriedade será alterado.

Você pode `SourceResourceId` `SourceUniqueId` usar e criar uma lista de todos os instantâneos associados a um determinado disco. Substitua pelo `<yourResourceGroupNameHere>` seu valor e, em seguida, você pode usar o seguinte exemplo para listar seus instantâneos incrementais existentes:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode usar os modelos do Azure Resource Manager para criar um instantâneo incremental. Você precisará ter certeza de que o apiVersion está definido para **2019-03-01** e que a propriedade incremental também está definida como verdadeira. O trecho a seguir é um exemplo de como criar um instantâneo incremental com modelos do Gerenciador de recursos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Se você quiser ver o código de amostra demonstrando a capacidade diferencial de instantâneos incrementais, usando o .NET, consulte backups do [Copy Azure Managed Disks para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
