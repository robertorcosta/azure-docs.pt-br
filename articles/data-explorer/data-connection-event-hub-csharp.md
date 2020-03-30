---
title: 'Crie uma conexão de dados do Event Hub para o Azure Data Explorer usando C #'
description: Neste artigo, você aprende como criar uma conexão de dados do Event Hub para o Azure Data Explorer usando C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667688"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Crie uma conexão de dados do Event Hub para o Azure Data Explorer usando C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modelo de Gerenciador de recursos do Azure](data-connection-event-hub-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão (carregamento de dados) de Hubs de Eventos, Hubs ioT e blobs escritos para recipientes blob. Neste artigo, você cria uma conexão de dados do Event Hub para o Azure Data Explorer usando C#.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tem o Visual 2019 Studio instalado, baixe e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Certifique-se de que você **habilite o desenvolvimento do Azure** durante a configuração do Visual Studio.
* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Crie [um cluster e um banco de dados](create-cluster-database-csharp.md)
* Criar [mapeamento de tabelas e colunas](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Definir [políticas de banco de dados e tabelas](database-table-policies-csharp.md) (opcional)
* Crie um [Event Hub com dados para ingestão.](ingest-data-event-hub.md#create-an-event-hub) 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Adicione uma conexão de dados do Event Hub

O exemplo a seguir mostra como adicionar uma conexão de dados do Event Hub de forma programática. Consulte [conectar-se ao centro de eventos](ingest-data-event-hub.md#connect-to-the-event-hub) para adicionar uma conexão de dados do Event Hub usando o portal Azure.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Configuração** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecido como ID do diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID de assinatura que você usa para criação de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| clientSecret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| resourceGroupName | *testrg* | O nome do grupo de recursos que contém seu cluster.|
| clusterName | *mykustocluster* | O nome do seu grupo.|
| databaseName | *mykustodatabase* | O nome do banco de dados de destino em seu cluster.|
| dataConexãoNome | *myeventhubconnect* | O nome desejado de sua conexão de dados.|
| tableName | *Eventos de Tempestade* | O nome da tabela de destino no banco de dados de alvos.|
| mapeamentoRuleName | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado à tabela de destino.|
| Dataformat | *Csv* | O formato de dados da mensagem.|
| eventHubResourceId | *ID de recursos* | O ID de recurso do seu Event Hub que contém os dados para ingestão. |
| consumerGroup | *$Default* | O grupo de consumidores do seu Event Hub.|
| local | *Centro dos EUA* | A localização do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
