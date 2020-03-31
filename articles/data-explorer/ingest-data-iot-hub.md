---
title: Ingerir dados do IoT Hub no Azure Data Explorer
description: Neste artigo, você aprende como ingerir dados (carregar) no Azure Data Explorer do IoT Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 78455c90bab694b77a5e4a56d0b40518867d8d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188362"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Ingerir dados do IoT Hub no Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modelo de Gerenciador de recursos do Azure](data-connection-iot-hub-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer oferece ingestão (carregamento de dados) do IoT Hub, uma plataforma de streaming de big data e serviço de ingestão de IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Crie [um cluster de teste e um banco de dados](create-cluster-database-portal.md) com o nome do banco de dados *testdb*.
* [Um aplicativo de amostra](https://github.com/Azure-Samples/azure-iot-samples-csharp) e documentação para simular um dispositivo.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para executar o aplicativo de exemplo.

## <a name="create-an-iot-hub"></a>Crie um Hub de Iot

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registre um dispositivo no IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Criar uma tabela de destino no Gerenciador de dados do Azure

Agora você cria uma tabela no Azure Data Explorer para a qual o IoT Hubs enviará dados. Você cria a tabela no cluster e banco de dados provisionados em [**Pré-requisitos**](#prerequisites).

1. No portal do Azure, navegue até seu cluster e selecione **Consulta**.

    ![Consulta a ADX no portal](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Copie o seguinte comando na janela e selecione **Executar** para criar a tabela (TestTable) que receberá os dados ingeridos.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Execução criar consulta](media/ingest-data-iot-hub/run-create-query.png)

1. Copie o seguinte comando na janela e selecione **Executar** para mapear os dados JSON de entrada para os tipos de dados e nomes de coluna da tabela (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Conecte a tabela do Azure Data Explorer ao hub ioT

Agora você se conecta ao IoT Hub do Azure Data Explorer. Quando essa conexão estiver concluída, os dados que fluem para o hub iot são transmitidos para a [tabela de destino que você criou](#create-a-target-table-in-azure-data-explorer).

1. Selecione **Notificações** na barra de ferramentas para verificar se a implantação do IoT Hub foi bem sucedida.

1. No cluster criado, selecione **Bancos de dados** e selecione o banco de dados que você criou **testdb**.
    
    ![Banco de dados de testes](media/ingest-data-iot-hub/select-database.png)

1. Selecione **ingestão de dados** e **Adicionar conexão de dados**. Então preencha o formulário com as seguintes informações. Selecione **Criar** quando terminar.

    ![Conexão IoT Hub](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Fonte de dados:**

    **Configuração** | **Descrição do campo**
    |---|---|
    | Nome da conexão de dados | O nome da conexão que você deseja criar no Azure Data Explorer
    | Hub IoT | Nome do Hub IoT |
    | Política de acesso compartilhado | O nome da política de acesso compartilhado. Deve ter lido permissões |
    | Grupo de consumidores |  O grupo de consumidores definido no ponto final incorporado do IoT Hub |
    | Propriedades do sistema de eventos | As [propriedades do sistema de eventos IoT Hub](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Ao adicionar propriedades do sistema, [crie](/azure/kusto/management/create-table-command) ou [atualize](/azure/kusto/management/alter-table-command) esquemas de tabela e [mapeamento](/azure/kusto/management/mappings) para incluir as propriedades selecionadas. | | | 

    > [!NOTE]
    > No caso de um [failover manual,](/azure/iot-hub/iot-hub-ha-dr#manual-failover)você deve recriar a conexão de dados.

    **Tabela alvo**:

    Há duas opções para rotear os dados ingeridos: *estático* e *dinâmico*. 
    Para este artigo, você usará o roteamento estático, no qual especificará o nome da tabela, o formato de dados e o mapeamento. Portanto, não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *TestTable* | A tabela que você criou em **testdb**. |
    | Formato de dados | *JSON* | Os formatos suportados são Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE e TXT. |
    | Mapeamento de coluna | *TestMapping* | O [mapeamento](/azure/kusto/management/mappings) que você criou no **testdb**, que mapeia os dados JSON de entrada para os nomes das colunas e tipos de dados do **testdb**. Necessário para JSON, MULTILINE JSON e AVRO, e opcional para outros formatos.|
    | | |

    > [!NOTE]
    > * Escolha **Meus dados incluem informações de roteamento** para usar o roteamento dinâmico no qual os dados incluem as informações de roteamento necessárias, como visto nos comentários do [exemplo de aplicativo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest). Se as propriedades estáticas e dinâmicas estiverem definidas, as propriedades dinâmicas substituirão as estáticas. 
    > * Somente os eventos enfileirados após a criação da conexão de dados são ingeridos.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Gerar dados de amostra para testes

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade.

1. Faça o download do projeto de exemplo em C# do https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP.

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto C# de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o arquivo **SimulatedDevice.cs** em seu editor de texto preferido.

    Substitua o `s_connectionString` valor da variável pela seqüência de conexão do dispositivo de [Registrar um dispositivo para o IoT Hub](#register-a-device-to-the-iot-hub). Salve as alterações no arquivo **SimulatedDevice.cs**.

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

Com o aplicativo gerando dados, agora você pode ver o fluxo de dados do hub ioT para a tabela em seu cluster.

1. No portal Azure, seu hub ioT, você vê o pico de atividade enquanto o aplicativo está em execução.

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
    > * O Azure Data Explorer tem uma política de agregação (envio em lote) para a ingestão de dados, criada para otimizar o processo de ingestão. A diretiva é configurada em 5 minutos ou 500 MB de dados, por padrão, para que você possa experimentar uma latência. Consulte [a política de loteamento](/azure/kusto/concepts/batchingpolicy) para opções de agregação. 
    > * Configure sua tabela para suportar streaming e remova o lag no tempo de resposta. Veja [a política de streaming](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Limpar recursos

Se você não planeja usar seu IoT Hub novamente, limpe **o test-hub-rg,** para evitar custos incorridos.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda, depois selecione o recurso de grupo que você criou.  

    Se o menu à esquerda estiver recolhido, selecione ![botão Expandir](media/ingest-data-event-hub/expand.png) para expandi-lo.

   ![Selecione o grupo de recursos para excluir](media/ingest-data-event-hub/delete-resources-select.png)

1. Em **test-resource-group**, selecione **Excluir grupo de recursos**.

1. Na nova janela, digite o nome do grupo de recursos para excluir (*test-hub-rg*) e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

* [Consultar dados no Azure Data Explorer](web-query-data.md)
