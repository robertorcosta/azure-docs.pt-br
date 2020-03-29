---
title: Criptografia de disco azure e escala de máquina virtual do Azure define sequenciamento de extensão
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Linux.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279024"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Usar o Azure Disk Encryption com o sequenciamento de extensão do conjunto de dimensionamento de máquinas virtuais

Extensões como a criptografia de disco Azure podem ser adicionadas a uma escala de máquinas virtuais do Azure definida em uma ordem especificada. Para isso, use [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md). 

Em geral, a criptografia deve ser aplicada a um disco:

- Após extensões ou scripts personalizados que preparam os discos ou volumes.
- Antes de extensões ou scripts personalizados que acessam ou consomem os dados nos discos ou volumes criptografados.

Em ambos os `provisionAfterExtensions` casos, a propriedade designa qual extensão deve ser adicionada posteriormente na seqüência.

## <a name="sample-azure-templates"></a>Exemplo de modelos Azure

Se você deseja que o Azure Disk Encryption `provisionAfterExtensions` seja aplicado após outra extensão, coloque a propriedade no bloco de extensão AzureDiskEncryption. 

Aqui está um exemplo usando "CustomScriptExtension", um script do Powershell que inicializa e formata um disco do Windows, seguido por "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Se você deseja que o Azure Disk Encryption `provisionAfterExtensions` seja aplicado antes de outra extensão, coloque a propriedade no bloco da extensão a seguir.

Aqui está um exemplo usando "AzureDiskEncryption" seguido de "VMDiagnosticsSettings", uma extensão que fornece recursos de monitoramento e diagnóstico em um VM Azure baseado no Windows:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Para obter um modelo mais aprofundado, consulte:
* Aplique a extensão Azure Disk Encryption após um script shell personalizado que formata o disco (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre sequenciamento de extensão: [Provisionamento de extensão de seqüência em conjuntos de escala de máquinavirtual](virtual-machine-scale-sets-extension-sequencing.md).
- Saiba mais `provisionAfterExtensions` sobre a propriedade: [Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure Disk Encryption para Conjuntos de Dimensionamento de Máquinas Virtuais](disk-encryption-overview.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](disk-encryption-cli.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando o Azure PowerShell](disk-encryption-powershell.md)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md)
