---
title: Extensão de máquina virtual de dependência Azure Monitor para Linux
description: Implante o Azure Monitor agente de dependência na máquina virtual Linux usando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383411"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual de dependência Azure Monitor para Linux

O recurso Mapa do Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. A extensão da máquina virtual do agente de dependência de VM do Azure para Linux é publicada e suportada pela Microsoft. A extensão instala o agente de dependência em máquinas virtuais do Azure. Este documento detalha as plataformas com suporte, as configurações e as opções de implantação para a extensão de máquina virtual do agente de dependência de VM do Azure para Linux.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

### <a name="operating-system"></a>Sistema operacional

A extensão do agente de dependência de VM do Azure para Linux pode ser executada nos sistemas operacionais com suporte listados na seção [sistemas operacionais com suporte](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) no artigo Azure monitor para VMs implantação.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente de dependência de VM do Azure em uma VM Linux do Azure. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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

| {1&gt;Nome&lt;1} | Valor/exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Implantação de modelo

Você pode implantar extensões de VM do Azure com os modelos do Azure Resource Manager. Você pode usar o esquema JSON detalhado na seção anterior em um modelo de Azure Resource Manager para executar a extensão do agente de dependência de VM do Azure durante uma implantação de modelo de Azure Resource Manager.

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual. Ou você pode colocá-lo no nível raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [Definir o nome e o tipo de recursos filho](../../azure-resource-manager/templates/child-resource-name-type.md).

O exemplo a seguir pressupõe que a extensão do agente de dependência está aninhada dentro do recurso de máquina virtual. Quando você Aninha o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.


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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Quando você coloca a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual pai. O tipo reflete a configuração aninhada. 

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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

Você pode usar o CLI do Azure para implantar a extensão de VM do agente de dependência em uma máquina virtual existente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o CLI do Azure. Para ver o estado de implantação das extensões de uma determinada VM, execute o seguinte comando usando o CLI do Azure:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução da extensão é registrada no seguinte arquivo:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Ou, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
