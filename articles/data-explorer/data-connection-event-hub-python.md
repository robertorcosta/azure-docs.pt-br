---
title: Crie uma conexão de dados do Event Hub para o Azure Data Explorer usando o Python
description: Neste artigo, você aprende como criar uma conexão de dados do Event Hub para o Azure Data Explorer usando python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c08271286373ab8c3e621ee6fa59782ba2d16fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444172"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Crie uma conexão de dados do Event Hub para o Azure Data Explorer usando o Python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modelo de Gerenciador de recursos do Azure](data-connection-event-hub-resource-manager.md)

Neste artigo, você cria uma conexão de dados do Event Hub para o Azure Data Explorer usando python. O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão, ou carregamento de dados, de Hubs de Eventos, Hubs de IoT e blobs escritos para recipientes blob.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Um cluster e um banco de dados.](create-cluster-database-python.md)

* [Mapeamento de tabelas e colunas](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Políticas de banco de dados e tabelas](database-table-policies-python.md) (opcional).

* [Hub de eventos com dados para ingestão.](ingest-data-event-hub.md#create-an-event-hub)

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Adicione uma conexão de dados do Event Hub

O exemplo a seguir mostra como adicionar uma conexão de dados do Event Hub de forma programática. Consulte [conectar-se ao centro de eventos](ingest-data-event-hub.md#connect-to-the-event-hub) para adicionar uma conexão de dados do Event Hub usando o portal Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Configuração** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecido como ID do diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID de assinatura que você usa para criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu inquilino. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu grupo.|
| database_name | *mykustodatabase* | O nome do banco de dados de destino em seu cluster.|
| data_connection_name | *myeventhubconnect* | O nome desejado de sua conexão de dados.|
| table_name | *Eventos de Tempestade* | O nome da tabela de destino no banco de dados de alvos.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado à tabela de destino.|
| data_format | *Csv* | O formato de dados da mensagem.|
| event_hub_resource_id | *ID de recursos* | O ID de recurso do seu Event Hub que contém os dados para ingestão. |
| consumer_group | *$Default* | O grupo de consumidores do seu Event Hub.|
| local | *Centro dos EUA* | A localização do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]