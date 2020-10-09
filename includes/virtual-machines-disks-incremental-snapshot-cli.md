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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204569"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Você pode criar um instantâneo incremental com o CLI do Azure, será necessário ter a versão mais recente do CLI do Azure. 

No Windows, o comando a seguir instalará ou atualizará a instalação existente para a versão mais recente:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
No Linux, a instalação da CLI irá variar dependendo da versão do sistema operacional.  Consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para sua versão específica do Linux.

Para criar um instantâneo incremental, use [AZ snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) com o `--incremental` parâmetro.

O exemplo a seguir cria um instantâneo incremental, substitui `<yourDesiredSnapShotNameHere>` ,, `<yourResourceGroupNameHere>` `<exampleDiskName>` e `<exampleLocation>` com seus próprios valores e, em seguida, executa o exemplo:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Você pode identificar instantâneos incrementais do mesmo disco com o `SourceResourceId` e as `SourceUniqueId` Propriedades de instantâneos. `SourceResourceId` é a ID de recurso Azure Resource Manager do disco pai. `SourceUniqueId` é o valor herdado da `UniqueId` Propriedade do disco. Se você for excluir um disco e, em seguida, criar um novo disco com o mesmo nome, o valor da `UniqueId` propriedade será alterado.

Você pode usar `SourceResourceId` e `SourceUniqueId` para criar uma lista de todos os instantâneos associados a um disco específico. O exemplo a seguir listará todos os instantâneos incrementais associados a um disco específico, mas exigirá alguma configuração.

Este exemplo usa JQ para consultar os dados. Para executar o exemplo, você deve [instalar o JQ](https://stedolan.github.io/jq/download/).

Substitua `<yourResourceGroupNameHere>` e `<exampleDiskName>` por seus valores, você pode usar o exemplo a seguir para listar seus instantâneos incrementais existentes, desde que você também tenha instalado o JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode usar modelos de Azure Resource Manager para criar um instantâneo incremental. Você precisará certificar-se de que apiVersion está definido como **2019-03-01** e que a propriedade incremental também é definida como true. O trecho a seguir é um exemplo de como criar um instantâneo incremental com modelos do Resource Manager:

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

Se você quiser ver um exemplo de código que demonstra a capacidade diferencial de instantâneos incrementais, usando o .NET, consulte [copiar backups de Managed disks do Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
