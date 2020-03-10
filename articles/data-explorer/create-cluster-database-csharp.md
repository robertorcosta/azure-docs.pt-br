---
title: Criar um cluster de Data Explorer do Azure & DB usandoC#
description: Saiba como criar um cluster e banco de dados do Azure Data Explorer usando C#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0c32d438ac8551f061343edb747e9fc035b498e2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379849"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Criar um cluster e banco de dados do Azure Data Explorer usando C#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo do Azure Resource Manager](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. Neste artigo, você cria um cluster e um banco de dados usando C#o.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Se você não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gratuito. Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.
* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Autenticação
Para executar os exemplos neste artigo, precisamos de um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos. Marque [criar um aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para criar um aplicativo gratuito do Azure AD e adicionar a atribuição de função no escopo da assinatura. Ele também mostra como obter as `Directory (tenant) ID`, `Application ID`e `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o código a seguir:

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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome desejado do cluster.|
   | skuName | *Standard_D13_v2* | O SKU que será usado para o cluster. |
   | camada | *Standard* | A camada de SKU. |
   | ALOCADA | *number* | O número de instâncias do cluster. |
   | resourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    > [!NOTE]
    > **Criar um cluster** é uma operação de execução demorada, portanto, é altamente recomendável usar CreateOrUpdateAsync, em vez de CreateOrUpdate. 

1. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Se o resultado contém `ProvisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o código a seguir:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | databaseName | *mykustodatabase* | O nome do banco de dados.|
   | resourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | softDeletePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos disponíveis para consulta. |
   | hotCachePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos no cache. |

2. Execute o seguinte comando para ver o banco de dados criado:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

* Se você planeja seguir nossos outros artigos, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Ingerir dados usando o SDK do .NET Standard no Azure Data Explorer (Versão prévia)](net-standard-ingest-data.md)
