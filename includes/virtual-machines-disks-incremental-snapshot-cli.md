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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343034"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Você pode criar um instantâneo incremental com o Cli do Azure, você precisará da versão mais recente do Azure CLI. 

No Windows, o seguinte comando instalará ou atualizará sua instalação existente para a versão mais recente:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
No Linux, a instalação da CLI varia dependendo da versão do sistema operacional.  Consulte [Instalar o Cli do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para sua versão específica do Linux.

Para criar um instantâneo incremental, use `--incremental` [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) com o parâmetro.

O exemplo a seguir cria `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>`um`<exampleDiskName>`instantâneo `<exampleLocation>` incremental, substitui, e com seus próprios valores, e executa o exemplo:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Você pode identificar instantâneos incrementais `SourceResourceId` do `SourceUniqueId` mesmo disco com as propriedades dos snapshots. `SourceResourceId`é o ID de recurso do Azure Resource Manager do disco pai. `SourceUniqueId`é o valor herdado da `UniqueId` propriedade do disco. Se você quiser excluir um disco e criar um novo disco `UniqueId` com o mesmo nome, o valor da propriedade será alterado.

Você pode `SourceResourceId` `SourceUniqueId` usar e criar uma lista de todos os instantâneos associados a um determinado disco. O exemplo a seguir listará todos os instantâneos incrementais associados a um disco específico, mas, ele requer alguma configuração.

Este exemplo usa jq para consultar os dados. Para executar o exemplo, você deve [instalar jq](https://stedolan.github.io/jq/download/).

`<yourResourceGroupNameHere>` Substitua `<exampleDiskName>` e com seus valores, então você pode usar o seguinte exemplo para listar seus instantâneos incrementais existentes, desde que você também tenha instalado o jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
