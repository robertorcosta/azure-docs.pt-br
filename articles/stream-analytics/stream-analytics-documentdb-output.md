---
title: Saída do Azure Stream Analytics para Azure Cosmos DB
description: Este artigo descreve como usar o Azure Stream Analytics para salvar a saída no Azure Cosmos DB para saída JSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254437"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Saída do Azure Stream Analytics para Azure Cosmos DB  
O Azure Stream Analytics pode direcionar [o Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) para a saída JSON, permitindo o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento aborda algumas práticas recomendadas para implementar essa configuração.

Se você não está familiarizado com o Azure Cosmos DB, consulte a [documentação do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para começar. 

> [!Note]
> Neste momento, o Stream Analytics suporta conexão ao Azure Cosmos DB apenas através da *API SQL*.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Stream Analytics para contas azure Cosmos DB criadas com outras APIs, os dados podem não ser armazenados corretamente. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Noções básicas do Azure Cosmos DB como alvo de saída
A saída Azure Cosmos DB no Stream Analytics permite escrever os resultados do processamento de fluxo como saída JSON em seus contêineres Azure Cosmos DB. 

O Stream Analytics não cria contêineres em seu banco de dados. Em vez disso, requer que você os crie na frente. Em seguida, você pode controlar os custos de faturamento dos contêineres Azure Cosmos DB. Você também pode ajustar o desempenho, consistência e capacidade de seus contêineres diretamente usando as [APIs Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Você deve adicionar 0.0.0.0 à lista de IPs permitidos no firewall do seu Microsoft Azure Cosmos DB.

As seções a seguir detalham algumas das opções de contêiner para OZure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Afinando consistência, disponibilidade e latência
Para corresponder aos requisitos do aplicativo, o Azure Cosmos DB permite ajustar o banco de dados e os contêineres e fazer trocas entre consistência, disponibilidade, latência e throughput. 

Dependendo dos níveis de consistência de leitura exigidos pelo seu cenário em relação à latência de leitura e de gravação, você poderá escolher um nível de consistência em sua conta de banco de dados. Você pode melhorar o throughput aumentando as Unidades de Solicitação (RUs) no contêiner. 

Também por padrão, o Azure Cosmos DB permite indexação síncrona em cada operação CRUD para o seu contêiner. Esta é outra opção útil para controlar o desempenho de gravação/leitura no Azure Cosmos DB. 

Para obter mais informações, revise o [artigo Alterar seu banco de dados e consultar níveis de consistência.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Inserções e atualizações a partir do Stream Analytics
A integração do Stream Analytics com o Azure Cosmos DB permite que você insira ou atualize registros em seu contêiner com base em uma determinada coluna **De identificação de documentos.** Isso também é chamado *de upsert.*

O Stream Analytics usa uma abordagem otimista de upsert. As atualizações só acontecem quando uma inserção falha com um conflito de identificação de documento. 

Com o nível de compatibilidade 1.0, o Stream Analytics executa essa atualização como uma operação PATCH, para que ele permita atualizações parciais ao documento. O Stream Analytics adiciona novas propriedades ou substitui uma propriedade existente gradualmente. No entanto, alterações nos valores das propriedades de matriz no documento JSON resultam na substituição de toda a matriz. Ou seja, a matriz não está fundida. 

Com 1.2, o comportamento upsert é modificado para inserir ou substituir o documento. A seção posterior sobre o nível de compatibilidade 1.2 descreve ainda esse comportamento.

Se o documento JSON de entrada tiver um campo de Identificação existente, esse campo será usado automaticamente como a coluna **ID de documentos** no Azure Cosmos DB. Quaisquer gravações subseqüentes são tratadas como tal, levando a uma dessas situações:

- IDs únicos levam à inserção.
- IDs duplicados e **iD de documento** definidos como **ID** levam ao upsert.
- IDs duplicados e **ID de documento** não definidos levam a erro, após o primeiro documento.

Se você quiser salvar *todos os* documentos, incluindo os que possuem um ID duplicado, renomeie o campo DeD em sua consulta (usando a palavra-chave **AS).** Deixe o Azure Cosmos DB criar o campo ID ou substituir o ID pelo valor de outra coluna (usando a palavra-chave **AS** ou usando a configuração **ID de documento).**

## <a name="data-partitioning-in-azure-cosmos-db"></a>Particionamento de dados no Azure Cosmos DB
O Azure Cosmos DB dimensiona automaticamente partições com base na sua carga de trabalho. Por isso, recomendamos contêineres [ilimitados](../cosmos-db/partition-data.md) como a abordagem para particionamento de seus dados. Quando o Stream Analytics grava em contêineres ilimitados, ele usa tantos escritores paralelos quanto a etapa de consulta anterior ou o esquema de particionamento de entrada.

> [!NOTE]
> O Azure Stream Analytics suporta apenas contêineres ilimitados com chaves de partição no nível superior. Por exemplo, `/region` tem suporte. As teclas de partição aninhadas (por exemplo) `/region/name`não são suportadas. 

Dependendo da sua escolha da chave de partição, você pode receber este _aviso:_

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

É importante escolher uma propriedade de chave de partição que tenha uma série de valores distintos, e que permite que você distribua sua carga de trabalho uniformemente através desses valores. Como um artefato natural de particionamento, as solicitações que envolvem a mesma chave de partição são limitadas pelo throughput máximo de uma única partição. 

O tamanho de armazenamento para documentos que pertencem à mesma chave de partição é limitado a 10 GB. Uma chave de partição ideal é aquela que aparece frequentemente como um filtro em suas consultas e tem cardinalidade suficiente para garantir que sua solução seja escalável.

Uma chave de partição também é o limite para transações em procedimentos armazenados e gatilhos para o Azure Cosmos DB. Você deve escolher a chave de partição para que os documentos que ocorrem juntos nas transações compartilhem o mesmo valor de chave de partição. O artigo [Particionamento no Azure Cosmos DB](../cosmos-db/partitioning-overview.md) dá mais detalhes sobre a escolha de uma chave de partição.

Para contêineres Azure Cosmos DB fixos, o Stream Analytics não permite que o Stream Analytics não seja dimensionado ou eliminado depois que eles estejam cheios. Eles têm um limite superior de 10 GB e 10.000 RU/s de throughput. Para migrar os dados de um contêiner fixo para um contêiner ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), use a ferramenta de [migração](../cosmos-db/import-data.md) de dados ou a [biblioteca de feedde alterações](../cosmos-db/change-feed.md).

A capacidade de escrever em vários recipientes fixos está sendo depreciada. Não recomendamos isso para dimensionar seu trabalho no Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Throughput melhorado com nível de compatibilidade 1.2
Com o nível de compatibilidade 1.2, o Stream Analytics suporta a integração nativa para gravar em massa no Azure Cosmos DB. Essa integração permite escrever efetivamente para o Azure Cosmos DB, maximizando o throughput e lidando eficientemente com solicitações de estrangulamento. 

O mecanismo de escrita aprimorado está disponível um novo nível de compatibilidade devido a uma diferença no comportamento upsert. Com níveis antes de 1.2, o comportamento upsert é inserir ou mesclar o documento. Com 1.2, o comportamento upsert é modificado para inserir ou substituir o documento.

Com níveis antes de 1.2, o Stream Analytics usa um procedimento armazenado personalizado para aumentar o volume de documentos por chave de partição no Azure Cosmos DB. Lá, um lote é escrito como uma transação. Mesmo quando um único registro atinge um erro transitório (estrangulamento), todo o lote tem que ser repetido. Isso torna os cenários com um estrangulamento ainda razoável relativamente lento.

O exemplo a seguir mostra dois trabalhos idênticos do Stream Analytics lendo da mesma entrada do Azure Event Hubs. Ambos os trabalhos do Stream Analytics são [totalmente particionados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) com uma consulta de passagem e gravação em recipientes Azure Cosmos DB idênticos. As métricas à esquerda são do trabalho configurado com o nível de compatibilidade 1.0. As métricas à direita estão configuradas com 1.2. A chave de partição do contêiner Azure Cosmos DB é uma GUID única que vem do evento de entrada.

![Comparação das métricas do Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

A taxa de eventos recebida no Event Hubs é duas vezes maior do que os contêineres Azure Cosmos DB (20.000 RUs) estão configurados para receber, então é esperado o estrangulamento no Azure Cosmos DB. No entanto, o trabalho com 1,2 está consistentemente escrevendo em um rendimento mais alto (eventos de saída por minuto) e com uma utilização média menor de SU%. No seu ambiente, essa diferença dependerá de mais alguns fatores. Esses fatores incluem a escolha do formato do evento, tamanho do evento/mensagem de entrada, teclas de partição e consulta.

![Comparação de métricas do Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Com o 1.2, o Stream Analytics é mais inteligente em utilizar 100% do throughput disponível no Azure Cosmos DB com pouquíssimas resubmissões de estrangulamento ou limitação de taxas. Isso fornece uma melhor experiência para outras cargas de trabalho, como consultas em execução no contêiner ao mesmo tempo. Se você quiser ver como o Stream Analytics se dimensiona com o Azure Cosmos DB como uma pia para 1.000 a 10.000 mensagens por segundo, experimente [este projeto de amostra do Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

O throughput da saída Azure Cosmos DB é idêntico ao 1.0 e 1.1. *Recomendamos fortemente* que você use o nível de compatibilidade 1.2 no Stream Analytics com o Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Configurações do Azure Cosmos DB para saída JSON

O uso do Azure Cosmos DB como saída no Stream Analytics gera o seguinte prompt para informações.

![Campos de informação para um fluxo de saída Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Campo           | Descrição|
|-------------   | -------------|
|Alias de saída    | Um alias para se referir a esta saída na consulta do Stream Analytics.|
|Subscription    | A assinatura do Azure.|
|ID da Conta      | O nome ou o URI do ponto de extremidade da conta do Azure Cosmos DB.|
|Chave de conta     | A chave de acesso compartilhado da conta do Azure Cosmos DB.|
|Banco de dados        | O nome do banco de dados do Azure Cosmos DB.|
|Nome do contêiner | O nome do `MyContainer`contêiner, como . Um contêiner `MyContainer` chamado deve existir.  |
|ID do documento     | Opcional. O nome da coluna em eventos de saída usado como a chave exclusiva que inserir ou atualizar as operações que devem ser baseadas. Se você deixá-lo vazio, todos os eventos serão inseridos, sem opção de atualização.|

Depois de configurar a saída Azure Cosmos DB, você pode usá-la na consulta como alvo de uma [declaração INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Quando você está usando uma saída Azure Cosmos DB dessa forma, [uma chave de partição precisa ser definida explicitamente](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

O registro de saída deve conter uma coluna sensível a maiúsculas e minúsculas nomeada síncronas em homenagem à chave de partição no Azure Cosmos DB. Para obter uma maior paralelização, a declaração pode exigir uma [cláusula PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) que usa a mesma coluna.

Aqui está um exemplo de consulta:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Tratamento de erros e novas tentativas

Se uma falha passageira, indisponibilidade de serviço ou estrangulamento acontecer enquanto o Stream Analytics estiver enviando eventos para o Azure Cosmos DB, o Stream Analytics se reinespera indefinidamente para concluir a operação com sucesso. Mas ele não tenta repetições para as seguintes falhas:

- Não autorizado (código de erro HTTP 401)
- Não encontrado (código de erro HTTP 404)
- Proibido (código de erro HTTP 403)
- BadRequest (código de erro HTTP 400)
