---
title: Automatize a implantação de recursos do aplicativo de função para o Azure
description: Aprenda a criar um modelo do Azure Resource Manager que implanta o aplicativo de funções.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d98d6fef896f9288be88824a62fa1c8179217f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276888"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implantação de recursos para seu aplicativo de funções do Azure Functions

Você pode usar um modelo do Azure Resource Manager para implantar um aplicativo de funções. Este artigo descreve os recursos e os parâmetros necessários para fazer isso. Talvez seja necessário implantar recursos adicionais, dependendo dos [gatilhos e associações](functions-triggers-bindings.md) em seu aplicativo de funções.

Para obter mais informações sobre a criação de modelos, consulte [Os modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Para modelos de exemplo, consulte:
- [Aplicativo de funções no Plano de Consumo]
- [Aplicativo de funções no Plano do Serviço de Aplicativo do Azure]

## <a name="required-resources"></a>Recursos necessários

Uma implantação de Funções do Azure geralmente consiste nesses recursos:

| Recurso                                                                           | Requisito | Referência de sintaxe e propriedades                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Um aplicativo de funções                                                                     | Obrigatório    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| Uma [conta de armazenamento azure](../storage/index.yml)                                   | Obrigatório    | [Microsoft.Armazenamento/armazenamentoContas](/azure/templates/microsoft.storage/storageaccounts) |   |
| Um componente [de insights de aplicativos](../azure-monitor/app/app-insights-overview.md) | Opcional    | [Microsoft.Insights/componentes](/azure/templates/microsoft.insights/components)         |   |
| Um [plano de hospedagem](./functions-scale.md)                                             | Opcional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup> Um plano de hospedagem só é necessário quando você optar por executar seu aplicativo de função em um [plano Premium](./functions-premium-plan.md) (em pré-visualização) ou em um plano de serviço [de aplicativo](../app-service/overview-hosting-plans.md).

> [!TIP]
> Embora não seja necessário, é fortemente recomendável que você configure insights de aplicativos para o seu aplicativo.

<a name="storage"></a>
### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para um aplicativo de funções. Você precisa de uma conta de finalidade geral que dá suporte a blobs, tabelas, consultas e arquivos. Para saber mais, confira [Requisitos da conta de armazenamento do Azure Functions](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Além disso, a propriedade `AzureWebJobsStorage` deve ser especificada como uma configuração de aplicativo na configuração do site. Se o aplicativo de função não usar o Application Insights para monitoramento, também deverá especificar `AzureWebJobsDashboard` como uma configuração de aplicativo.

O Azure Functions runtime usa a cadeia de conexão `AzureWebJobsStorage` para criar filas internas.  Quando o Application Insights não estiver habilitado, o runtime usará a cadeia de conexão `AzureWebJobsDashboard` para fazer logon no armazenamento de Tabela do Azure e capacitar a guia **Monitor** no portal.

Essas propriedades são especificadas na coleção `appSettings` no objeto `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

O Application Insights é recomendado para monitorar seus aplicativos de função. O recurso Application Insights é definido com o tipo **Microsoft.Insights/componentes** e o tipo **web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Além disso, a chave de instrumentação precisa ser `APPINSIGHTS_INSTRUMENTATIONKEY` fornecida ao aplicativo de função usando a configuração do aplicativo. Esta propriedade é especificada `appSettings` na `siteConfig` coleção no objeto:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plano de hospedagem

A definição do plano de hospedagem varia e pode ser uma das seguintes:
* [Plano de consumo](#consumption) (padrão)
* [Plano premium](#premium) (em pré-visualização)
* [Plano de serviço de aplicativos](#app-service-plan)

### <a name="function-app"></a>Aplicativo de funções

O recurso do aplicativo de função é definido usando um recurso do tipo **Microsoft.Web/sites** e kind **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Se você estiver definindo explicitamente um plano de hospedagem, um item adicional será necessário na matriz dependsOn:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Um aplicativo de função deve incluir essas configurações do aplicativo:

| Nome da configuração                 | Descrição                                                                               | Valores de exemplo                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Uma seqüência de conexão para uma conta de armazenamento que o tempo de execução Functions usa para filas internas | Ver [conta de armazenamento](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | A versão do tempo de execução do Azure Functions                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | A pilha de idiomas a ser usada para funções neste aplicativo                                   | `dotnet`, `node`, `java`, `python` ou `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Só é necessário `node` se usar a pilha de idiomas, especificar a versão a ser usada              | `10.14.1`                             |

Essas propriedades são especificadas `appSettings` na `siteConfig` coleção da propriedade:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Implantar no plano de consumo

O plano Consumo aloca automaticamente o poder de computação quando seu código está em execução, dimensiona conforme necessário para lidar com a carga e, em seguida, dimensiona quando o código não está em execução. Você não tem que pagar por VMs ociosos, e você não tem que reservar capacidade com antecedência. Para saber mais, confira [Escala e hospedagem do Azure Functions](functions-scale.md#consumption-plan).

Para um exemplo de modelo do Azure Resource Manager, consulte [Aplicativo de funções no Plano de Consumo].

### <a name="create-a-consumption-plan"></a>Criar um Plano de Consumo

Um plano de consumo não precisa ser definido. Um deles será criado ou selecionado automaticamente por região quando você criar o próprio recurso do aplicativo de função.

O plano de Consumo é um tipo especial de recurso "serverfarm". Para o Windows, você pode `Dynamic` especá-lo usando o valor para as `computeMode` propriedades e: `sku`

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> O plano de consumo não pode ser explicitamente definido para o Linux. Ele será criado automaticamente.

Se você definir explicitamente o seu plano de `serverFarmId` consumo, você precisará definir a propriedade no aplicativo para que ele aponte para o ID de recurso do plano. Você deve garantir que o `dependsOn` aplicativo de função tenha uma configuração para o plano também.

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

#### <a name="windows"></a>Windows

No Windows, um plano de consumo requer duas `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` `WEBSITE_CONTENTSHARE`configurações adicionais na configuração do site: e . Essas propriedades configuram a conta de armazenamento e o caminho do arquivo em que o código e as configurações do aplicativo de funções estão armazenados.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

No Linux, o aplicativo `kind` de `functionapp,linux`função deve ter `reserved` o seu `true`definido para , e ele deve ter a propriedade definida como :

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Implantar no plano Premium

O plano Premium oferece o mesmo dimensionamento do plano de consumo, mas inclui recursos dedicados e recursos adicionais. Para saber mais, consulte [O Plano Premium de Funções do Azure](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Criar um plano Premium

Um plano Premium é um tipo especial de recurso "serverfarm". Você pode especá-lo `EP3` usando `Name` ou `EP1` `EP2`para `sku` o valor da propriedade no [objeto de descrição](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

Um aplicativo de função em `serverFarmId` um plano Premium deve ter a propriedade definida para o ID de recurso do plano criado anteriormente. Além disso, um plano Premium requer duas configurações adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Essas propriedades configuram a conta de armazenamento e o caminho do arquivo em que o código e as configurações do aplicativo de funções estão armazenados.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Implantar no plano de serviço de aplicativos

No Plano do Serviço de Aplicativo, seus aplicativos de funções são executados em VMs dedicadas, em SKUs Basic, Standard e Premium, assim como os aplicativos Web. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md).

Para um exemplo de modelo do Azure Resource Manager, consulte [Aplicativo de funções no Plano do Serviço de Aplicativo do Azure].

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Um plano de Serviço de Aplicativo é definido por um recurso "serverfarm".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Para executar seu aplicativo no Linux, `kind` `Linux`você também deve definir o :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

Um aplicativo de função em um `serverFarmId` plano de Serviço de Aplicativo deve ter a propriedade definida para o ID de recurso do plano criado anteriormente.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Os aplicativos Linux `linuxFxVersion` também `siteConfig`devem incluir uma propriedade em . Se você estiver apenas implantando código, o valor para isso é determinado pela pilha de tempo de execução desejada:

| Pilha            | Valor de exemplo                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Se você estiver [implantando uma imagem de contêiner personalizada,](./functions-create-function-linux-custom-image.md)você deve especá-la com `linuxFxVersion` e incluir configuração que permita que sua imagem seja puxada, como no Web App for [Containers](/azure/app-service/containers). Além disso, defina `WEBSITES_ENABLE_APP_SERVICE_STORAGE` para `false`, uma vez que o conteúdo do seu aplicativo é fornecido no próprio contêiner:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Personalização de uma implantação

Um aplicativo de funções tem muitos recursos filho que podem ser usados na sua implantação, incluindo configurações do aplicativo e opções de controle do código-fonte. Você também pode optar por remover o recurso filho **do sourcecontrols** e usar uma opção de [implantação](functions-continuous-deployment.md) diferente.

> [!IMPORTANT]
> Para implantar seu aplicativo com êxito usando o Azure Resource Manager, é importante entender como os recursos são implantados no Azure. No exemplo a seguir, as configurações de nível superior são aplicadas usando **siteConfig**. É importante definir essas configurações em um nível superior porque transmitem informações para o mecanismo de implantação e de runtime do Functions. Informações de nível superior são necessárias antes do recurso filho **sourcecontrols/web** ser aplicado. Embora seja possível definir essas configurações no nível de recurso filho **config/appSettings**, em alguns casos, o aplicativo de funções deve ser implantado *antes de * **config/appSettings** ser aplicado. Por exemplo, quando você está usado funções com [aplicativos lógicos](../logic-apps/index.yml), as funções são uma dependência de outro recurso.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Este modelo usa o valor de configurações do aplicativo [Project,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) que define o diretório base no qual o mecanismo de implantação functions (Kudu) procura um código implantável. Em nosso repositório, nossas funções estão em uma subpasta da pasta **src**. Assim, no exemplo anterior, definimos o valor de configurações do aplicativo para `src`. Se as funções estão na raiz do seu repositório, ou se não está implantando do controle de origem, você pode remover esse valor de configurações do aplicativo.

## <a name="deploy-your-template"></a>Implantar o modelo

Você pode usar qualquer uma das seguintes maneiras para implantar o modelo:

* [Powershell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Portal Azure](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botão Implantar no Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` por uma versão [codificada de URL](https://www.bing.com/search?q=url+encode) do caminho bruto de seu `azuredeploy.json` arquivo no GitHub.

Este é um exemplo que usa markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Este é um exemplo que usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implantar usando o PowerShell

Os seguintes comandos do PowerShell criam um grupo de recursos e implantam um modelo que cria um aplicativo de função com seus recursos necessários. Para ser executado localmente, você deve ter [o Azure PowerShell](/powershell/azure/install-az-ps) instalado. Corra [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) para entrar.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Para testar essa implantação, você pode usar um [modelo como este](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) que cria um aplicativo de função no Windows em um plano de consumo. Substitua por `<function-app-name>` um nome exclusivo para o seu aplicativo de função.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como desenvolver e configurar o Azure Functions.

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Como configurar as configurações do aplicativo de função Azure](functions-how-to-use-azure-function-app-settings.md)
* [Criar sua primeira função do Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplicativo de funções no Plano de Consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplicativo de funções no Plano do Serviço de Aplicativo do Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
