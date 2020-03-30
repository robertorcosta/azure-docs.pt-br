---
title: 'Crie uma conexão de dados do IoT Hub para o Azure Data Explorer usando C #'
description: Neste artigo, você aprende como criar uma conexão de dados IoT Hub para o Azure Data Explorer usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667377"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Crie uma conexão de dados do IoT Hub para o Azure Data Explorer usando C# (Preview)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modelo de Gerenciador de recursos do Azure](data-connection-iot-hub-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão (carregamento de dados) de Hubs de Eventos, Hubs ioT e blobs escritos para recipientes blob. Neste artigo, você cria uma conexão de dados IoT Hub para o Azure Data Explorer usando C#.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tem o Visual 2019 Studio instalado, baixe e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Certifique-se de que você **habilite o desenvolvimento do Azure** durante a configuração do Visual Studio.
* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Crie [um cluster e um banco de dados](create-cluster-database-csharp.md)
* Criar [mapeamento de tabelas e colunas](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Definir [políticas de banco de dados e tabelas](database-table-policies-csharp.md) (opcional)
* Crie um [Hub ioT com uma política de acesso compartilhado configurada](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Adicione uma conexão de dados do IoT Hub 

O exemplo a seguir mostra como adicionar uma conexão de dados IoT Hub programáticamente. Consulte [conectar a tabela Do Azure Data Explorer ao IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) para adicionar uma conexão de dados Iot Hub usando o portal Azure.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Configuração** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecido como ID do diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID de assinatura que você usa para criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| clientSecret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu inquilino. |
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém seu cluster.|
| clusterName | *mykustocluster* | O nome do seu grupo.|
| databaseName | *mykustodatabase* | O nome do banco de dados de destino em seu cluster.|
| dataConexãoNome | *myeventhubconnect* | O nome desejado de sua conexão de dados.|
| tableName | *Eventos de Tempestade* | O nome da tabela de destino no banco de dados de alvos.|
| mapeamentoRuleName | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado à tabela de destino.|
| Dataformat | *Csv* | O formato de dados da mensagem.|
| iotHubResourceId | *ID de recursos* | O ID de recurso do seu hub de IoT que contém os dados para ingestão. |
| compartilhadoAccessPolicyName | *iothubforread* | O nome da política de acesso compartilhado que define as permissões para dispositivos e serviços se conectarem ao IoT Hub. |
| consumerGroup | *$Default* | O grupo de consumidores do seu hub de eventos.|
| local | *Centro dos EUA* | A localização do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
