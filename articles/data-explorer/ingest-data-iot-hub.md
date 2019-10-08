---
title: Ingerir dados do Hub IoT para o Azure Data Explorer
description: Neste artigo, você aprenderá a ingerir (carregar) dados no Azure Data Explorer do Hub IoT.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: ef66e6a69b6d33b16a558293fe29b8adf51cd137
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996764"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Ingerir dados do Hub IoT para o Azure Data Explorer (versão prévia)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão (carregamento de dados) do Hub IoT, uma plataforma Big Data streaming e serviço de ingestão de IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* Crie [um cluster de teste e um banco de dados](create-cluster-database-portal.md) com o nome do banco de dados *TestDB*.

* [Um aplicativo de exemplo](https://github.com/Azure-Samples/azure-iot-samples-csharp) e documentação para simular um dispositivo.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para executar o aplicativo de exemplo.

## <a name="create-an-iot-hub"></a>Criar um hub IOT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrar um dispositivo no Hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Criar uma tabela de destino no Gerenciador de dados do Azure

Agora, você cria uma tabela no Azure Data Explorer à qual os hubs IoT enviarão dados. Você cria a tabela no cluster e no banco de dados provisionado em [**pré-requisitos**](#prerequisites).

1. No portal do Azure, navegue até seu cluster e selecione **Consulta**.

    ![Consulta do ADX no portal](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Copie o seguinte comando na janela e selecione **Executar** para criar a tabela (TestTable) que receberá os dados ingeridos.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Execução criar consulta](media/ingest-data-iot-hub/run-create-query.png)

1. Copie o seguinte comando na janela e selecione **Executar** para mapear os dados JSON de entrada para os tipos de dados e nomes de coluna da tabela (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Conectar a tabela de Data Explorer do Azure ao Hub IoT

Agora você se conecta ao Hub IoT do Azure Data Explorer. Quando essa conexão é concluída, os dados que fluem para os fluxos do Hub IOT para a [tabela de destino que você criou](#create-a-target-table-in-azure-data-explorer).

1. Selecione **notificações** na barra de ferramentas para verificar se a implantação do Hub IOT foi bem-sucedida.

1. No cluster que você criou, selecione **bancos** de dados e, em seguida, selecione o Database que você criou **TestDB**.
    
    ![Banco de dados de testes](media/ingest-data-iot-hub/select-database.png)

1. Selecione **ingestão de dados** e **Adicionar conexão de dados**. Então preencha o formulário com as seguintes informações. Selecione **criar** quando tiver terminado.

    ![Conexão do Hub IoT](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Fonte de dados**:

    **Configuração** | **Descrição do campo**
    |---|---|
    | Nome da conexão de dados | O nome da conexão que você deseja criar no Azure Data Explorer
    | Hub IoT | Nome do Hub IoT |
    | Política de acesso compartilhado | O nome da política de acesso compartilhado. Deve ter permissões de leitura |
    | Grupo de consumidores |  O grupo de consumidores definido no ponto de extremidade interno do Hub IoT |
    | Propriedades do sistema de eventos | As [Propriedades do sistema de eventos do Hub IOT](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Ao adicionar propriedades do sistema, [crie](/azure/kusto/management/tables#create-table) ou [atualize](/azure/kusto/management/tables#alter-table-and-alter-merge-table) o esquema de tabela e o [mapeamento](/azure/kusto/management/mappings) para incluir as propriedades selecionadas. | | | 

    > [!NOTE]
    > No caso de um [failover manual](/azure/iot-hub/iot-hub-ha-dr#manual-failover), você deve recriar a conexão de dados.

    **Tabela de destino**:

    Há duas opções para rotear os dados ingeridos: *estático* e *dinâmico*. 
    Para este artigo, você usará o roteamento estático, no qual especificará o nome da tabela, o formato de dados e o mapeamento. Portanto, não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *TestTable* | A tabela que você criou em **TestDB**. |
    | Formato de dados | *JSON* | Os formatos com suporte são Avro, CSV, JSON, JSON MULTILINHA, PSV, SOHSV, SCSV, TSV, TSVE e TXT. |
    | Mapeamento de coluna | *TestMapping* | O [mapeamento](/azure/kusto/management/mappings) que você criou em **TestDB**, que mapeia dados JSON de entrada para os nomes de coluna e tipos de dados de **TestDB**. Necessário para JSON, JSON MULTILINHA e AVRO, e opcional para outros formatos.|
    | | |

    > [!NOTE]
    > * Escolha **Meus dados incluem informações de roteamento** para usar o roteamento dinâmico no qual os dados incluem as informações de roteamento necessárias, como visto nos comentários do [exemplo de aplicativo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest). Se as propriedades estáticas e dinâmicas estiverem definidas, as propriedades dinâmicas substituirão as estáticas. 
    > * Somente os eventos enfileirados após a criação da conexão de dados são ingeridos.

## <a name="generate-sample-data-for-testing"></a>Gerar dados de exemplo para teste

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade.

1. Faça o download do projeto de exemplo em C# do https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP.

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto C# de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o arquivo **SimulatedDevice.cs** em seu editor de texto preferido.

    Substitua o valor da variável `s_connectionString` pela cadeia de conexão do dispositivo de [registrar um dispositivo no Hub IOT](#register-a-device-to-the-iot-hub). Salve as alterações no arquivo **SimulatedDevice.cs**.

1. Na janela de terminal local, execute os seguintes comandos para instalar os pacotes necessários para o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

1. Na janela de terminal local, execute os seguintes comandos para compilar e executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet run
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Revise o fluxo de dados

Com o aplicativo que gera dados, agora você pode ver o fluxo de dados do Hub IoT para a tabela no cluster.

1. Na portal do Azure, em seu hub IoT, você verá o pico na atividade enquanto o aplicativo estiver em execução.

    ![Métricas do Hub IoT](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Para verificar quantas mensagens chegaram ao banco de dados até o momento, execute a consulta a seguir em seu banco de dados de teste.

    ```Kusto
    TestTable
    | count
    ```

1. Para ver o conteúdo das mensagens, execute a consulta a seguir:

    ```Kusto
    TestTable
    ```

    O conjunto de resultados:
    
    ![Mostrar resultados de dados ingeridos](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * O Azure Data Explorer tem uma política de agregação (envio em lote) para a ingestão de dados, criada para otimizar o processo de ingestão. A política é configurada para 5 minutos ou 500 MB de dados, por padrão, para que você possa experimentar uma latência. Consulte [política de envio em lote](/azure/kusto/concepts/batchingpolicy) para obter opções de agregação. 
    > * Configure sua tabela para dar suporte ao streaming e remova a latência no tempo de resposta. Consulte a [política de streaming](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não planeja usar o Hub IoT novamente, limpe **Test-Hub-RG**para evitar incorrer em custos.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda, depois selecione o recurso de grupo que você criou.  

    Se o menu à esquerda estiver recolhido, selecione ![botão Expandir](media/ingest-data-event-hub/expand.png) para expandi-lo.

   ![Selecione o grupo de recursos para excluir](media/ingest-data-event-hub/delete-resources-select.png)

1. Em **test-resource-group**, selecione **Excluir grupo de recursos**.

1. Na nova janela, digite o nome do grupo de recursos para excluir (*test-hub-rg*) e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

* [Consultar dados no Azure Data Explorer](web-query-data.md)
