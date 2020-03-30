---
title: Use a biblioteca .NET do executor a granel no Azure Cosmos DB para operações de importação e atualização a granel
description: Importação e atualização em massa dos documentos do Azure Cosmos DB usando a biblioteca .NET do executor em massa.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246871"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Use a biblioteca .NET do executor em massa para realizar operações em massa no Azure Cosmos DB

Este tutorial fornece instruções sobre como usar a biblioteca .NET do executor em massa para importar e atualizar documentos para um contêiner Do Azure Cosmos. Para saber mais sobre a biblioteca de executores em massa e como ela ajuda você a aproveitar o throughput e o armazenamento em massa, consulte o artigo [de visão geral da biblioteca executor a granel.](bulk-executor-overview.md) Neste tutorial, você verá uma amostra do aplicativo .NET que importa documentos gerados aleatoriamente em um contêiner do Azure Cosmos. Após a importação, ele mostra como você pode atualizar em massa os dados importados, especificando os patches como operações a serem executadas em campos de documento específicos.

Atualmente, a biblioteca de executores em massa é suportada apenas pelas contas API AQ SQL db SQL e Gremlin Do API do Azure Cosmos. Este artigo descreve como usar a biblioteca .NET do executor em massa com contas API SQL. Para saber mais sobre o uso da biblioteca .NET em massa com contas de API Gremlin, consulte [realizar operações em massa na API Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se você ainda não tem o Visual Studio 2019 instalado, você pode baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Certifique-se de ativar o "desenvolvimento do Azure" durante a configuração do Visual Studio.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, gratuitamente e sem compromisso. Ou, você pode usar o [emulador Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) com o `https://localhost:8081` ponto final. A Chave Primária é fornecida nas [Solicitações de autenticação](local-emulator.md#authenticating-requests).

* Crie uma conta de API SQL do Azure Cosmos DB usando as etapas descritas na seção [criar conta de banco de dados](create-sql-api-dotnet.md#create-account) do artigo de início rápido do .NET.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora vamos mudar para trabalhar com código baixando uma amostra do aplicativo .NET do GitHub. Este aplicativo realiza operações em massa nos dados armazenados em sua conta do Azure Cosmos. Para clonar o aplicativo, abra um prompt de comando, navegue até o diretório onde deseja copiá-lo e execute o seguinte comando:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

O repositório clonado contém duas amostras "BulkImportSample" e "BulkUpdateSample". Você pode abrir qualquer um dos aplicativos de exemplo, atualizar as cadeias de conexão no arquivo App.config com as suas cadeias de conexão da conta do Azure Cosmos DB, compilar a solução e executá-la.

O aplicativo "BulkImportSample" gera documentos aleatórios e os importa em massa para sua conta do Azure Cosmos. O aplicativo "BulkUpdateSample" atualiza em massa os documentos importados, especificando os patches como operações a serem executadas em campos de documento específicos. Nas próximas seções, você examinará o código em cada um desses aplicativos de exemplo.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Dados de importação em massa para uma conta do Azure Cosmos

1. Navegue até a pasta "BulkImportSample" e abra o arquivo "BulkImportSample.sln".  

2. As cadeias de conexão do Azure Cosmos DB são recuperadas do arquivo App.config, conforme mostrado no código a seguir:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   O importador a granel cria um novo banco de dados e um contêiner com o nome do banco de dados, o nome do contêiner e os valores de throughput especificados no arquivo App.config.

3. Em seguida, o objeto DocumentClient é inicializado com o modo de conexão Direct TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. O objeto BulkExecutor é inicializado com um alto valor de repetição para tempo de espera e solicitações estranguladas. E, em seguida, eles são definidos como 0 para passar o controle de congestionamento para BulkExecutor por toda a sua vida útil.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. O aplicativo chama a API BulkImportAsync. A biblioteca .NET fornece duas sobrecargas da API de importação a granel - uma que aceita uma lista de documentos JSON serializados e outra que aceita uma lista de documentos POCO desserializados. Para saber mais sobre as definições de cada um desses métodos sobrecarregados, consulte a documentação da [API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **O método BulkImportAsync aceita os seguintes parâmetros:**
   
   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |enableUpsert    |   Uma bandeira para habilitar operações upsert nos documentos. Se um documento com o ID dado já existir, ele será atualizado. Por padrão, está definido como false.      |
   |disableAutomaticIdGeneration    |    Um sinalizador para desabilitar a geração automática de ID. Por padrão, está definido como true.     |
   |maxConcurrencyPerPartitionKeyRange    | O grau máximo de simultaneidade por intervalo de chave de partição, a configuração como null fará com que a biblioteca use um valor padrão de 20. |
   |maxInMemorySortingBatchSize     |  O número máximo de documentos que são retirados do enumerador de documentos, que é repassado para a chamada da API em cada etapa. Para a fase de classificação na memória que acontece antes da importação em massa, definir este parâmetro como nulo fará com que a biblioteca use o valor mínimo padrão (documents.count, 1000000).       |
   |cancellationToken    |    O token de cancelamento para sair graciosamente da operação de importação a granel.     |

   **Definição de objeto de resposta de importação em massa** O resultado da chamada de API de importação em massa contém os seguintes atributos:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  O número total de documentos que foram importados com sucesso do total de documentos fornecidos à chamada aPI de importação em massa.       |
   |TotalRequestUnitsConsumed (double)   |   O total de unidades de solicitação (RU) consumidas pela chamada de API de importação em massa.      |
   |TotalTimeTaken (TimeSpan)    |   O tempo total gasto pela aPI de importação a granel para concluir a execução.      |
   |BadInputDocuments (lista\<objeto>)   |     A lista de documentos com formato inválido que não foram importados com êxito na chamada de API de importação em massa. Corrija os documentos devolvidos e tente novamente a importação. Os documentos com formato inválido incluem documentos cujo valor de ID não é uma cadeia de caracteres (null ou qualquer outro tipo de dados é considerado inválido).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Dados de atualização em massa em sua conta do Azure Cosmos

Você pode atualizar os documentos existentes usando a API BulkUpdateAsync. Neste exemplo, você definirá o `Name` campo como `Description` um novo valor e removerá o campo dos documentos existentes. Para obter o conjunto completo de operações de atualização suportadas, consulte a documentação da [API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

1. Navegue até a pasta "BulkUpdateSample" e abra o arquivo "BulkUpdateSample.sln".  

2. Defina os itens de atualização juntamente com as operações de atualização de campo correspondentes. Neste exemplo, você `SetUpdateOperation` usará para `Name` atualizar `UnsetUpdateOperation` o `Description` campo e remover o campo de todos os documentos. Você também pode executar outras operações, como incremento de um campo de documento por um valor específico, efetuar push de valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para saber mais sobre os diferentes métodos fornecidos pela API de atualização em massa, consulte a [documentação da API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. O aplicativo chama a API BulkUpdateAsync. Para saber mais sobre a definição do método BulkUpdateAsync, consulte a documentação da [API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **O método BulkUpdateAsync aceita os seguintes parâmetros:**

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   O grau máximo de simultude por intervalo de teclas de partição, definindo este parâmetro como nulo fará com que a biblioteca use o valor padrão(20).   |
   |maxInMemorySortingBatchSize    |    O número máximo de itens de atualização retirados do enumerador de itens de atualização passou para a chamada da API em cada etapa. Para a fase de classificação na memória que acontece antes da atualização em massa, definir esse parâmetro como nulo fará com que a biblioteca use o valor mínimo padrão (updateItems.count, 10000000).     |
   | cancellationToken|O token de cancelamento para sair graciosamente da operação de atualização em massa. |

   **Definição de objeto de resposta de atualização em massa** O resultado da chamada de API desatualização em massa contém os seguintes atributos:

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   O número de documentos que foram atualizados com sucesso do total de documentos fornecidos à chamada aPI de atualização em massa.      |
   |TotalRequestUnitsConsumed (double)   |    As unidades de solicitação totais (RUs) consumidas pela chamada aPI de atualização em massa.    |
   |TotalTimeTaken (TimeSpan)   | O tempo total gasto pela chamada aPI de atualização em massa para concluir a execução. |
    
## <a name="performance-tips"></a>Dicas de desempenho 

Considere os seguintes pontos para obter melhor desempenho ao usar a biblioteca executor a granel:

* Para obter o melhor desempenho, execute seu aplicativo a partir de uma máquina virtual Azure que esteja na mesma região da região de gravação da sua conta Do Azure Cosmos.  

* Recomenda-se que você instancia um único `BulkExecutor` objeto para toda a aplicação dentro de uma única máquina virtual que corresponda a um contêiner Azure Cosmos específico.  

* Uma vez que uma única execução de API de operação em massa consome uma grande parte da CPU e iO de rede da máquina cliente (isso acontece gerando várias tarefas internamente). Evite gerar várias tarefas simultâneas dentro do processo de aplicativo que executam chamadas de API de operação em massa. Se uma única chamada de API de operação em massa que está sendo executada em uma única máquina virtual não conseguir consumir todo o throughput do contêiner (se o throughput do seu contêiner > 1 milhão de RU/s), é preferível criar máquinas virtuais separadas para executar simultaneamente as chamadas de API de operação em massa.  

* Certifique-se de que o `InitializeAsync()` método é invocado depois de instanciar um objeto BulkExecutor para buscar o mapa de partição do contêiner Cosmos alvo.  

* No App.Config do aplicativo, certifique-se de **gcServer** esteja habilitado para melhor desempenho
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A biblioteca emite rastreamentos que podem ser coletados em um arquivo de log ou no console. Para habilitar ambos, adicione o seguinte código ao arquivo App.Config do seu aplicativo.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os detalhes do pacote Nuget e as notas de lançamento, consulte os detalhes do [executor em massa SDK](sql-api-sdk-bulk-executor-dot-net.md).
