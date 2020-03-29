---
title: Consulte uma rede virtual existente em um modelo de conjunto de escala sinuoso
description: Saiba como adicionar uma rede virtual a um modelo existente do conjunto de dimensionamento de máquinas virtuais do Azure
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275543"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicionar referência a uma rede virtual existente em um modelo do conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de escala básica](virtual-machine-scale-sets-mvss-start.md) para implantar em uma rede virtual existente em vez de criar uma nova.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Em um [artigo anterior,](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo de conjunto de escala supérdia. Agora usaremos esse modelo anterior e o modificaremos para criar um modelo que implante uma escala definida em uma rede virtual existente. 

Primeiro, adicione um parâmetro `subnetId`. Essa cadeia de caracteres será passada para a configuração do conjunto de dimensionamento, permitindo que o conjunto de dimensionamento identifique a sub-rede pré-criada na qual implantará máquinas virtuais. Esta seqüência deve ser da forma:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Por exemplo implantar o conjunto de dimensionamento em uma rede virtual existente com o nome `myvnet`, sub-rede `mysubnet`, grupo de recursos `myrg` e assinatura`00000000-0000-0000-0000-000000000000`, o subnetId seria: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Em seguida, exclua o recurso de rede virtual da matriz `resources`, pois você usará uma rede virtual existente e não precisará implantar uma nova.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

A rede virtual já existe antes do modelo ser implantado e, portanto, não é necessário especificar uma cláusula dependsOn do conjunto de dimensionamento para a rede virtual. Exclua as seguintes linhas:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Finalmente, passe `subnetId` no parâmetro definido pelo usuário `resourceId` (em vez de usar para obter o ID de um vnet na mesma implantação, que é o que o modelo básico de conjunto de escala viável faz).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
