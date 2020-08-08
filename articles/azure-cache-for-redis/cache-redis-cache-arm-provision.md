---
title: Implantar o cache do Azure para Redis com o Azure Resource Manager
description: Saiba como usar um modelo de Azure Resource Manager para implantar um cache do Azure para o recurso Redis. Os modelos são fornecidos para cenários comuns.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 2d00a6b7753a61bb2527a56231b2fe054736f1b0
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008569"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Criar um Cache Redis do Azure usando um modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Neste tópico, você aprende a criar um modelo do Azure Resource Manager que implanta um aplicativo do Cache Redis do Azure. O cache pode ser usado com uma conta de armazenamento existente para manter os dados de diagnóstico. Você também aprende como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Atualmente, as configurações de diagnóstico são compartilhadas por todos os caches na mesma região de uma assinatura. A atualização de um cache na região afeta todos os outros caches na região.

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/templates/template-syntax.md). Para saber mais sobre a sintaxe JSON e as propriedades dos tipos de recurso de cache, consulte [Tipos de recurso de Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

Para obter o modelo completo, consulte [Modelo do Cache Redis do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Os modelos do Resource Manager para a nova [camada Premium](cache-overview.md#service-tiers) estão disponíveis. 
> 
> * [Criar um Cache do Azure para Redis Premium com clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Criar um Cache Redis do Azure Premium com persistência de dados](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Criar um cache Redis Premium implantado em uma rede virtual](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Para verificar os modelos mais recentes, consulte [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) e procure por `Azure Cache for Redis`.
> 
> 

## <a name="what-you-will-deploy"></a>O que você implantará
Neste modelo, você implantará um Cache Redis do Azure que usa uma conta de armazenamento existente para os dados de diagnóstico.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro.
Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
O local do Cache Redis do Azure. Para obter o melhor desempenho, use o mesmo local que o aplicativo a ser usado com o cache.

```json
  "redisCacheLocation": {
    "type": "string"
  }
```

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
O nome da conta de armazenamento existente a ser usada para o diagnóstico. 

```json
  "existingDiagnosticsStorageAccountName": {
    "type": "string"
  }
```

### <a name="enablenonsslport"></a>enableNonSslPort
Um valor booliano que indica a permissão de acesso por meio de portas não SSL.

```json
  "enableNonSslPort": {
    "type": "bool"
  }
```

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Um valor que indica se o diagnóstico está habilitado. Use ATIVAR ou DESATIVAR.

```json
  "diagnosticsStatus": {
    "type": "string",
    "defaultValue": "ON",
    "allowedValues": [
          "ON",
          "OFF"
      ]
  }
```

## <a name="resources-to-deploy"></a>Recursos a implantar
### <a name="azure-cache-for-redis"></a>Cache Redis do Azure
Cria o Cache Redis do Azure.

```json
  {
    "apiVersion": "2015-08-01",
    "name": "[parameters('redisCacheName')]",
    "type": "Microsoft.Cache/Redis",
    "location": "[parameters('redisCacheLocation')]",
    "properties": {
      "enableNonSslPort": "[parameters('enableNonSslPort')]",
      "sku": {
        "capacity": "[parameters('redisCacheCapacity')]",
        "family": "[parameters('redisCacheFamily')]",
        "name": "[parameters('redisCacheSKU')]"
      }
    },
    "resources": [
      {
        "apiVersion": "2017-05-01-preview",
        "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
        "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
        "location": "[parameters('redisCacheLocation')]",
        "dependsOn": [
          "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
        ],
        "properties": {
          "status": "[parameters('diagnosticsStatus')]",
          "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
        }
      }
    ]
  }
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
