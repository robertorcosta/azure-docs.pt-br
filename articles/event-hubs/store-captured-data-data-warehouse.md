---
title: 'Tutorial: Migrar dados de evento para o SQL Data Warehouse – Hubs de Eventos do Azure'
description: 'Tutorial: Este tutorial mostra como capturar dados do hub de eventos em um SQL data warehouse usando uma função do Azure disparada por uma grade de eventos.'
services: event-hubs
ms.date: 06/23/2020
ms.topic: tutorial
ms.openlocfilehash: 1a7030014a8926b1db11ad99ba031e51194ddfd9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833684"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Tutorial: Migrar dados dos Hubs de Eventos capturados para um SQL Data Warehouse usando a Grade de Eventos e o Azure Functions

A [captura](./event-hubs-capture-overview.md) dos Hubs de Eventos é a maneira mais fácil de enviar dados transmitidos nos Hubs de Eventos automaticamente para um Armazenamento de Blobs do Azure ou para um Azure Data Lake Store. Posteriormente, você pode processar e enviar os dados para outros destinos de armazenamento de sua escolha, como o SQL Data Warehouse ou o Cosmos DB. Neste tutorial, você aprenderá a capturar dados do hub de eventos em um SQL data warehouse usando uma função do Azure disparada por uma [grade de eventos](../event-grid/overview.md).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

