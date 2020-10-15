---
title: 'Tutorial: Enviar dados de Hubs de Eventos para o data warehouse – Grade de Eventos'
description: 'Tutorial: Descreve como usar a Grade de Eventos do Azure e os Hubs de Eventos para migrar dados para um Azure Synapse Analytics. Ele usa uma Função do Azure para recuperar um arquivo de Captura.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 64d4b9769e1a228294bd7d8741f6f4b1260fb0dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91270540"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Transmitir Big Data para um data warehouse
A [Grade de Eventos](overview.md) do Azure é um serviço de roteamento de evento inteligente que permite que você reaja às notificações (eventos) de aplicativos e serviços. Por exemplo, ele pode disparar uma função do Azure para processar dados de Hubs de Eventos que foram capturados em um Armazenamento de Blobs do Azure ou Azure Data Lake Storage e migrar os dados para outros repositórios de dados. Este [Exemplo de integração de Hubs de Eventos e Grade de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra como usar os Hubs de Eventos com a Grade de Eventos para migrar com perfeição dados dos Hubs de Eventos capturados do armazenamento de blobs para um Azure Synapse Analytics (anteriormente conhecido como SQL Data Warehouse).

![Visão geral do aplicativo](media/event-grid-event-hubs-integration/overview.png)

Este diagrama ilustra o fluxo de trabalho da solução que você cria neste tutorial: 

1. Dados enviados para um hub de eventos do Azure são capturados no Armazenamento de Blobs do Azure.
2. Quando a captura de dados for concluída, um evento será gerado e enviado para uma Grade de Eventos do Azure. 
3. A grade de eventos encaminha esses dados de evento para um aplicativo de funções do Azure.
4. O aplicativo de funções usa a URL do blob nos dados do evento para recuperar o blob do armazenamento. 
5. O aplicativo de funções migra os dados de blob para um Azure Synapse Analytics. 

Neste artigo, você executa as seguintes etapas:

> [!div class="checklist"]
> * Usar um modelo do Azure Resource Manager para implantar a infraestrutura: um hub de eventos, uma conta de armazenamento, um aplicativo de funções, um Synapse Analytics.
> * Criar uma tabela no data warehouse.
> * Adiciona código ao aplicativo de funções.
> * Assinar o evento. 
> * Execute o aplicativo que envia dados para o hub de eventos.
> * Exiba os dados migrados no data warehouse.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, você deve ter:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2019](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de área de trabalho do .NET, desenvolvimento do Azure, ASP.NET e desenvolvimento para a Web, desenvolvimento em Node.js e desenvolvimento do Python.
* Baixe o [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) para seu computador.

## <a name="deploy-the-infrastructure"></a>Implantar a infraestrutura
Nesta etapa, você implanta a infraestrutura necessária com um [modelo do Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando você implanta o modelo, são criados os seguintes recursos:

* Hub de Eventos com o recurso Captura habilitado.
* Conta de armazenamento para os arquivos capturados. 
* Plano de serviço de aplicativo para hospedar o aplicativo de função
* Aplicativo de funções para o processamento do evento
* SQL Server para hospedagem do data warehouse
* Azure Synapse Analytics para armazenar os dados migrados

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Inicialize o Azure Cloud Shell no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Selecione o botão **Cloud Shell** na parte superior.

    ![Portal do Azure](media/event-grid-event-hubs-integration/azure-portal.png)
3. Você verá que o Cloud Shell será aberto na parte inferior do navegador.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. No Cloud Shell, se você vir uma opção para selecionar entre **Bash** e **PowerShell**, selecione **Bash**. 
5. Se você estiver usando o Cloud Shell pela primeira vez, crie uma conta de armazenamento selecionando **Criar armazenamento**. O Azure Cloud Shell requer uma conta de armazenamento do Azure para armazenar alguns arquivos. 

    ![Captura de tela que mostra a caixa de diálogo "Você não tem nenhum armazenamento montado" com o botão "Criar armazenamento" selecionado.](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Aguarde até o Cloud Shell ser inicializado. 

    ![Criar armazenamento para o Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Usar a CLI do Azure

1. Crie um grupo de recursos do Azure executando o seguinte comando da CLI: 
    1. Copie e cole o seguinte comando na janela do Cloud Shell. Altere o nome e a localização do grupo de recursos, se desejar.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Pressione **ENTER**. 

        Veja um exemplo:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Implante todos os recursos mencionados na seção anterior (hub de eventos, conta de armazenamento, aplicativo de funções, o Azure Synapse Analytics) executando o seguinte comando da CLI: 
    1. Copie e cole o comando na janela do Cloud Shell. Como alternativa, talvez você queira copiar/colar em um editor de sua escolha, definir valores e, em seguida, copiar o comando para o Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Especifique valores para as seguintes entidades:
        1. Nome do grupo de recursos criado anteriormente.
        2. Nome para o namespace do hub de eventos. 
        3. Nome do hub de eventos. Você pode deixar o valor como está (hubdatamigration).
        4. Nome para o SQL Server.
        5. Nome do usuário do SQL e senha. 
        6. Nome para o Azure Synapse Analytics
        7. Nome da conta de armazenamento. 
        8. Nome do aplicativo de funções. 
    3.  Pressione **ENTER** na janela do Cloud Shell para executar o comando. Esse processo pode demorar um pouco, pois você está criando diversos recursos. No resultado do comando, verifique se não houve falhas. 
    

### <a name="use-azure-powershell"></a>Usar PowerShell do Azure

1. No Azure Cloud Shell, alterne para modo do PowerShell. Selecione a seta no canto superior esquerdo do Azure Cloud Shell para baixo e selecione **PowerShell**.

    ![Alternar para o PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Crie um grupo de recursos do Azure executando o seguinte comando: 
    1. Copie e cole o seguinte comando na janela do Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. Especifique um nome para um novo **grupo de recursos**.
    3. Pressione ENTER. 
3. Implante todos os recursos mencionados na seção anterior (hub de eventos, conta de armazenamento, aplicativo de funções, o Azure Synapse Analytics) executando o seguinte comando:
    1. Copie e cole o comando na janela do Cloud Shell. Como alternativa, talvez você queira copiar/colar em um editor de sua escolha, definir valores e, em seguida, copiar o comando para o Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Especifique valores para as seguintes entidades:
        1. Nome do grupo de recursos criado anteriormente.
        2. Nome para o namespace do hub de eventos. 
        3. Nome do hub de eventos. Você pode deixar o valor como está (hubdatamigration).
        4. Nome para o SQL Server.
        5. Nome do usuário do SQL e senha. 
        6. Nome para o Azure Synapse Analytics
        7. Nome da conta de armazenamento. 
        8. Nome do aplicativo de funções. 
    3.  Pressione **ENTER** na janela do Cloud Shell para executar o comando. Esse processo pode demorar um pouco, pois você está criando diversos recursos. No resultado do comando, verifique se não houve falhas. 

### <a name="close-the-cloud-shell"></a>Fechar o Cloud Shell 
Feche o Cloud Shell selecionando o botão **Cloud Shell** no portal (ou) o botão **X** no canto superior direito da janela do Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verifique se os recursos são criados

1. No portal do Azure, selecione **Grupos de recursos** no menu esquerdo. 
2. Filtre a lista de grupos de recursos digitando o nome do seu grupo de recursos na caixa de pesquisa. 
3. Selecione seu grupo de recursos na lista.

    ![Selecione o grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Confirme que você vê os seguintes recursos no grupo de recursos:

    ![Recursos no grupo de recursos](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>Criar uma tabela no Azure Synapse Analytics
Crie uma tabela no data warehouse executando o script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Para executar o script, você pode usar o Visual Studio ou o Editor de Consultas no portal. As etapas a seguir mostram como usar o Editor de Consultas: 

1. Na lista de recursos no grupo de recursos, selecione o seu **pool de SQL do Synapse (data warehouse)** . 
2. Na página do Azure Synapse Analytics, selecione **Editor de consultas (versão prévia)** no menu à esquerda. 

    ![Página do Azure Synapse Analytics](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Insira o nome de **usuário** e **senha** para o SQL Server e selecione **OK**. Talvez você precise adicionar seu endereço IP de cliente ao firewall para fazer logon com êxito no SQL Server. 

    ![Autenticação do SQL Server](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. Na janela de consulta, copie e execute o seguinte script SQL: 

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

    ![Executar uma consulta SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Mantenha essa guia ou janela aberta para que você possa verificar se os dados são criados no final do tutorial. 

### <a name="update-the-function-runtime-version"></a>Atualizar a versão de runtime da função

1. No portal do Azure, selecione **Grupos de recursos** no menu esquerdo.
2. Selecione o grupo de recursos no qual o aplicativo de funções está localizado. 
3. Selecione o aplicativo de funções do tipo **Serviço de Aplicativo** na lista de recursos no grupo de recursos.
4. Selecione **Configuração** em **Configurações** no menu à esquerda. 
5. Alterne para a guia **Configurações de runtime da função** no painel direito. 
5. Atualize a **versão de runtime** para **~3**. 

    ![Atualizar a versão de runtime da função](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>Publicar o aplicativo do Azure Functions

1. Inicie o Visual Studio.
2. Abra a solução **EventHubsCaptureEventGridDemo.sln** que você baixou do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte dos pré-requisitos.
3. No Gerenciador de Soluções, clique com o botão direito do mouse em **FunctionEGDWDumper** e selecione **Publicar**.

   ![Publicar o aplicativo de funções](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Se você vir a tela a seguir, selecione **Iniciar**. 

   ![Captura de tela que mostra o Visual Studio com o botão "Iniciar" na seção Publicar.](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Na caixa de diálogo **Publicar**, selecione **Azure** como **Destino** e **Avançar**. 

   ![Botão Iniciar publicação](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. Selecione **Aplicativo de Funções do Azure (Windows)** e **Avançar**. 

   ![Selecionar Aplicativo de Funções do Azure (Windows)](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. Na guia **Instância do Functions**, selecione sua assinatura do Azure, expanda o grupo de recursos, escolha o aplicativo de funções e selecione **Concluir**. Faça logon na sua conta do Azure se ainda não fez isso. 

   ![Selecione seu aplicativo de funções](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. Na seção **Dependências do Serviço**, selecione **Configurar**.
9. Na página **Configurar dependência**, selecione a conta de armazenamento que você criou anteriormente e escolha **Avançar**. 
10. Mantenha as configurações de nome e valor da cadeia de conexão e escolha **Avançar**.
11. Desmarque a opção **Repositório de segredos** e selecione **Concluir**.  
8. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecionar Publicar](media/event-grid-event-hubs-integration/select-publish.png)

Depois de publicar a função, você estará pronto para assinar o evento.

## <a name="subscribe-to-the-event"></a>Assinar o evento

1. Em uma nova guia ou em uma nova janela de um navegador da Web, navegue até o [portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **Grupos de recursos** no menu esquerdo. 
3. Filtre a lista de grupos de recursos digitando o nome do seu grupo de recursos na caixa de pesquisa. 
4. Selecione seu grupo de recursos na lista.

    ![Selecione o grupo de recursos](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Selecione o Plano do Serviço de Aplicativo (não o Serviço de Aplicativo) na lista de recursos no grupo de recursos. 
5. Na página Plano do Serviço de Aplicativo, selecione **Aplicativos** no menu à esquerda e selecione o aplicativo de funções. 

    ![Selecione seu aplicativo de funções](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Expanda o aplicativo de funções, expanda as funções e, em seguida, selecione sua função. 
7. Selecione **Adicionar assinatura de Grade de Eventos** na barra de ferramentas. 

    ![Selecionar a função do Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. Na página **Criar Assinatura de Grade de Eventos**, faça as seguintes ações: 
    1. Na página **DETALHES DA ASSINATURA DE EVENTO**, insira um nome para a assinatura (por exemplo: captureEventSub) e selecione **Criar**. 
    2. Na seção **DETALHES DO TÓPICO**, execute as seguintes ações:
        1. Selecione **Namespaces dos Hubs de Eventos** como os **Tipos de Tópico**. 
        2. Selecione sua assinatura do Azure.
        2. Selecione o grupo de recursos do Azure.
        3. Selecione o namespace dos Hubs de Eventos.
    3. Na seção **TIPOS DE EVENTO**, confirme se o **Arquivo de Captura Criado** está selecionado para **Filtrar para Tipos de Evento**. 
    4. Na seção **DETALHES DO PONTO DE EXTREMIDADE**, confirme se o **Tipo de ponto de extremidade** está definido como **Função do Azure** e se **Ponto de extremidade** está definido como a função do Azure. 
    
        ![Criar Assinatura de Grade de Eventos](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Executar o aplicativo para gerar dados
Você terminou de configurar seu hub de eventos, o Azure Synapse Analytics, o aplicativo de funções do Azure e a assinatura de evento. Antes de executar um aplicativo que gere dados para o hub de eventos, você precisa configurar alguns valores.

1. No portal do Azure, navegue até o grupo de recursos como fez anteriormente. 
2. Selecione o namespace Hubs de Eventos.
3. Na página **Namespace de Hubs de Eventos**, selecione **Políticas de acesso compartilhado** no menu à esquerda.
4. Selecione **RootManageSharedAccessKey** na lista de políticas. 
5. Selecione o botão Copiar ao lado da caixa de texto **Chave primária da cadeia de conexão**. 

    ![Cadeia de conexão para o namespace do hub de eventos](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Volte para sua solução do Visual Studio. 
2. No projeto WindTurbineDataGenerator, abra **program.cs**.
5. Substitua os dois valores de constantes. Use o valor copiado para **EventHubConnectionString**. Use **hubdatamigration** no nome do hub de eventos. Se você tiver usado um nome diferente para o hub de eventos, especifique esse nome. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compile a solução. Execute o aplicativo **WindTurbineGenerator.exe**. 
7. Depois de alguns minutos, consulte os dados migrados na tabela em seu data warehouse.

    ![Resultados da consulta](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Dados de eventos gerados pelo hub de eventos
A Grade de Eventos distribui os dados do evento para os assinantes. O exemplo a seguir mostra os dados de evento gerados quando os dados sendo transmitidos por um hub de eventos são capturados em um blob. Em particular, observe se a propriedade `fileUrl` no objeto `data` aponta para o blob no armazenamento. O aplicativo de funções usa essa URL para recuperar o arquivo de blob com os dados capturados.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as diferenças nos serviços do sistema de mensagens do Azure, veja [Escolher entre os serviços do Azure que entregam mensagens](compare-messaging-services.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para uma introdução à Captura dos Hubs de Eventos, consulte [Habilitar a Captura dos Hubs de Eventos usando o Portal do Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para obter mais informações sobre como configurar e executar o exemplo, consulte [exemplo Captura dos Hubs de Eventos e Grade de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
