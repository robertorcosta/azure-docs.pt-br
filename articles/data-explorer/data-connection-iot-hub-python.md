---
title: Criar uma conexão de dados do Hub IoT para o Azure Data Explorer usando o Python
description: Neste artigo, você aprenderá a criar uma conexão de dados do Hub IoT para o Azure Data Explorer usando o Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 76c8ca24882f465bf2a973dc59736745178fc61f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669515"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Criar uma conexão de dados do Hub IoT para o Azure Data Explorer usando Python (versão prévia)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modelo do Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

Neste artigo, você cria uma conexão de dados do Hub IoT para o Azure Data Explorer usando o Python. O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão ou carregamento de dados, de hubs de eventos, hubs IoT e Blobs gravados em contêineres de BLOB.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Um cluster e um banco de dados](create-cluster-database-python.md).

* [Mapeamento de tabela e coluna](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Políticas de banco de dados e tabela](database-table-policies-python.md) (opcional).

* [Um hub IOT com uma política de acesso compartilhado configurada](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Adicionar uma conexão de dados do Hub IoT 

O exemplo a seguir mostra como adicionar uma conexão de dados do Hub IoT programaticamente. Consulte [conectar a tabela de data Explorer do Azure ao Hub IOT](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) para adicionar uma conexão de dados do Hub IOT usando o portal do Azure.

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
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID do locatário. Também conhecida como ID de diretório.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID da assinatura que você usa para a criação de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A ID do cliente do aplicativo que pode acessar recursos em seu locatário.|
| client_secret | *xxxxxxxxxxxxxx* | O segredo do cliente do aplicativo que pode acessar recursos em seu locatário. |
| resource_group_name | *testrg* | O nome do grupo de recursos que contém o cluster.|
| cluster_name | *mykustocluster* | O nome do cluster.|
| database_name | *mykustodatabase* | O nome do banco de dados de destino no cluster.|
| data_connection_name | *myeventhubconnect* | O nome desejado da sua conexão de dados.|
| table_name | *StormEvents* | O nome da tabela de destino no banco de dados de destino.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | O nome do mapeamento de coluna relacionado à tabela de destino.|
| data_format | *CSV* | O formato de dados da mensagem.|
| iot_hub_resource_id | *ID do recurso* | A ID de recurso do Hub IoT que contém os dados para ingestão.|
| shared_access_policy_name | *iothubforread* | O nome da política de acesso compartilhado que define as permissões para dispositivos e serviços para se conectar ao Hub IoT. |
| consumer_group | *$Default* | O grupo de consumidores do hub de eventos.|
| local | *Centro dos EUA* | O local do recurso de conexão de dados.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]