- Primeiro, crie um hub de eventos com o recurso **Capturar** habilitado e defina um armazenamento de blobs do Azure como destino. Os dados gerados pelo WindTurbineGenerator são transmitidos para o hub de eventos e são capturados automaticamente no Armazenamento do Azure como arquivos Avro.
- Em seguida, você cria uma assinatura da Grade de Eventos do Azure com o namespace dos Hubs de Eventos como sua fonte e o ponto de extremidade da função do Azure como seu destino.
- Sempre que um novo arquivo Avro é enviado para o blob do Armazenamento do Azure pelo recurso Captura dos Hubs de Eventos, a Grade de Eventos notifica a função do Azure com o URI do blob. A função, em seguida, migra dados do blob para um SQL data warehouse.

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
>
> - Implantar a infraestrutura
> - Publicar o código em um aplicativo do Functions
> - Criar uma assinatura da Grade de Eventos a partir do aplicativo de funções
> - Transmitir dados de exemplo ao Hub de Eventos.
> - Verificar dados capturados em um SQL Data Warehouse

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual Studio 2019](https://www.visualstudio.com/vs/). Durante a instalação, instale as cargas de trabalho a seguir: desenvolvimento para desktop com .NET, desenvolvimento do Azure, ASP.NET e desenvolvimento Web, desenvolvimento em Node.js e desenvolvimento em Python
- Baixe o [exemplo do Git](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo) A solução de exemplo contém os seguintes componentes:

  - *WindTurbineDataGenerator*: um editor simples que envia dados de exemplo de turbina eólica para um hub de eventos habilitado para captura
  - *FunctionDWDumper*: uma função do Azure que recebe uma notificação da Grade de Eventos quando um arquivo Avro é capturado no blob do Armazenamento do Azure. Ela recebe o caminho do URI do blob, lê seu conteúdo e envia esses dados para um SQL Data Warehouse.

  Este exemplo usa o pacote mais recente Azure.Messaging.EventHubs. Você pode encontrar o exemplo antigo que usa o pacote Microsoft.Azure.EventHubs [aqui](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

### <a name="deploy-the-infrastructure"></a>Implantar a infraestrutura

Use o Azure PowerShell ou a CLI do Azure para implantar a infraestrutura necessária para o tutorial usando esse [modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Este modelo cria os seguintes recursos:

- Hub de Eventos com o recurso Captura habilitado
- Conta de armazenamento para os dados de evento capturados
- Plano do Serviço de Aplicativo do Azure para hospedar o aplicativo de funções
- Aplicativo de funções para processar arquivos de eventos capturados
- Servidor SQL lógico para hospedar o Data Warehouse
- SQL Data Warehouse para armazenamento dos dados migrados

As seções a seguir fornecem comandos da CLI do Azure e do Azure PowerShell a fim de implantar a infraestrutura necessária para o tutorial. Atualize os nomes dos objetos abaixo antes de executar os comandos: 

- Grupo de recursos do Azure 
- Região do grupo de recursos
- Namespace do Hubs de Eventos
- Hub de Eventos
- Servidor SQL lógico
- Usuário do SQL (e senha)
- Banco de Dados SQL do Azure
- Armazenamento do Azure 
- Aplicativo do Azure Functions

Esses scripts levam algum tempo para criar todos os artefatos do Azure. Aguarde até que o script seja concluído antes de continuar. Se a implantação falhar por algum motivo, exclua o grupo de recursos, corrija o problema relatado e execute o comando novamente. 

#### <a name="azure-cli"></a>CLI do Azure

Para implantar o modelo usando a CLI do Azure, use os seguintes comandos:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create `
  --resource-group rgDataMigrationSample `
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json `
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Para implantar o modelo usando o PowerShell, use os seguintes comandos:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

### <a name="create-a-table-in-sql-data-warehouse"></a>Criar uma tabela no SQL Data Warehouse

Crie uma tabela no SQL data warehouse, executando o script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) usando o [Visual Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), o [SQL Server Management Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-ssms.md) ou o Editor de Consulta no portal. 

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-code-to-the-functions-app"></a>Publicar código para o aplicativo do Functions

1. Abra a solução *EventHubsCaptureEventGridDemo.sln* no Visual Studio 2019.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em *FunctionEGDWDumper* e selecione **Publicar**.

   ![Publicar o aplicativo de funções](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Selecione **Aplicativo Azure Function** e **Selecionar Existente**. Selecione **Publicar**.

   ![Destinar aplicativo de funções](./media/store-captured-data-data-warehouse/pick-target.png)

1. Selecione o aplicativo de funções implantado por meio do modelo. Selecione **OK**.

   ![Selecionar aplicativo de funções](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecionar Publicar](./media/store-captured-data-data-warehouse/select-publish.png)

Depois de publicar a função, você está pronto para assinar o evento de captura dos Hubs de Eventos!


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Criar uma assinatura da Grade de Eventos a partir do aplicativo de funções
 
1. Vá para o [Portal do Azure](https://portal.azure.com/). Selecione o grupo de recursos e o aplicativo de funções.

   ![Exibir aplicativo de funções](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Selecione a função.

   ![Selecionar função](./media/store-captured-data-data-warehouse/select-function.png)

1. Selecione **Adicionar assinatura da Grade de Eventos**.

   ![Adicionar assinatura](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Nomeie a assinatura de grade de eventos. Use **Namespaces de Hubs de Evento** como o tipo de evento. Forneça valores para selecionar a instância do namespace de Hubs de Eventos. Deixe o ponto de extremidade do assinante como o valor fornecido. Selecione **Criar**.

   ![Criar assinatura](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Gerar dados de exemplo  
Você configurou o Hub de Eventos, o SQL Data Warehouse, o Aplicativo de funções do Azure e a assinatura de da Grade de Eventos. Você pode executar WindTurbineDataGenerator.exe para gerar fluxos de dados para o Hub de Eventos depois de atualizar a cadeia de conexão e o nome do hub de eventos no código-fonte. 

1. No portal, selecione seu namespace do hub de eventos. Selecione **Cadeias de Conexão**.

   ![Selecionar cadeias de conexão](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Selecione **RootManageSharedAccessKey**

   ![Selecionar a chave](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Copie **Cadeia de conexão – Chave primária**

   ![Copiar chave](./media/store-captured-data-data-warehouse/copy-key.png)

4. Volte ao seu projeto do Visual Studio. No projeto *WindTurbineDataGenerator*, abra *program.cs*.

5. Atualize os valores de **EventHubConnectionString** e **EventHubName** com a cadeia de conexão e o nome do hub de eventos. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compile a solução e execute o aplicativo WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Verificar dados capturados no data warehouse
Depois de alguns minutos, consulte a tabela no SQL data warehouse. Observe que os dados gerados pelo WindTurbineDataGenerator foram transmitidos ao Hub de Eventos, capturados em um contêiner do Armazenamento do Azure e migrados para a tabela do SQL Data Warehouse pela função do Azure.  

## <a name="next-steps"></a>Próximas etapas 
Você pode usar as ferramentas de visualização de dados avançada com o data warehouse para obter insights acionáveis.

Este artigo mostra como usar o [Power BI com o SQL Data Warehouse](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)