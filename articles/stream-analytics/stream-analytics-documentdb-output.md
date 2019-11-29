---
title: Saída do Azure Stream Analytics para Cosmos DB
description: Este artigo descreve como usar o Azure Stream Analytics para salvar a saída no Azure Cosmos DB para saída JSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: aa4ac011a7b6258958ac1ac176fd63b18a4ef856
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560178"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída do Azure Stream Analytics para Azure Cosmos DB  
O Stream Analytics pode direcionar o [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para uma saída em JSON, possibilitando o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento aborda algumas práticas recomendadas para implementar essa configuração.

Para aqueles que não estão familiarizados com o Cosmos DB, consulte [Roteiro de aprendizagem do Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obter uma introdução. 

> [!Note]
> Neste momento, o Azure Stream Analytics oferece suporte apenas à conexão ao Microsoft Azure CosmosDB usando a **API do SQL**.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Conceitos básicos do Cosmos DB como um destino de saída
A saída de Azure Cosmos DB no Stream Analytics permite gravar seus resultados de processamento de fluxo como saída JSON em seus contêineres de Cosmos DB. Stream Analytics não cria contêineres em seu banco de dados, em vez disso, exige que você os crie antecipadamente. Isso é para que os custos de cobrança de contêineres de Cosmos DB sejam controlados por você e para que você possa ajustar o desempenho, a consistência e a capacidade de seus contêineres diretamente usando as [APIs de Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Você deve adicionar 0.0.0.0 à lista de IPs permitidos no firewall do seu Microsoft Azure Cosmos DB.

Algumas das opções de contêiner de Cosmos DB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, a disponibilidade e a latência
Para corresponder aos requisitos do aplicativo, Azure Cosmos DB permite ajustar o banco de dados e os contêineres e fazer compensações entre consistência, disponibilidade, latência e taxa de transferência. Dependendo dos níveis de consistência de leitura exigidos pelo seu cenário em relação à latência de leitura e de gravação, você poderá escolher um nível de consistência em sua conta de banco de dados. A taxa de transferência pode ser melhorada com a expansão de RUs (unidades de solicitação) no contêiner. Também por padrão, Azure Cosmos DB habilita a indexação síncrona em cada operação CRUD para seu contêiner. Essa é outra opção útil para controlar o desempenho de leitura/gravação no Microsoft Azure Cosmos DB. Para obter mais informações, consulte o artigo [alterar o banco de dados e níveis de consistência de consulta](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Inserções e atualizações a partir do Stream Analytics
Stream Analytics integração com o Azure Cosmos DB permite inserir ou atualizar registros em seu contêiner com base em uma determinada coluna de ID de documento. Isso também é chamado de *Upsert*.

O Stream Analytics utiliza uma abordagem upsert otimista, na qual as atualizações são feitas somente quando a inserção falha com um conflito de ID do Documento. Com o nível de compatibilidade 1,0, essa atualização é executada como um PATCH, de modo que permite atualizações parciais no documento, ou seja, a adição de novas propriedades ou a substituição de uma propriedade existente é executada de forma incremental. Porém, as alterações nos valores de propriedades de matriz em seu documento JSON resultam na substituição de toda a matriz, ou seja, a matriz não é mesclada. Com o 1,2, o comportamento Upsert é modificado para inserir ou substituir o documento. Isso é descrito mais detalhadamente na seção nível de compatibilidade 1,2 abaixo.

Se o documento JSON recebido tiver um campo de ID existente, esse campo será usado automaticamente como coluna de ID do documento do Cosmos DB e quaisquer gravações subsequentes serão tratadas como tal, levando a uma destas situações:
- IDs exclusivas levam a inserção
- IDs duplicadas e 'ID do documento' definida como 'ID' levam a upsert
- IDs duplicadas e 'ID do documento' não definida leva a erros após o primeiro documento

Se você quiser salvar <i>todos</i> os documentos, incluindo aqueles com uma ID duplicada, renomeie o campo de ID em sua consulta (com a palavra-chave AS) e permita que o Cosmos DB crie o campo de ID ou substitua a ID por outro valor de coluna (usando a palavra-chave AS ou usando a configuração de ‘ID do documento’).

## <a name="data-partitioning-in-cosmos-db"></a>Particionamento de dados no Cosmos DB
Os contêineres [ilimitados](../cosmos-db/partition-data.md) do Azure Cosmos DB são a abordagem recomendada para particionar seus dados, à medida que o Azure Cosmos DB automaticamente dimensiona partições com base em sua carga de trabalho. Ao gravar os contêineres ilimitados, o Stream Analytics usa tantos gravadores paralelos como a etapa de consulta anterior ou entrada de esquema de particionamento.
> [!NOTE]
> Neste momento, Azure Stream Analytics dá suporte apenas a contêineres ilimitados com chaves de partição no nível superior. Por exemplo, `/region` tem suporte. Não há suporte para chaves de partição aninhadas (por exemplo, `/region/name`). 

Dependendo de sua escolha de chave de partição, você poderá receber este _aviso_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

É importante escolher uma propriedade de chave de partição que tenha um número de valores distintos e permitir que você distribua sua carga de trabalho uniformemente entre esses valores. Como um artefato natural de particionamento, as solicitações que envolvem a mesma chave de partição são limitadas pela taxa de transferência máxima de uma única partição. Além disso, o tamanho do armazenamento para documentos pertencentes à mesma chave de partição é limitado a 10 GB. Uma chave de partição ideal é aquela que é exibida com frequência, como um filtro nas suas consultas, e tem cardinalidade suficiente para garantir que sua solução seja escalonável.

Uma chave de partição também é o limite para transações nos gatilhos e procedimentos armazenados do DocumentDB. Você deve escolher a chave de partição para que os documentos que ocorrem em transações compartilhem o mesmo valor de chave de partição. O artigo [particionamento no cosmos DB](../cosmos-db/partitioning-overview.md) fornece mais detalhes sobre como escolher uma chave de partição.

Para contêineres de Azure Cosmos DB fixos, Stream Analytics não permite escalar verticalmente ou horizontalmente quando estiverem cheios. Eles têm um limite de 10 GB e taxa de transferência de 10.000 RU/s.  Para migrar dados de um contêiner fixo para um contêiner ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), você precisa usar a [ferramenta de migração de dados](../cosmos-db/import-data.md) ou a [biblioteca de feed de alterações](../cosmos-db/change-feed.md).

A capacidade de gravar em vários contêineres fixos está sendo preterida e não é recomendável para escalar horizontalmente seu trabalho de Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Taxa de transferência aprimorada com nível de compatibilidade 1,2
Com o nível de compatibilidade 1,2, o Stream Analytics dá suporte à integração nativa para gravação em massa em Cosmos DB. Isso permite a gravação eficaz para Cosmos DB com a maximização da taxa de transferência e o tratamento eficiente de solicitações de limitação. O mecanismo de escrita aprimorado está disponível em um novo nível de compatibilidade devido a uma diferença de comportamento de Upsert.  Antes de 1,2, o comportamento do Upsert é inserir ou mesclar o documento. Com o 1,2, o comportamento upserts é modificado para inserir ou substituir o documento.

Antes de 1,2, o usa um procedimento armazenado personalizado para Upsert em massa documentos por chave de partição em Cosmos DB, em que um lote é gravado como uma transação. Mesmo quando um único registro atinge um erro transitório (limitação), todo o lote deve ser repetido. Isso tornou os cenários com uma limitação ainda razoável relativamente mais lenta. A comparação a seguir mostra como esses trabalhos se comportariam com o 1,2.

O exemplo a seguir mostra dois trabalhos de Stream Analytics idênticos lendo da mesma entrada do hub de eventos. Ambos os trabalhos de Stream Analytics são [totalmente particionados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta de passagem e gravam em contêineres CosmosDB idênticos. As métricas à esquerda são do trabalho configurado com o nível de compatibilidade 1,0 e aquelas à direita são configuradas com 1,2. A chave de partição de um contêiner de Cosmos DB é um GUID exclusivo proveniente do evento de entrada.

![comparação de métricas do Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

A taxa de eventos de entrada no Hub de eventos é 2x maior que os contêineres de Cosmos DB (20 mil RUs) são configurados para entrada, portanto, a limitação é esperada em Cosmos DB. No entanto, o trabalho com 1,2, está escrevendo consistentemente em uma taxa de transferência mais alta (eventos de saída/minuto) e com uma menor utilização média de SU%. Em seu ambiente, essa diferença dependerá de mais alguns fatores, como opções de formato de evento, tamanho de evento/mensagem de entrada, chaves de partição, consulta etc.

![comparação de métricas do cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com o 1,2, o Stream Analytics é mais inteligente na utilização de 100% da taxa de transferência disponível em Cosmos DB com poucos reenvios de limitação/limitação de taxa. Isso fornece uma experiência melhor para outras cargas de trabalho, como consultas em execução no contêiner ao mesmo tempo. Caso você precise experimentar como o ASA é dimensionado com Cosmos DB como um coletor de 1K para 10 mil mensagens/segundo, aqui está um [projeto de exemplos do Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) que permite fazer isso.
Observe que Cosmos DB taxa de transferência de saída é idêntica com 1,0 e 1,1. Como 1,2 não é o padrão no momento, você pode [definir o nível de compatibilidade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) para um trabalho de Stream Analytics usando o portal ou usando a chamada de [API REST de trabalho Create](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). É *altamente recomendável* usar o nível de compatibilidade 1,2 no ASA com Cosmos DB.



## <a name="cosmos-db-settings-for-json-output"></a>Configurações do Cosmos DB para saída em JSON

A criação do Cosmos DB como uma saída no Stream Analytics gera uma solicitação de informações, conforme mostrado abaixo. Esta seção fornece uma explicação da definição de propriedades.

![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrição|
|-------------   | -------------|
|Alias de saída    | Um alias para se referir a essa saída em sua consulta do ASA.|
|Subscription    | Escolha a assinatura do Azure.|
|ID da Conta      | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB.|
|Chave de conta     | A chave de acesso compartilhado da conta do Azure Cosmos DB.|
|Banco de dados        | O nome do banco de dados do Azure Cosmos DB.|
|Nome do contêiner | O nome do contêiner a ser usado. `MyContainer` é um contêiner de entrada válido de um recipiente chamado `MyContainer` deve existir.  |
|ID do documento     | Opcional. O nome da coluna em eventos de saída usado como a chave exclusiva que inserir ou atualizar as operações que devem ser baseadas. Se deixado vazio, todos os eventos serão inseridos, com nenhuma opção de atualização.|

Depois que a saída de Cosmos DB é configurada, ela pode ser usada na consulta como o destino de uma [instrução into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Ao usar um Cosmos DB saída como tal, [uma chave de partição precisa ser definida explicitamente](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). O registro de saída deve conter uma coluna que diferencia maiúsculas de minúsculas, denominada após a chave de partição em Cosmos DB. Para obter maior paralelização, a instrução pode exigir uma [cláusula PARTITION by](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) usando a mesma coluna.

**Consulta de exemplo**:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Tratamento de erros e novas tentativas

No caso de uma falha transitória, a indisponibilidade ou a limitação do serviço ao enviar eventos para Cosmos DB, Stream Analytics repete indefinidamente para concluir a operação com êxito. No entanto, há algumas falhas para o qual as repetições não são tentadas e elas são os seguintes:

- Não autorizado (código de erro http 401)
- Não encontrado (código de erro http 404)
- Proibido (código de erro HTTP 403)
- BadRequest (código de erro http 400)
