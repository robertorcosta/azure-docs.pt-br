---
title: Criar um cluster e um banco de dados do Azure Data Explorer usando Python
description: Saiba como criar um cluster e um banco de dados do Azure Data Explorer usando o Python.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b3329ccb3edb3077a45e3bbf9ba7b48d7e3a93a2
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996238"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Criar um cluster e um banco de dados do Azure Data Explorer usando Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo de ARM](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando o Python.

## <a name="prerequisites"></a>Pré-requisitos

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="install-python-package"></a>Instalar o pacote do Python

Para instalar o pacote Python para o Azure Data Explorer (Kusto), abra um prompt de comando que tenha Python no caminho. Execute este comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, precisamos de um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos. Marque [criar um aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar um aplicativo gratuito do Azure AD e adicionar a atribuição de função no escopo da assinatura. Ele também mostra como obter o `Directory (tenant) ID`, `Application ID` e `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o seguinte comando:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome desejado do cluster.|
   | sku_name | *Standard_D13_v2* | O SKU que será usado para o cluster. |
   | Camada | *Standard* | A camada de SKU. |
   | recurso | *number* | O número de instâncias do cluster. |
   | resource_group_name | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    > [!NOTE]
    > **Criar um cluster** é uma operação de execução longa. O método **create_or_update** retorna uma instância de LROPoller, consulte [classe LROPoller](/python/api/msrest/msrest.polling.lropoller?view=azure-python) para obter mais informações.

1. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Se o resultado contém `provisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o seguinte comando:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | database_name | *mykustodatabase* | O nome do banco de dados.|
   | resource_group_name | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | soft_delete_period | *3650 dias, 0:00:00* | O tempo durante o qual os dados serão mantidos disponíveis para consulta. |
   | hot_cache_period | *3650 dias, 0:00:00* | O tempo durante o qual os dados serão mantidos no cache. |

1. Execute o seguinte comando para ver o banco de dados criado:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Próximas etapas

* [Ingerir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md)
