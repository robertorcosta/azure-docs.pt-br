---
title: Adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a um Service Fabric tipo de nó de cluster gerenciado (versão prévia)
description: Veja como adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a Service Fabric tipo de nó de cluster gerenciado
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 10487bad4fce41c68b4e2cb90c311b986d709eee
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410289"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a um Service Fabric tipo de nó de cluster gerenciado (versão prévia)

Cada tipo de nó em um Cluster Service Fabric gerenciado é apoiado por um conjunto de dimensionamento de máquinas virtuais. Isso permite que você adicione [extensões do conjunto de dimensionamento de máquinas virtuais](../virtual-machines/extensions/overview.md) a seus Service Fabric tipos de nó de cluster gerenciados.

Você pode adicionar uma extensão de conjunto de dimensionamento de máquinas virtuais a um tipo de nó usando o comando do PowerShell [Add-AzServiceFabricManagedNodeTypeVMExtension](https://docs.microsoft.com/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension?view=azps-4.7.0&preserve-view=true) .

Como alternativa, você pode uma extensão de conjunto de dimensionamento de máquinas virtuais em um Service Fabric tipo de nó de cluster gerenciado em seu modelo de Azure Resource Manager, por exemplo:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Para obter mais informações sobre como configurar Service Fabric tipos de nó de cluster gerenciado, consulte [tipo de nó de cluster gerenciado](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Service Fabric clusters gerenciados, consulte:

> [!div class="nextstepaction"]
> [Service Fabric perguntas frequentes sobre clusters gerenciados](./faq-managed-cluster.md)
