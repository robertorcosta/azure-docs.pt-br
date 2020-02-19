---
title: Criar um cluster e um banco de dados do Azure Data Explorer usando Python
description: Saiba como criar um cluster e um banco de dados do Azure Data Explorer usando o Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 50e050a05fd364a4b1f880e3501b04274ffd360c
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444223"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Criar um cluster e um banco de dados do Azure Data Explorer usando Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo de ARM](create-cluster-database-resource-manager.md)

Neste artigo, você cria um cluster de Data Explorer do Azure e um banco de dados usando Python. O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Data Explorer do Azure, primeiro crie um cluster e crie um ou mais bancos de dados nesse cluster. Em seguida, ingerir, ou carregar, dados em um banco de dado para que você possa executar consultas nele.

## <a name="prerequisites"></a>Prerequisites

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4+](https://www.python.org/downloads/).

* [Um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Obter valores para `Directory (tenant) ID`, `Application ID`e `Client Secret`.

## <a name="install-python-package"></a>Instalar o pacote do Python

Para instalar o pacote Python para o Azure Data Explorer (Kusto), abra um prompt de comando que tenha Python no caminho. Execute este comando:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, precisamos de um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos. Marque [criar um aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar um aplicativo gratuito do Azure AD e adicionar a atribuição de função no escopo da assinatura. Ele também mostra como obter as `Directory (tenant) ID`, `Application ID`e `Client Secret`.

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
   | ALOCADA | *number* | O número de instâncias do cluster. |
   | resource_group_name | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    > [!NOTE]
    > **Criar um cluster** é uma operação de execução longa. O método **create_or_update** retorna uma instância de LROPoller, consulte a [classe LROPoller](/python/api/msrest/msrest.polling.lropoller?view=azure-python) para obter mais informações.

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
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

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

## <a name="clean-up-resources"></a>Limpar os recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Próximas etapas

* [Ingerir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md)
