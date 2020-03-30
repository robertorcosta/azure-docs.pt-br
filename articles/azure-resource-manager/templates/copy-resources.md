---
title: Implantar várias instâncias de recursos
description: Use a operação de cópia e os arrays em um modelo do Azure Resource Manager para implantar o tipo de recurso muitas vezes.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153311"
---
# <a name="resource-iteration-in-arm-templates"></a>Iteração de recursos em modelos ARM

Este artigo mostra como criar mais de uma instância de um recurso no modelo ARM (Azure Resource Manager, gerenciador de recursos do Azure). Ao adicionar o elemento **de cópia** à seção de recursos do seu modelo, você pode definir dinamicamente o número de recursos a serem implantados. Você também evita ter que repetir a sintaxe do modelo.

Você também pode usar cópia com [propriedades,](copy-properties.md) [variáveis](copy-variables.md) e [saídas.](copy-outputs.md)

Caso precise especificar se um recurso é ou não implantado, confira [Elemento condition](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Iteração de recurso

O elemento de cópia tem o seguinte formato geral:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A propriedade **nome** é qualquer valor que identifique o loop. A propriedade **count** especifica o número de iterações desejadas para o tipo de recurso.

Use as propriedades **mode** e **batchSize** para especificar se os recursos estão implantados em paralelo ou em seqüência. Essas propriedades são descritas em [Serial ou Parallel](#serial-or-parallel).

O exemplo a seguir cria o número de contas de armazenamento especificadas no parâmetro **storageCount.**

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Observe que o nome de cada recurso inclui a função `copyIndex()`, que retorna a iteração atual no loop. `copyIndex()`é baseado em zero. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex())]",
```

Cria estes nomes:

* storage0
* storage1
* storage2.

Para deslocar o valor do índice, você pode passar um valor na função copyIndex(). O número de iterações ainda é especificado no elemento cópia, mas o valor do copyIndex é compensado pelo valor especificado. Assim, o seguinte exemplo:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Cria estes nomes:

* storage1
* storage2
* storage3

A operação de cópia é útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. Use a função `length` na matriz para especificar a contagem de iterações e `copyIndex` para recuperar o índice atual na matriz.

O exemplo a seguir cria uma conta de armazenamento para cada nome fornecido no parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Se você quiser devolver valores dos recursos implantados, você pode usar [cópia na seção saídas](copy-outputs.md).

## <a name="serial-or-parallel"></a>Serial ou Paralelo

Por padrão, o Gerenciador de Recursos cria os recursos em paralelo. Não aplica limite ao número de recursos implantados em paralelo, além do limite total de 800 recursos no modelo. A ordem em que eles são criados não é garantida.

No entanto, convém especificar que os recursos são implantados em sequência. Por exemplo, ao atualizar um ambiente de produção, convém balancear as atualizações para que apenas um determinado número seja atualizado por vez. Para implantar em série mais de uma instância de um recurso, defina `mode` para **serial** e `batchSize` para o número de instâncias a serem implantadas de cada vez. Com o modo serial, o Resource Manager cria uma dependência em instâncias anteriores no loop de modo que não inicie um lote até que o lote anterior esteja concluído.

Por exemplo, para implantar em série duas contas de armazenamento por vez, use:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

A propriedade de modo também aceita **paralelo**, que é o valor padrão.

## <a name="depend-on-resources-in-a-loop"></a>Depender dos recursos em um loop

Você especifica que um recurso é implantado após outro recurso usando o elemento `dependsOn`. Para implantar um recurso que depende da coleção de recursos em um loop, forneça o nome do loop de cópia no elemento dependsOn. O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina virtual. A definição completa da máquina virtual não é mostrada. Observe que o elemento de `storagecopy` cópia tem nome definido e o elemento `storagecopy`dependsOn para a máquina virtual também está definido como .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iteração para um recurso filho

Você não pode usar um loop de cópia para um recurso filho. Para criar mais de uma instância de um recurso que você normalmente define como aninhado em outro recurso, você deve criar esse recurso como um recurso de nível superior. Você define a relação com o recurso pai por meio das propriedades de tipo e nome.

Por exemplo, suponha que você normalmente defina um conjunto de dados como um recurso filho em uma data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Para criar mais de um conjunto de dados, mova-o para fora do data factory. O conjunto de dados deve estar no mesmo nível que a data factory, mas ainda é um recurso filho da data factory. Você preserva a relação entre um conjunto de dados e o data factory por meio das propriedades de tipo e nome. Como o tipo não pode ser inferido de sua posição no modelo, você deve fornecer o tipo totalmente qualificado no formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Para estabelecer uma relação pai/filho com uma instância da data factory, forneça um nome para o conjunto de dados que inclui o nome do recurso pai. Use o formato: `{parent-resource-name}/{child-resource-name}`.

O exemplo a seguir mostra a implementação:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Se você implantar um modelo com o Azure PowerShell 2.6 ou posterior, O Azure CLI 2.0.74 ou posterior, ou a versão rest API **2019-05-10** ou posterior, você pode definir a contagem para zero. Versões anteriores do PowerShell, CLI e a API REST não suportam zero para contagem.

Tenha cuidado com a [implantação completa do modo](deployment-modes.md) com cópia. Se você reimplantar com o modo completo para um grupo de recursos, todos os recursos que não forem especificados no modelo após a resolução do loop de cópia serão excluídos.

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram cenários comuns para criar mais de uma instância de um recurso ou propriedade.

|Modelo  |Descrição  |
|---------|---------|
|[Armazenamento de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implanta a mais de uma conta de armazenamento com um número de índice no nome. |
|[Armazenamento de cópia serial](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implanta várias contas de armazenamento ao tempo. O nome inclui o número de índice. |
|[Armazenamento de cópia com matriz](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implanta várias contas de armazenamento. O nome inclui um valor de uma matriz. |
|[Implantação da VM com um número variável de discos de dados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implanta vários discos de dados com uma máquina virtual. |
|[Várias regras de segurança](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implanta várias regras de segurança em um grupo de segurança de rede. Cria as regras de segurança a partir de um parâmetro. Para o parâmetro, consulte [vários arquivos de parâmetro NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Próximas etapas

* Para passar por um tutorial, consulte [Tutorial: crie várias instâncias de recursos usando modelos ARM](template-tutorial-create-multiple-instances.md).
* Para outros usos do elemento de cópia, consulte:
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Para obter informações sobre como usar cópia com modelos aninhados, consulte [Usando cópia](linked-templates.md#using-copy).
* Se você quiser aprender sobre as seções de um modelo, consulte [Modelos DE AUTORIA DO ARM](template-syntax.md).
* Para saber como implantar seu modelo, consulte [Implantar um aplicativo com o modelo ARM](deploy-powershell.md).

