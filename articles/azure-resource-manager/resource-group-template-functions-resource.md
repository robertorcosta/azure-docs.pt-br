---
title: Funções de modelo do Azure Resource Manager – recursos | Microsoft Docs
description: Descreve as funções a serem usadas em um modelo do Azure Resource Manager para recuperar valores sobre recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: tomfitz
ms.openlocfilehash: dc39c727526f55039a5e18a8fd2aeeb4f25234a6
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965623"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funções de recursos para modelos do Azure Resource Manager

O Gerenciador de Recursos fornece as seguintes funções para obter valores de recurso:

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [assinatura](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Para obter valores de parâmetros, de variáveis ou da implantação atual, veja [Funções de valor de implantação](resource-group-template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Retorna a ID de recurso para um [recurso de extensão](extension-resource-types.md), que é um tipo de recurso que é aplicado a outro recurso a ser adicionado aos seus recursos.

### <a name="parameters"></a>parâmetros

| . | obrigatórios | Type | Descrição |
|:--- |:--- |:--- |:--- |
| ResourceId |SIM |string |A ID de recurso para o recurso ao qual o recurso de extensão é aplicado. |
| resourceType |SIM |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |SIM |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

O formato básico da ID do recurso retornado por essa função é:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

O segmento de escopo varia de acordo com o recurso que está sendo estendido.

Quando o recurso de extensão é aplicado a um **recurso**, a ID do recurso é retornada no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um **grupo de recursos**, o formato é:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a uma **assinatura**, o formato é:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um **grupo de gerenciamento**, o formato é:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>exemplo de extensionResourceId

O exemplo a seguir retorna a ID de recurso para um bloqueio de grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>lista*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

A sintaxe dessa função varia de acordo com o nome das operações de lista. Cada implementação retorna valores para o tipo de recurso compatível com uma operação de lista. O nome da operação deve começar com `list`. Alguns usos comuns são `listKeys` e `listSecrets`. 

### <a name="parameters"></a>parâmetros

| . | obrigatórios | Type | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |SIM |string |Identificador exclusivo para o recurso. |
| apiVersion |SIM |string |Versão de API do estado de tempo de execução do recurso. Normalmente, no formato **aaaa-mm-dd**. |
| functionValues |Não |objeto | Um objeto que tem valores para a função. Fornecer apenas este objeto para funções que dão suporte ao recebimento de um objeto com valores de parâmetro, como **listAccountSas** em uma conta de armazenamento. Um exemplo de passar valores de função é mostrado neste artigo. | 

### <a name="valid-uses"></a>Usos válidos

As funções de lista só podem ser usadas nas propriedades de uma definição de recurso e na seção de saídas de um modelo ou implantação. Quando usado com a [iteração de propriedade](resource-group-create-multiple.md#property-iteration), você pode usar as funções de lista para `input` porque a expressão é atribuída à propriedade de recurso. Você não pode usá-los com `count` porque a contagem deve ser determinada antes que a função list seja resolvida.

### <a name="implementations"></a>Implementações

Os possíveis usos de lista* são mostrados na tabela a seguir.

| Tipo de recurso | Nome da função |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. AppConfiguration/configurationStores | ListKeys |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/Channels | listChannelWithKeys |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/registros/execuções | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/registros/tarefas | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. Data Box/Jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/domínios | [listKeys](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. LabServices/usuários | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/usuários | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/workflows/Versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | listKeys |
| Microsoft.MachineLearningServices/workspaces | listKeys |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/APIs/conexões | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/APIs/conexões | listsecrets |
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Para determinar quais tipos de recursos têm uma operação de lista, use as seguintes opções:

* Veja as [operações da API REST](/rest/api/) de um provedor de recursos e procure por operações de lista. Por exemplo, as contas de armazenamento têm a [operação listKeys](/rest/api/storagerp/storageaccounts).
* Use o cmdlet do PowerShell [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation). O exemplo a seguir obtém todas as operações de lista de contas de armazenamento:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Use o seguinte comando da CLI do Azure para filtrar apenas as operações de lista:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Valor de retorno

O objeto retornado varia de acordo com a função de lista que você usa. Por exemplo, o listKeys para uma conta de armazenamento retorna o seguinte formato:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Outras funções de lista têm formatos diferentes de retorno. Para ver o formato de uma função, inclua-a na seção de saídas, conforme mostra o exemplo de modelo.

### <a name="remarks"></a>Comentários

Especifique o recurso usando o nome de recurso ou o [função resourceId](#resourceid). Ao usar uma função de lista no mesmo modelo que implanta o recurso referenciado, use o nome do recurso.

Se você usar uma função de **lista** em um recurso que é implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado. Você receberá um erro se a função de **lista** se referir a um recurso que não existe. Use a função **If** para garantir que a função só seja avaliada quando o recurso estiver sendo implantado. Consulte a [função If](resource-group-template-functions-logical.md#if) para obter um modelo de exemplo que usa If e List com um recurso implantado condicionalmente.

### <a name="list-example"></a>Exemplo de lista

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) a seguir mostra como retornar as chaves primárias e secundárias de uma conta de armazenamento na seção de saídas. Ele também retorna um token SAS para a conta de armazenamento. 

Para obter o token SAS, passe um objeto para a hora de expiração. A data de validade deve ser futura. Este exemplo destina-se a mostrar como você pode usar as funções da lista. Normalmente, você deve usar o token SAS em um valor de recurso em vez de retorná-lo como um valor de saída. Os valores de saída são armazenados no histórico de implantação e não são seguros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>providers

```json
providers(providerNamespace, [resourceType])
```

Retorna informações sobre um provedor de recursos e seus tipos de recursos com suporte. Se você não fornecer um tipo de recurso, a função retornará todos os tipos com suporte para o provedor de recursos.

### <a name="parameters"></a>parâmetros

| . | obrigatórios | Type | Descrição |
|:--- |:--- |:--- |:--- |
| providerNamespace |SIM |string |Namespace do provedor |
| resourceType |Não |string |O tipo de recurso no namespace especificado. |

### <a name="return-value"></a>Valor de retorno

Cada tipo com suporte é retornado no seguinte formato: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

A ordenação de matriz dos valores retornados não é garantida.

### <a name="providers-example"></a>Exemplo de provedores

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) a seguir mostra como usar a função do provedor:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Para o provedor de recursos **Microsoft.Web** e o tipo de recurso **sites**, o exemplo anterior retorna um objeto no seguinte formato:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Retorna um objeto que representa o estado de tempo de execução de um recurso.

### <a name="parameters"></a>parâmetros

| . | obrigatórios | Type | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |SIM |string |Nome ou identificador exclusivo de um recurso. Ao referenciar um recurso no modelo atual, forneça apenas o nome do recurso como parâmetro. Ao fazer referência a um recurso implantado anteriormente, forneça a ID do recurso. |
| apiVersion |Não |string |Versão da API do recurso especificado. Inclua esse parâmetro quando o recurso não estiver provisionado no mesmo modelo. Normalmente, no formato **aaaa-mm-dd**. Para obter as versões de API válidas para seu recurso, consulte [referência de modelo](/azure/templates/). |
| 'Full' |Não |string |Valor que especifica se o objeto de recurso completo deve ser retornado. Se você não especificar `'Full'`, apenas o objeto de propriedades do recurso será retornado. O objeto completo inclui valores como a ID do recurso e o local. |

### <a name="return-value"></a>Valor de retorno

Cada tipo de recurso retorna propriedades diferentes para a função de referência. A função não retorna um único formato predefinido. Além disso, o valor retornado será diferente caso você tenha especificado o objeto completo. Para ver as propriedades de um tipo de recurso, retorne o objeto na seção de saída, conforme mostra o exemplo.

### <a name="remarks"></a>Comentários

A função de referência recupera o estado de tempo de execução de um recurso implantado anteriormente ou um recurso implantado no modelo atual. Este artigo mostra exemplos de ambos os cenários.

Normalmente, você usa a função **reference** para retornar um valor específico de um objeto, como o URI do ponto de extremidade de blob ou o nome de domínio totalmente qualificado.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Use `'Full'` quando precisar de valores de recurso que não fizerem parte do esquema de propriedades. Por exemplo, para definir políticas de acesso ao cofre de chaves, obtenha as propriedades de identidade de uma máquina virtual.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Usos válidos

A função de referência pode ser usada somente nas propriedades de uma definição de recurso e na seção de saídas de um modelo ou uma implantação. Quando usado com a [iteração de propriedade](resource-group-create-multiple.md#property-iteration), você pode usar a função de referência para `input` porque a expressão é atribuída à propriedade de recurso. Você não pode usá-lo com `count` porque a contagem deve ser determinada antes que a função de referência seja resolvida.

Você não pode usar a função de referência nas saídas de um [modelo aninhado](resource-group-linked-templates.md#nested-template) para retornar um recurso que você implantou no modelo aninhado. Em vez disso, use um [modelo vinculado](resource-group-linked-templates.md#external-template).

Se você usar a função de **referência** em um recurso que é implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado.  Você receberá um erro se a função de **referência** se referir a um recurso que não existe. Use a função **If** para garantir que a função só seja avaliada quando o recurso estiver sendo implantado. Consulte a [função If](resource-group-template-functions-logical.md#if) para obter um modelo de exemplo que usa If e Reference com um recurso implantado condicionalmente.

### <a name="implicit-dependency"></a>Dependência implícita

Usando a função de referência, você implicitamente declarar que um recurso depende de outro recurso se o recurso referenciado é provisionado no mesmo modelo e consulte o recurso pelo seu nome (não a ID de recurso). Você não precisa usar a propriedade dependsOn também. A função não é avaliada até que o recurso referenciado conclua a implantação.

### <a name="resource-name-or-identifier"></a>Identificador ou nome do recurso

Ao fazer referência a um recurso que é implantado no mesmo modelo, forneça o nome do recurso.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ao fazer referência a um recurso que não está implantado no mesmo modelo, forneça a ID do recurso.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Para evitar ambigüidade sobre qual recurso você está referenciando, você pode fornecer um nome de recurso totalmente qualificado.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

Ao construir uma referência totalmente qualificada a um recurso, a ordem para combinação dos segmentos do tipo e do nome não é simplesmente uma concatenação dos dois. Em vez disso, após o namespace, use uma sequência de pares *tipo/nome* do menos específico para o mais específico:

**{Resource-Provider-namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-Name}]**

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`está correto, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto

### <a name="reference-example"></a>Exemplo de referência

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) a seguir implanta um recurso e faz referência a esse recurso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

O exemplo anterior retorna os dois objetos. O objeto de propriedades tem o seguinte formato:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

O objeto completo tem o seguinte formato:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) a seguir faz referência a uma conta de armazenamento que não está implantada nesse modelo. A conta de armazenamento já existe na mesma assinatura.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Retorna um objeto que representa o grupo de recursos atual. 

### <a name="return-value"></a>Valor de retorno

O objeto retornado está no seguinte formato:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

A propriedade **ManagedBy** é retornada somente para grupos de recursos que contêm recursos que são gerenciados por outro serviço. Para aplicativos gerenciados, databricks e AKS, o valor da propriedade é a ID de recurso do recurso de gerenciamento.

### <a name="remarks"></a>Comentários

A função `resourceGroup()` não pode ser usada em um modelo que está [implantado no nível da assinatura](deploy-to-subscription.md). Ela só pode ser usada em modelos que são implantados em um grupo de recursos.

Um uso comum da função resourceGroup é criar recursos no mesmo local que o grupo de recursos. O exemplo a seguir usa o local do grupo de recursos para atribuir o local de um site Web.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

Você também pode usar a função resourcegroup para aplicar marcas do grupo de recursos a um recurso. Para obter mais informações, consulte [aplicar marcas do grupo de recursos](resource-group-using-tags.md#apply-tags-from-resource-group).

### <a name="resource-group-example"></a>Exemplo de grupo de recursos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) a seguir retorna as propriedades do grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

O exemplo anterior retorna um objeto no seguinte formato:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>ResourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Retorna o identificador exclusivo de um recurso. Você pode usar essa função quando o nome do recurso é ambíguo ou não provisionado no mesmo modelo. 

### <a name="parameters"></a>parâmetros

| . | obrigatórios | Type | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |string (no formato GUID) |O valor padrão é a assinatura atual. Especifique esse valor quando você precisar recuperar um recurso em outra assinatura. |
| resourceGroupName |Não |string |O valor padrão é o grupo de recursos atual. Especifique esse valor quando você precisar recuperar um recurso em outro grupo de recursos. |
| resourceType |SIM |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |SIM |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

A ID do recurso é retornada no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Quando usado em uma [implantação em nível de assinatura](deploy-to-subscription.md), a ID de recurso é retornada no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Para obter a ID em outros formatos, consulte:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Comentários

O número de parâmetros que você fornece varia dependendo de o recurso ser um recurso pai ou filho e se o recurso está na mesma assinatura ou grupo de recursos.

Para obter a ID de recurso de um recurso pai na mesma assinatura e grupo de recursos, forneça o tipo e o nome do recurso.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Para obter a ID de recurso de um recurso filho, preste atenção ao número de segmentos no tipo de recurso. Forneça um nome de recurso para cada segmento do tipo de recurso. O nome do segmento corresponde ao recurso que existe para essa parte da hierarquia.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Para obter a ID de recurso de um recurso na mesma assinatura, mas em um grupo de recursos diferente, forneça o nome do grupo de recursos.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Para obter a ID de recurso de um recurso em uma assinatura e um grupo de recursos diferentes, forneça a ID da assinatura e o nome do grupo de recursos.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Frequentemente, você precisa usar essa função ao usar uma conta de armazenamento ou rede virtual em um grupo de recursos alternativo. O exemplo a seguir mostra como um recurso de um grupo de recursos externo pode ser facilmente usado:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Exemplo de ID de recurso

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) a seguir retorna a ID de recurso de uma conta de armazenamento no grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| name | Type | Value |
| ---- | ---- | ----- |
| sameRGOutput | string | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | string | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | string | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | string | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscription

```json
subscription()
```

Retorna detalhes sobre a assinatura da implantação atual. 

### <a name="return-value"></a>Valor de retorno

A função retorna o seguinte formato:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Exemplo de assinatura

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) a seguir mostra a função de assinatura chamada na seção de saídas. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Retorna o identificador exclusivo de um recurso implantado no nível da assinatura.

### <a name="parameters"></a>parâmetros

| . | obrigatórios | Type | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |Cadeia de caracteres (no formato GUID) |O valor padrão é a assinatura atual. Especifique esse valor quando você precisar recuperar um recurso em outra assinatura. |
| resourceType |SIM |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |SIM |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

O identificador é retornado no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Comentários

Use essa função para obter a ID do recurso para os recursos que são [implantados na assinatura](deploy-to-subscription.md) em vez de um grupo de recursos. A ID retornada difere do valor retornado pela função [ResourceId](#resourceid) , não incluindo um valor de grupo de recursos.

### <a name="subscriptionresourceid-example"></a>exemplo de subscriptionResourceID

O modelo a seguir atribui uma função interna. Você pode implantá-lo em um grupo de recursos ou assinatura. Ele usa a função subscriptionResourceId para obter a ID de recurso para funções internas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Retorna o identificador exclusivo de um recurso implantado no nível do locatário.

### <a name="parameters"></a>parâmetros

| . | obrigatórios | Type | Descrição |
|:--- |:--- |:--- |:--- |
| resourceType |SIM |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |SIM |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor de retorno

O identificador é retornado no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Comentários

Você usa essa função para obter a ID de recurso para um recurso que é implantado no locatário. A ID retornada difere dos valores retornados por outras funções de ID de recurso, não incluindo os valores de grupo de recursos ou de assinatura.

## <a name="next-steps"></a>Próximos passos

* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, consulte [Implantar um aplicativo com o Modelo do Azure Resource Manager](resource-group-template-deploy.md).

