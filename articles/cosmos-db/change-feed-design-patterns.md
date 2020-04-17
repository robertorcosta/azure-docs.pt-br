---
title: Alterar os padrões de design de feed no Azure Cosmos DB
description: Visão geral dos padrões comuns de design de feed de mudança
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450344"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Alterar os padrões de design de feed no Azure Cosmos DB

O feed de alterações Do Azure Cosmos DB permite o processamento eficiente de grandes conjuntos de dados com um alto volume de gravações. O feed de alterações também oferece uma alternativa para consultar todo um conjunto de dados para identificar o que foi alterado. Este documento se concentra em padrões comuns de design de feed de alterações, tradeoffs de design e limitações de alimentação de alteração.

O Azure Cosmos DB é adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é usar as alterações nos dados para disparar ações adicionais. Os exemplos de ações adicionais incluem:

* Disparar uma notificação ou uma chamada a uma API, quando um item é inserido ou atualizado.
* Processamento de fluxo em tempo real para IoT ou processamento de análise em tempo real em dados operacionais.
* Movimentação de dados, como sincronizar com um cache, um mecanismo de pesquisa, um data warehouse ou armazenamento a frio.

O feed de alterações no Azure Cosmos DB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões, conforme mostra a imagem a seguir:

![Usando o feed de alterações do Azure Cosmos DB para capacitar a análise em tempo real e cenários de computação orientada a eventos](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Computação de eventos e notificações

O feed de alterações do Azure Cosmos DB pode simplificar cenários que precisam desencadear uma notificação ou uma chamada para uma API com base em um determinado evento. Você pode usar a [Biblioteca de processos de feed de alteração](change-feed-processor.md) para verificar automaticamente seu contêiner para obter alterações e chamar uma API externa cada vez que houver uma gravação ou atualização.

Você também pode acionar seletivamente uma notificação ou enviar uma chamada para uma API com base em critérios específicos. Por exemplo, se você estiver lendo a partir do feed de alterações usando [funções do Azure,](change-feed-functions.md)você pode colocar lógica na função para enviar apenas uma notificação se um critério específico tiver sido atendido. Enquanto o código de função Do Zure seria executado durante cada gravação e atualização, a notificação só seria enviada se critérios específicos tivessem sido atendidos.

## <a name="real-time-stream-processing"></a>Processamento de fluxo em tempo real

O feed de alterações do Azure Cosmos DB pode ser usado para processamento de fluxo em tempo real para processamento de IoT ou análise em tempo real em dados operacionais.
Por exemplo, você pode receber e armazenar dados de eventos de dispositivos, sensores, infra-estrutura e aplicativos, e processar esses eventos em tempo real, usando [o Spark](../hdinsight/spark/apache-spark-overview.md). A imagem a seguir mostra como você pode implementar uma arquitetura lambda usando o Azure Cosmos DB via feed de alterações:

![Pipeline lambda baseado no Azure Cosmos DB para ingestão e consulta](./media/change-feed/lambda.png)

Em muitos casos, as implementações de processamento de fluxo recebem primeiro um grande volume de dados recebidos em uma fila temporária de mensagens, como o Azure Event Hub ou o Apache Kafka. O feed de mudançaé uma ótima alternativa devido à capacidade do Azure Cosmos DB de suportar uma alta taxa sustentada de ingestão de dados com latência garantida de baixa leitura e gravação. As vantagens do feed de alteração do Azure Cosmos DB em uma fila de mensagens incluem:

### <a name="data-persistence"></a>Persistência de dados

Os dados gravados no Azure Cosmos DB aparecerão no feed de alterações e serão retidos até serem excluídos. As filas de mensagens normalmente têm um período máximo de retenção. Por exemplo, [o Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) oferece uma retenção máxima de dados de 90 dias.

### <a name="querying-ability"></a>Capacidade de consulta

Além de ler a partir do feed de alteração de um contêiner Cosmos, você também pode executar consultas SQL sobre os dados armazenados no Azure Cosmos DB. O feed de alteração não é uma duplicação de dados já no contêiner, mas apenas um mecanismo diferente de leitura dos dados. Portanto, se você ler dados do feed de alteração, ele sempre será consistente com as consultas do mesmo contêiner Azure Cosmos DB.

### <a name="high-availability"></a>Alta disponibilidade

O Azure Cosmos DB oferece até 99,999% de disponibilidade de leitura e gravação. Ao contrário de muitas filas de mensagens, os dados do Azure Cosmos DB podem ser facilmente distribuídos e configurados globalmente com um [RTO (Recovery Time Objective)](consistency-levels-tradeoffs.md#rto) de zero.

Depois de processar itens no feed de alterações, você pode construir uma visualização materializada e persistir valores agregados de volta no Azure Cosmos DB. Se você estiver usando o Azure Cosmos DB para criar um jogo, poderá, por exemplo, usar o feed de alterações para implementar placares em tempo real de acordo com as pontuações dos jogos concluídos.

## <a name="data-movement"></a>Movimentação de dados

Você também pode ler a partir do feed de alterações para movimentação de dados em tempo real.

Por exemplo, o feed de alteração ajuda você a executar as seguintes tarefas de forma eficiente:

* Atualize um cache, índice de pesquisa ou data warehouse com dados armazenados no Azure Cosmos DB.

* Realizar migrações com tempo de inatividade zero para outra conta ou para outro contêiner do Azure Cosmos com uma chave de partição lógica diferente.

* Implemente um hierárquico e arquivamento de dados em nível de aplicativo. Por exemplo, você pode armazenar "dados quentes" no Azure Cosmos DB e envelhecer "dados frios" para outros sistemas de armazenamento, como [o Azure Blob Storage](../storage/common/storage-introduction.md).

Quando você tem que [desnormalizar dados através de partições e contêineres,](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)você pode ler a partir do feed de alterações do seu contêiner como uma fonte para essa replicação de dados. A replicação de dados em tempo real com o feed de alterações só pode garantir uma eventual consistência. Você pode [monitorar até que ponto o processador de alimentação de alterações fica para trás](how-to-use-change-feed-estimator.md) no processamento de alterações no seu contêiner Cosmos.

## <a name="event-sourcing"></a>Sourcing de eventos

O [padrão de sourcing de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) envolve o uso de uma loja somente para anexar para registrar a série completa de ações sobre esses dados. O feed de alterações do Azure Cosmos DB é uma ótima escolha como um armazenamento central de dados em arquiteturas de sourcing de eventos onde toda ingestão de dados é modelada como gravações (sem atualizações ou exclusões). Neste caso, cada gravação para o Azure Cosmos DB é um "evento" e você terá um registro completo de eventos passados no feed de alterações. Os usos típicos dos eventos publicados pela loja central de eventos são para manter vistas materializadas ou para integração com sistemas externos. Como não há limite de tempo para retenção no feed de alterações, você pode repetir todos os eventos passados lendo desde o início do feed de alterações do seu contêiner Cosmos.

Você pode ter [vários feeds de alteração os consumidores assinam o feed de mudança do mesmo contêiner](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Além da transmissão provisionada [do contêiner de locação,](change-feed-processor.md#components-of-the-change-feed-processor) não há custo para utilizar a alimentação de mudança. A alimentação de mudança está disponível em todos os recipientes, independentemente de ser utilizado.

O Azure Cosmos DB é um grande armazenamento de dados persistente somente para apêndices central no padrão de sourcing de eventos devido aos seus pontos fortes em escalabilidade horizontal e alta disponibilidade. Além disso, a biblioteca do processador de ração de alteração oferece uma garantia ["pelo menos uma vez",](change-feed-processor.md#error-handling) garantindo que você não perca o processamento de nenhum evento.

## <a name="current-limitations"></a>Limitações atuais

O feed de mudança tem limitações importantes que você deve entender. Embora os itens em um contêiner Cosmos sempre permaneçam no feed de alterações, o feed de alteração não é um registro de operação completo. Existem áreas importantes a serem consideradas ao projetar um aplicativo que utilize o feed de alteração.

### <a name="intermediate-updates"></a>Atualizações intermediárias

Apenas a alteração mais recente para um determinado item está incluída na alimentação de alteração. Ao processar alterações, você lerá a versão mais recente disponível do item. Se houver várias atualizações para o mesmo item em um curto período de tempo, é possível perder o processamento de atualizações intermediárias. Se você quiser acompanhar atualizações e ser capaz de reproduzir atualizações passadas para um item, recomendamos modelar essas atualizações como uma série de gravações em vez disso.

### <a name="deletes"></a>Deletes

O feed de alteração não captura exclusões. Se você excluir um item do seu recipiente, ele também será removido da alimentação de alteração. O método mais comum de lidar com isso é adicionar um marcador macio nos itens que estão sendo excluídos. Você pode adicionar uma propriedade chamada "excluída" e defini-la como "verdadeira" no momento da exclusão. Esta atualização do documento aparecerá no feed de alterações. Você pode definir um TTL neste item para que ele possa ser automaticamente excluído mais tarde.

### <a name="guaranteed-order"></a>Ordem garantida

Há ordem garantida no feed de alteração dentro de um valor de chave de partição, mas não em valores-chave de partição. Você deve selecionar uma chave de partição que lhe dê uma garantia de ordem significativa.

Por exemplo, considere um aplicativo de varejo usando o padrão de design de sourcing de eventos. Neste aplicativo, diferentes ações de usuário são cada "eventos" que são modelados como gravações para o Azure Cosmos DB. Imagine se algum exemplo de eventos ocorresse na seguinte sequência:

1. O cliente adiciona o Item A ao seu carrinho de compras
2. Cliente adiciona item B ao seu carrinho de compras
3. Cliente remove item A de seu carrinho de compras
4. O conteúdo do carrinho de compras e o conteúdo do carrinho de compras são enviados

Uma visão materializada do conteúdo atual do carrinho de compras é mantida para cada cliente. Este aplicativo deve garantir que esses eventos sejam processados na ordem em que ocorrem. Se, por exemplo, o checkout do carrinho fosse processado antes da remoção do Item A, é provável que o cliente tivesse enviado o Item A, ao contrário do item B desejado. Para garantir que esses quatro eventos sejam processados por ordem de ocorrência, eles devem se enquadrar no mesmo valor de chave de partição. Se você selecionar o **nome de usuário** (cada cliente tem um nome de usuário único) como a chave de partição, você pode garantir que esses eventos apareçam no feed de alterações na mesma ordem em que são gravados no Azure Cosmos DB.

## <a name="examples"></a>Exemplos

Aqui estão alguns exemplos de código de feed de mudança do mundo real que se estendem além do escopo das amostras fornecidas nos docs da Microsoft:

- [Introdução ao feed de mudança](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Caso de uso de IoT centrado em torno do feed de mudança](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso de uso de varejo centrado em torno do feed de mudança](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do feed de alterações](change-feed.md)
* [Opções de ler o feed de alterações](read-change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)