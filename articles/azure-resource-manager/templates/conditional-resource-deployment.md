---
title: Implantação condicional com modelos
description: Descreve como implantar condicionalmente um recurso em um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153413"
---
# <a name="conditional-deployment-in-arm-templates"></a>Implantação condicional em modelos ARM

Às vezes, você precisa implantar opcionalmente um recurso em um modelo ARM (Azure Resource Manager). Use `condition` o elemento para especificar se o recurso está implantado. O valor desse elemento é resolvido como verdadeiro ou falso. Quando o valor for true, o recurso será criado. Quando o valor for false, o recurso não será criado. O valor só pode ser aplicado para o recurso inteiro.

## <a name="new-or-existing-resource"></a>Recurso novo ou existente

Você pode usar a implantação condicional para criar um novo recurso ou usar um já existente. O exemplo a seguir mostra como usar a condição para implantar uma nova conta de armazenamento ou usar uma conta de armazenamento existente.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Quando o parâmetro **newOrExisting** é definido como **novo,** a condição avalia como verdadeira. A conta de armazenamento é implantada. No entanto, quando **o newOrExisting** é definido como **existente,** a condição avalia-se como falsa e a conta de armazenamento não é implantada.

Para obter um modelo de exemplo completo que usa o elemento `condition`, confira [VM com uma Rede Virtual nova ou existente, um Armazenamento e um IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Permitir condição

Você pode passar em um valor de parâmetro que indica se uma condição é permitida. O exemplo a seguir implanta um servidor SQL e permite opcionalmente os IPs do Azure.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Para obter o modelo completo, consulte [o servidor lógico Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Funções de tempo de execução

Se você usar uma função [de referência](template-functions-resource.md#reference) ou [lista](template-functions-resource.md#list) com um recurso que seja implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado. Você recebe um erro se a função se refere a um recurso que não existe.

Use a função [if](template-functions-logical.md#if) para garantir que a função só seja avaliada para condições quando o recurso for implantado. Consulte a [função if](template-functions-logical.md#if) para um modelo de exemplo que use se e faça referência com um recurso implantado condicionalmente.

Você define um [recurso tão dependente de](define-resource-dependency.md) um recurso condicional exatamente como qualquer outro recurso. Quando um recurso condicional não é implantado, o Azure Resource Manager remove-o automaticamente das dependências necessárias.

## <a name="condition-with-complete-mode"></a>Condição com modo completo

Se você implantar um modelo com [modo completo](deployment-modes.md) e um recurso não for implantado porque a condição avalia como falsa, o resultado depende de qual versão da API REST você usa para implantar o modelo. Se você usar uma versão anterior a 2019-05-10, o recurso **não será excluído**. Com 2019-05-10 ou posterior, o recurso **é excluído**. As versões mais recentes do Azure PowerShell e do Azure CLI excluem o recurso quando a condição é falsa.

## <a name="next-steps"></a>Próximas etapas

* Para obter recomendações sobre a criação de modelos, consulte [as práticas recomendadas do modelo ARM](template-best-practices.md).
* Para criar várias instâncias de um recurso, consulte [iteração de recursos em modelos ARM](copy-resources.md).