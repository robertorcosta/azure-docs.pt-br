---
title: Extensão da máquina virtual do Azure Monitor Dependency para Windows
description: Implante o agente de dependência do Monitor do Azure na máquina virtual do Windows usando uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250667"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual do Azure Monitor Dependency para Windows

O recurso Mapa do Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. A extensão da máquina virtual do agente azure VM Dependency para Windows é publicada e suportada pela Microsoft. A extensão instala o agente Dependency em máquinas virtuais Do Zure. Este documento detalha as plataformas, configurações e opções de implantação suportadas para a extensão da máquina virtual do agente de dependência do Azure VM para Windows.

## <a name="operating-system"></a>Sistema operacional

A extensão do agente azure VM Dependency para Windows pode ser executada contra os sistemas operacionais suportados listados na seção [de sistemas operacionais suportados](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) do artigo de implantação do Monitor Azure para VMs.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente de dependência do Azure VM em uma VM do Windows Azure.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publicador | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Implantação de modelo

Você pode implantar as extensões do Azure VM com os modelos do Azure Resource Manager. Você pode usar o esquema JSON detalhado na seção anterior em um modelo do Azure Resource Manager para executar a extensão do agente de dependência do Azure VM durante uma implantação de modelo do Azure Resource Manager.

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso da máquina virtual. Ou, você pode colocá-lo na raiz ou no nível superior de um modelo JSON do Gerenciador de Recursos. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir nome e digite para recursos de crianças](../../azure-resource-manager/templates/child-resource-name-type.md).

O exemplo a seguir assume que a extensão do agente dependency está aninhada dentro do recurso da máquina virtual. Quando você aninha o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Quando você coloca o JSON de extensão na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual pai. O tipo reflete a configuração aninhada.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>Implantação do PowerShell

Você pode `Set-AzVMExtension` usar o comando para implantar a extensão da máquina virtual do agente dependency em uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela hash do PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal Azure e usando o módulo Azure PowerShell. Para ver o estado de implantação das extensões para uma determinada VM, execute o seguinte comando usando o módulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução da extensão é registrada nos arquivos localizados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Suporte

Se você precisar de mais ajuda em qualquer ponto deste artigo, você pode entrar em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou, você pode registrar um incidente de suporte azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para obter informações sobre como usar o Suporte ao Azure, leia o suporte ao [Microsoft Azure .](https://azure.microsoft.com/support/faq/)
