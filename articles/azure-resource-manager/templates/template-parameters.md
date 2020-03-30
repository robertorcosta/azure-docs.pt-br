---
title: Parâmetros em modelos
description: Descreve como definir parâmetros em um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122416"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parâmetros nos modelos do Azure Resource Manager

Este artigo descreve como definir e usar parâmetros no modelo do Azure Resource Manager. Ao fornecer valores diferentes para parâmetros, você pode reutilizar um modelo para diferentes ambientes.

O Gerenciador de recursos resolve os valores dos parâmetros antes de iniciar as operações de implantação. Onde quer que o parâmetro seja usado no modelo, o Gerenciador de recursos substitui-o pelo valor resolvido.

## <a name="define-parameter"></a>Definir parâmetro

O exemplo a seguir mostra uma definição de parâmetro simples. Ele define um parâmetro chamado **storageSKU**. O parâmetro é um valor de string, e só aceita valores válidos para o seu uso pretendido. O parâmetro usa um valor padrão quando nenhum valor é fornecido durante a implantação.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>Usar parâmetro

No modelo, você faz referência ao valor do parâmetro usando a função [parâmetros.](template-functions-deployment.md#parameters) No exemplo a seguir, o valor do parâmetro é usado para definir SKU para a conta de armazenamento.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>Funções de modelo

Ao especificar o valor padrão para um parâmetro, você pode usar a maioria das funções de modelo. Você pode usar outro valor de parâmetro para criar um valor padrão. O modelo a seguir demonstra o uso de funções no valor padrão. Quando nenhum nome é fornecido para o site, ele cria um valor de string único e o anexa ao **site**. Quando nenhum nome é fornecido para o plano de host, ele leva o valor para o site e anexa **-plano**.

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Você não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista](template-functions-resource.md#list) na seção parâmetros. Essas funções recebem o estado de tempo de execução de um recurso e não podem ser executadas antes da implantação quando os parâmetros são resolvidos.

## <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados, passando-os como um objeto. Essa abordagem também reduz o número de parâmetros no modelo.

O exemplo a seguir mostra um parâmetro que é um objeto. O valor padrão mostra as propriedades esperadas para o objeto.

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Você faz referência às propriedades do objeto usando o operador de ponto.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir demonstram cenários para o uso de parâmetros.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções de valores padrão](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como usar funções de modelo ao definir valores padrão para parâmetros. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |
|[objeto parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra como usar um objeto para um parâmetro. O modelo não implanta todos os recursos. Ele cria valores de parâmetro e retorna os valores. |


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para parâmetros, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
* Para saber mais sobre a passagem de valores de parâmetros como um arquivo, consulte [Criar arquivo parâmetro do Gerenciador de recursos](parameter-files.md).
* Para recomendações sobre a criação de [parâmetros,](template-best-practices.md#parameters)consulte Melhores práticas - parâmetros .
