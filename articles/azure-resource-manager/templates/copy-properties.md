---
title: Definir várias instâncias de uma propriedade
description: Use a operação de cópia em um modelo de Azure Resource Manager para iterar várias vezes ao criar uma propriedade em um recurso.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b1e31f981f361b4cfbe7e7930f2c70bfce8b8656
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210860"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Iteração de propriedade em modelos de Azure Resource Manager

Este artigo mostra como criar mais de uma instância de uma propriedade em seu modelo de Azure Resource Manager. Ao adicionar o elemento **copiar** à seção Propriedades de um recurso em seu modelo, você pode definir dinamicamente o número de itens para uma propriedade durante a implantação. Você também evita a repetição da sintaxe do modelo.

Você também pode usar copiar com [recursos](copy-resources.md) e [variáveis](copy-variables.md).

## <a name="property-iteration"></a>Iteração de propriedade

O elemento Copy tem o seguinte formato geral:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Para **nome**, forneça o nome da propriedade de recurso que você deseja criar. A propriedade **Count** especifica o número de iterações que você deseja para a propriedade.

A propriedade de **entrada** especifica as propriedades que você deseja repetir. Você cria uma matriz de elementos construídos com base no valor na propriedade de **entrada** .

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
> A iteração de propriedade também dá suporte a um argumento offset. O deslocamento deve vir após o nome da iteração, como copyIndex (' datadisks ', 1).
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
  "name": "examleLB",
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

A contagem não pode ser um número negativo. Se você implantar um modelo com Azure PowerShell 2,6 ou posterior, CLI do Azure 2.0.74 ou posterior, ou a API REST versão **2019-05-10** ou posterior, poderá definir Count como zero. As versões anteriores do PowerShell, da CLI e da API REST não dão suporte a zero para contagem.

## <a name="example-templates"></a>Modelos de exemplo

O exemplo a seguir mostra um cenário comum para criar mais de um valor para uma propriedade.

|Modelo  |DESCRIÇÃO  |
|---------|---------|
|[Implantação da VM com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implanta vários discos de dados com uma máquina virtual. |

## <a name="next-steps"></a>Próximas etapas

* Para passar por um tutorial, consulte [Tutorial: crie várias instâncias de recursos usando os modelos do Resource Manager](template-tutorial-create-multiple-instances.md).
* Para outros usos do elemento Copy, consulte [iteração de recursos em modelos de Azure Resource Manager](copy-resources.md) e [iteração de variável em modelos de Azure Resource Manager](copy-variables.md).
* Para saber mais sobre as seções de um modelo, veja [Criando modelos do Azure Resource Manager](template-syntax.md).
* Para saber mais sobre como implantar o modelo, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](deploy-powershell.md).

