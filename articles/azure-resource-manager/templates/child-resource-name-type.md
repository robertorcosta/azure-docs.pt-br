---
title: Recursos infantis em modelos
description: Descreve como definir o nome e o tipo de recursos de criança em um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 6de2b476fe19a057a62e4a54963dd8fde0d11579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207886"
---
# <a name="set-name-and-type-for-child-resources"></a>Definir nome e tipo para recursos infantis

Recursos infantis são recursos que existem apenas no contexto de outro recurso. Por exemplo, uma [extensão de máquina virtual](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) não pode existir sem uma [máquina virtual.](/azure/templates/microsoft.compute/2019-03-01/virtualmachines) O recurso de extensão é filho da máquina virtual.

Em um modelo de Manjedoura de recursos, você pode especificar o recurso filho dentro do recurso pai ou fora do recurso pai. O exemplo a seguir mostra o recurso filho incluído na propriedade de recursos do recurso pai.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

O próximo exemplo mostra o recurso filho fora do recurso pai. Você pode usar essa abordagem se o recurso pai não for implantado no mesmo modelo ou se quiser usar a [cópia](copy-resources.md) para criar mais de um recurso filho.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Os valores fornecidos para o nome e o tipo do recurso variam de acordo com se o recurso filho é definido dentro ou fora do recurso pai.

## <a name="within-parent-resource"></a>Dentro do recurso pai

Quando definido dentro do tipo de recurso pai, você formatar os valores de tipo e nome como uma única palavra sem cortes.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e com uma sub-rede. Observe que a sub-rede está incluída no array de recursos para a rede virtual. O nome é definido como **Subnet1** e o tipo é definido como **sub-redes**. O recurso filho é marcado como dependente do recurso pai porque o recurso pai deve existir antes que o recurso filho possa ser implantado.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

O tipo completo de recurso ainda é **Microsoft.Network/virtualNetworks/sub-nets**. Você não fornece **Microsoft.Network/virtualNetworks/** porque ele é assumido a partir do tipo de recurso pai.

O nome do recurso filho é definido **como Subnet1,** mas o nome completo inclui o nome dos pais. Você não fornece **O VNet1** porque ele é assumido a partir do recurso pai.

## <a name="outside-parent-resource"></a>Recurso pai externo

Quando definido fora do recurso pai, você formatar o tipo e com barras para incluir o tipo e nome pai.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

O exemplo a seguir mostra uma rede virtual e uma sub-rede que são definidas no nível raiz. Observe que a sub-rede não está incluída no array de recursos para a rede virtual. O nome está definido como **VNet1/Subnet1** e o tipo está definido como **Microsoft.Network/virtualNetworks/sub-nets**. O recurso filho é marcado como dependente do recurso pai porque o recurso pai deve existir antes que o recurso filho possa ser implantado.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos](template-syntax.md).

* Para saber mais sobre o formato do nome do recurso ao fazer referência ao recurso, consulte a [função de referência](template-functions-resource.md#reference).
