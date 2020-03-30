---
title: Ingerir Blobs do Azure no Azure Data Explorer
description: Neste artigo, você aprende como enviar dados de conta de armazenamento para o Azure Data Explorer usando uma assinatura event grid.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370433"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingerir blobs no Azure Data Explorer assinando notificações da Grade de Eventos

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modelo de Gerenciador de recursos do Azure](data-connection-event-grid-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e escalonável para dados de log e telemetria. Ele oferece ingestão contínua (carregamento de dados) de blobs gravados em contêineres de blob. 

Neste artigo, você aprende como definir uma assinatura [do Azure Event Grid](/azure/event-grid/overview) e encaminhar eventos para o Azure Data Explorer através de um hub de eventos. Para começar, você precisa ter uma conta de armazenamento com uma assinatura da grade de eventos que envie notificações para os Hubs de Eventos do Azure. Então, você criará uma conexão de dados da Grade de Eventos e verá o fluxo de dados pelo sistema.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
* [Um cluster e um banco de dados.](create-cluster-database-portal.md)
* [Uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Um hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Criar uma assinatura da Grade de Eventos em sua conta de armazenamento

1. No portal do Azure, encontre a conta de armazenamento.
1. Selecione **a** > **assinatura de eventos .**

    ![Link do aplicativo de consulta](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Na janela **Criar assinatura de evento** na guia **Básico**, forneça os valores a seguir:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome | *test-grid-connection* | O nome da grade de eventos que você deseja criar.|
    | Esquema do evento | *Esquema da Grade de Eventos* | O esquema que deve ser usado para a grade de eventos. |
    | Tipo de tópico | *Conta de armazenamento* | O tipo de tópico da grade de eventos. |
    | Recurso do Tópico | *gridteststorage* | O nome da sua conta de armazenamento. |
    | Assinar todos os tipos de evento | *Claro* | Não ser notificado de todos os eventos. |
    | Tipos de Eventos Definidos | *Blob criado* | De quais eventos específicos receber notificações. |
    | Tipo de Ponto de Extremidade | *Hubs de eventos* | O tipo do ponto de extremidade ao qual você envia os eventos. |
    | Ponto de extremidade | *test-hub* | O hub de eventos que você criou. |
    | | |

1. Selecione a guia **Filtros** se quiser rastrear arquivos de um contêiner específico. Defina os filtros das notificações da seguinte maneira:
    * Campo **Assunto começa com** é o prefixo *literal* do contêiner de blobs. Como é o padrão aplicado é *startswith*, ele pode abranger vários contêineres. Não são permitidos curingas.
     Ele *precisa* ser definido da seguinte maneira: *`/blobServices/default/containers/`*[prefixo do contêiner]
    * O campo **Assunto termina com** é o sufixo *literal* do blob. Não são permitidos curingas.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Criar uma tabela de destino no Gerenciador de dados do Azure

Crie uma tabela no Azure Data Explorer em que os Hubs de Eventos enviarão dados. Crie a tabela no cluster e no banco de dados preparado nos pré-requisitos.

1. No portal do Azure, em seu cluster, selecione **consulta**.

    ![Link do aplicativo de consulta](media/ingest-data-event-grid/query-explorer-link.png)

1. Copie o seguinte comando na janela e selecione **Executar** para criar a tabela (TestTable) que receberá os dados ingeridos.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Execução criar consulta](media/ingest-data-event-grid/run-create-table.png)

1. Copie o seguinte comando na janela e selecione **Executar** para mapear os dados JSON de entrada para os tipos de dados e nomes de coluna da tabela (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Criar uma conexão de dados da Grade de Eventos no Azure Data Explorer

Agora conecte-se à Grade de Eventos do Azure Data Explorer, para que os dados que fluem para o contêiner blob sejam transmitidos para a tabela de testes. 

1. Selecione **Notificações** na barra de ferramentas para verificar se a implantação do hub de eventos foi bem-sucedida.

1. No cluster que você criou, selecione **Databases** > **TestDatabase**.

    ![Banco de dados de testes](media/ingest-data-event-grid/select-test-database.png)

1. Selecione **a ingestão de** > **dados Adicionar conexão de dados**.

    ![Ingestão de dados](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Selecione o tipo de conexão: **Blob Storage**.

1. Preencha o formulário com as seguintes informações e selecione **Criar**.

    ![Conexão do hub de eventos](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Fonte de dados:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da conexão de dados | *teste de hub de conexão* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | Assinatura da conta de armazenamento | Sua ID de assinatura | A ID da assinatura em que sua conta de armazenamento reside.|
    | Conta de armazenamento | *gridteststorage* | O nome da conta de armazenamento que você criou anteriormente.|
    | Grade de Eventos | *test-grid-connection* | O nome da grade de eventos que você criou. |
    | Nome do Hub de Eventos | *test-hub* | O hub de eventos que você criou. Esse campo é preenchido automaticamente quando você escolhe uma grade de eventos. |
    | Grupo de consumidores | *test-group* | O grupo de consumidores definido no hub de eventos que você criou. |
    | | |

    Tabela de destino:

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *TestTable* | A tabela criada na **TestDatabase**. |
    | Formato de dados | *JSON* | Os formatos suportados são Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW e TXT. Opções de compactação suportadas: Zip e GZip |
    | Mapeamento de coluna | *TestMapping* | O mapeamento que você criou em **TestDatabase**, que mapeia os dados de entrada JSON para tipos de dados e nomes de coluna da **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora que o Azure Data Explorer e a conta de armazenamento estão conectados, você pode criar dados de exemplo e fazer upload deles no Armazenamento de Blobs.

Nós trabalharemos com um pequeno script shell que emite alguns comandos básicos da CLI do Azure para interagir com recursos do Armazenamento do Azure. Esse script cria um novo contêiner na conta de armazenamento, carrega um arquivo existente (como um blob) nesse contêiner e, em seguida, lista os blobs no contêiner. Você pode usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para executar o script diretamente no portal.

Salve os dados em um arquivo e carregue-o com este script:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Para obter o melhor desempenho de ingestão, deve ser comunicado o tamanho *descompactado* das bolhas compactadas submetidas à ingestão. Como as notificações do Event Grid contêm apenas detalhes básicos, as informações de tamanho devem ser explicitamente comunicadas. As informações de tamanho não compactados `rawSizeBytes` podem ser fornecidas definindo a propriedade nos metadados blob com o tamanho de dados *não compactado* em bytes.

### <a name="ingestion-properties"></a>Propriedades da ingestão

Você pode especificar as propriedades de [Ingestão](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) da ingestão de bolhas através dos metadados blob.

Essas propriedades podem ser definidas:

|**Propriedade** | **Descrição da propriedade**|
|---|---|
| `rawSizeBytes` | Tamanho dos dados brutos (não comprimidos). Para Avro/ORC/Parquet, este é o tamanho antes da aplicação da compactação específica do formato.|
| `kustoTable` |  Nome da tabela de destino existente. Substitui o `Table` conjunto `Data Connection` na lâmina. |
| `kustoDataFormat` |  Formato de dados. Substitui o `Data format` conjunto `Data Connection` na lâmina. |
| `kustoIngestionMappingReference` |  Nome do mapeamento de ingestão existente a ser utilizado. Substitui o `Column mapping` conjunto `Data Connection` na lâmina.|
| `kustoIgnoreFirstRecord` | Se estiver `true`definido, Kusto ignora a primeira linha da bolha. Use em dados de formato tabular (CSV, TSV ou similares) para ignorar cabeçalhos. |
| `kustoExtentTags` | String representando [tags](/azure/kusto/management/extents-overview#extent-tagging) que serão anexadas em extensão resultante. |
| `kustoCreationTime` |  Substitui [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) para a bolha, formatado como uma seqüência ISO 8601. Use para recarga. |

> [!NOTE]
> O Azure Data Explorer não excluirá as bolhas de publicação de ingestão.
> Mantenha as bolhas por até cinco dias.
> Use [o ciclo de vida do armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) para gerenciar a exclusão de bolhas. 

## <a name="review-the-data-flow"></a>Revise o fluxo de dados

> [!NOTE]
> O Azure Data Explorer tem uma política de agregação (envio em lote) para a ingestão de dados, criada para otimizar o processo de ingestão.
Por padrão, a política é configurada como 5 minutos.
Você será capaz de alterar a apólice mais tarde, se necessário. Neste artigo você pode esperar uma latência de alguns minutos.

1. No portal do Azure, em sua grade de eventos, você vê o pico de atividade enquanto o aplicativo está em execução.

    ![Gráfico da grade de eventos](media/ingest-data-event-grid/event-grid-graph.png)

1. Para verificar quantas mensagens chegaram ao banco de dados até o momento, execute a consulta a seguir em seu banco de dados de teste.

    ```kusto
    TestTable
    | count
    ```

1. Para ver o conteúdo das mensagens, execute a consulta a seguir em seu banco de dados de teste.

    ```kusto
    TestTable
    ```

    O conjunto de resultados deve se parecer com o seguinte.

    ![Conjunto de resultados de mensagem](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não planeja usar sua grade de eventos novamente, limpe **test-hub-rg** para evitar custos.

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda, depois selecione o recurso de grupo que você criou.  

    Se o menu à esquerda estiver recolhido, selecione ![botão Expandir](media/ingest-data-event-grid/expand.png) para expandi-lo.

   ![Selecione o grupo de recursos para excluir](media/ingest-data-event-grid/delete-resources-select.png)

1. Em **test-resource-group**, selecione **Excluir grupo de recursos**.

1. Na nova janela, insira o nome do grupo de recursos para excluir (*test-hub-rg*) e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

* [Consultar dados no Azure Data Explorer](web-query-data.md)
