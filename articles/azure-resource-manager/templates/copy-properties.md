---
title: Definir várias instâncias de uma propriedade
description: Use a operação de cópia em um modelo do Azure Resource Manager para iterar várias vezes ao criar uma propriedade em um recurso.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e86d38b0e5d2e39d54b3c419b6eebdcda74022db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258100"
---
# <a name="property-iteration-in-arm-templates"></a>Iteração de propriedade em modelos ARM

Este artigo mostra como criar mais de uma instância de uma propriedade no modelo ARM (Azure Resource Manager, gerenciador de recursos do Azure). Ao adicionar o elemento **de cópia** à seção de propriedades de um recurso em seu modelo, você pode definir dinamicamente o número de itens para uma propriedade durante a implantação. Você também evita ter que repetir a sintaxe do modelo.

Você também pode usar cópia com [recursos,](copy-resources.md) [variáveis](copy-variables.md)e [saídas.](copy-outputs.md)

## <a name="property-iteration"></a>Iteração de propriedade

O elemento de cópia tem o seguinte formato geral:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Para **nome,** forneça o nome da propriedade de recurso que deseja criar. A propriedade **de contagem** especifica o número de iterações desejadas para a propriedade.

A propriedade **de entrada** especifica as propriedades que você deseja repetir. Você cria uma matriz de elementos construídos a partir do valor na propriedade **de entrada.**

O exemplo a seguir mostra como aplicar `copy` para a propriedade dataDisks em uma máquina virtual:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Observe que ao usar `copyIndex` dentro de uma iteração de propriedade, você deve fornecer o nome da iteração.

> [!NOTE]
> A iteração de propriedade também suporta um argumento de deslocamento. A compensação deve vir após o nome da iteração, como copyIndex ('dataDisks', 1).
>

Gerenciador de recursos expande a matriz `copy` durante a implantação. O nome da matriz se torna o nome da propriedade. Os valores de entrada se tornam as propriedades do objeto. O modelo implantado se torna:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

O elemento de cópia está em uma matriz, assim você pode especificar mais de uma propriedade para o recurso.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Você pode usar iteração de recurso e propriedade juntos. Referência a iteração de propriedade por nome.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Se você implantar um modelo com o Azure PowerShell 2.6 ou posterior, O Azure CLI 2.0.74 ou posterior, ou a versão rest API **2019-05-10** ou posterior, você pode definir a contagem para zero. Versões anteriores do PowerShell, CLI e a API REST não suportam zero para contagem.

## <a name="example-templates"></a>Modelos de exemplo

O exemplo a seguir mostra um cenário comum para criar mais de um valor para um imóvel.

|Modelo  |Descrição  |
|---------|---------|
|[Implantação da VM com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implanta vários discos de dados com uma máquina virtual. |

## <a name="next-steps"></a>Próximas etapas

* Para passar por um tutorial, consulte [Tutorial: crie várias instâncias de recursos usando modelos ARM](template-tutorial-create-multiple-instances.md).
* Para outros usos do elemento de cópia, consulte:
  * [Iteração de recursos em modelos ARM](copy-resources.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Se você quiser aprender sobre as seções de um modelo, consulte [Modelos DE AUTORIA DO ARM](template-syntax.md).
* Para saber como implantar seu modelo, consulte [Implantar um aplicativo com o modelo ARM](deploy-powershell.md).

