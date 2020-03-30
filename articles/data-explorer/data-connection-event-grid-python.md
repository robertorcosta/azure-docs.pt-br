---
title: Crie uma conexão de dados event grid para o Azure Data Explorer usando python
description: Neste artigo, você aprende como criar uma conexão de dados event grid para o Azure Data Explorer usando Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444189"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Crie uma conexão de dados event grid para o Azure Data Explorer usando python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modelo de Gerenciador de recursos do Azure](data-connection-event-grid-resource-manager.md)

Neste artigo, você cria uma conexão de dados Event Grid para o Azure Data Explorer usando Python. O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão, ou carregamento de dados, de Hubs de Eventos, Hubs de IoT e blobs escritos para recipientes blob.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Um cluster e um banco de dados.](create-cluster-database-python.md)

* [Mapeamento de tabelas e colunas](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Políticas de banco de dados e tabelas](database-table-policies-csharp.md) (opcional).

* [Uma conta de armazenamento com uma assinatura event grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Adicione uma conexão de dados da Event Grid

O exemplo a seguir mostra como adicionar uma conexão de dados event grid programáticamente. Consulte [criar uma conexão de dados event grid no Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) para adicionar uma conexão de dados event grid usando o portal Azure.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Configuração** | **Valor sugerido** | **Descrição do campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecido como ID do diretório.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID de assinatura que você usa para criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | O ID do cliente do aplicativo que pode acessar recursos em seu inquilino.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu inquilino. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém seu cluster.|
| cluster_name | *mykustocluster* | O nome do seu grupo.|
| database_name | *mykustodatabase* | O nome do banco de dados de destino em seu cluster.|
| data_connection_name | *myeventhubconnect* | O nome desejado de sua conexão de dados.|
| table_name | *Eventos de Tempestade* | O nome da tabela de destino no banco de dados de alvos.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | O nome do mapeamento da coluna relacionado à tabela de destino.|
| data_format | *Csv* | O formato de dados da mensagem.|
| event_hub_resource_id | *ID de recursos* | O ID de recurso do seu Event Hub onde a Grade de Eventos está configurada para enviar eventos. |
| storage_account_resource_id | *ID de recursos* | O ID de recurso da sua conta de armazenamento que contém os dados para ingestão. |
| consumer_group | *$Default* | O grupo de consumidores do seu Event Hub.|
| local | *Centro dos EUA* | A localização do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]