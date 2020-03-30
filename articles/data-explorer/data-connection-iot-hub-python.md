---
title: Crie uma conexão de dados do IoT Hub para o Azure Data Explorer usando python
description: Neste artigo, você aprende como criar uma conexão de dados IoT Hub para o Azure Data Explorer usando python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 76c8ca24882f465bf2a973dc59736745178fc61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669515"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Crie uma conexão de dados do IoT Hub para o Azure Data Explorer usando python (Preview)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modelo de Gerenciador de recursos do Azure](data-connection-iot-hub-resource-manager.md)

Neste artigo, você cria uma conexão de dados IoT Hub para o Azure Data Explorer usando Python. O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão, ou carregamento de dados, de Hubs de Eventos, Hubs de IoT e blobs escritos para recipientes blob.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Um cluster e um banco de dados.](create-cluster-database-python.md)

* [Mapeamento de tabelas e colunas](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Políticas de banco de dados e tabelas](database-table-policies-python.md) (opcional).

* [Um IoT Hub com uma política de acesso compartilhado configurada](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Adicione uma conexão de dados do IoT Hub 

O exemplo a seguir mostra como adicionar uma conexão de dados IoT Hub programáticamente. Consulte [conectar a tabela Do Azure Data Explorer ao IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) para adicionar uma conexão de dados Iot Hub usando o portal Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
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
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
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
| iot_hub_resource_id | *ID de recursos* | O ID de recurso do seu hub de IoT que contém os dados para ingestão.|
| shared_access_policy_name | *iothubforread* | O nome da política de acesso compartilhado que define as permissões para dispositivos e serviços se conectarem ao IoT Hub. |
| consumer_group | *$Default* | O grupo de consumidores do seu hub de eventos.|
| local | *Centro dos EUA* | A localização do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]