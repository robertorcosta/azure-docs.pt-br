---
title: Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB
description: Use Azure Cosmos DB suporte do feed de alterações para controlar alterações em documentos, processamento baseado em evento como gatilhos e manter caches e sistemas analíticos atualizados
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984854"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Feed de alterações no Azure Cosmos DB

O suporte para feed de alterações no Azure Cosmos DB funciona ouvindo um contêiner do Azure Cosmos DB para quaisquer alterações. Ele gera a lista classificada de documentos que foram alterados na ordem em que eles foram modificados. As alterações são mantidas, podem ser processadas de maneira assíncrona e incremental e a saída pode ser distribuída em um ou mais consumidores para processamento paralelo.

Saiba mais sobre os [padrões de design do feed de alterações](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>SDKs do cliente e APIs com suporte

No momento, o recurso é compatível com os seguintes SDKs do cliente e APIs do Azure Cosmos DB.

| **Drivers do cliente** | **API do SQL** | **API do Azure Cosmos DB para Cassandra** | **API do Azure Cosmos DB para MongoDB** | **API do Gremlin**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Sim | Sim | Sim | Sim | Não |
|Java|Sim|Sim|Sim|Sim|Não|
|Python|Sim|Sim|Sim|Sim|Não|
|Nó/JS|Sim|Sim|Sim|Sim|Não|

## <a name="change-feed-and-different-operations"></a>Feed de alterações e operações diferentes

Hoje, você verá todas as inserções e atualizações no feed de alterações. Não é possível filtrar o feed de alterações para um tipo específico de operação. Uma alternativa possível é adicionar um "marcador flexível" no item para atualizações e filtrar com base no que ocorre durante o processamento de itens no feed de alterações.

Atualmente, o feed de alterações não registra exclusões. Semelhante ao exemplo anterior, você pode adicionar um marcador flexível nos itens que estão sendo excluídos. Por exemplo, você pode adicionar um atributo no item chamado "excluído" e defini-lo como "true" e definir um TTL no item, para que ele possa ser excluído automaticamente. Você pode ler o feed de alterações para itens históricos (a alteração mais recente correspondente ao item, não inclui as alterações intermediárias), por exemplo, itens que foram adicionados cinco anos atrás. Você pode ler o feed de alterações até a origem do seu contêiner, mas se um item for excluído, ele será removido do feed de alterações.

### <a name="sort-order-of-items-in-change-feed"></a>Ordem de classificação de itens no feed de alterações

Os itens do feed de alterações são organizados na ordem da hora de modificação. Essa ordem de classificação é garantida por chave de partição lógica.

### <a name="consistency-level"></a>Nível de consistência

Ao consumir o feed de alterações em um nível de consistência eventual, pode haver eventos duplicados entre operações de leitura do feed de alterações subsequentes (o último evento de uma operação de leitura aparece como o primeiro do próximo).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed de alterações em contas do Azure Cosmos em várias regiões

Em uma conta do Azure Cosmos em várias regiões, se uma região de gravação fizer failover, o feed de alterações funcionará em toda a operação de failover manual e será contíguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed de alterações e TTL (vida útil)

Se uma propriedade TTL (vida útil) for definida em um item como -1, o feed de alterações será mantido para sempre. Se os dados não forem excluídos, continuarão no feed de alterações.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed de alterações e _etag, _lsn ou _ts

O formato de _etag é interno e você não deve assumir uma dependência dele, porque ele pode ser alterado a qualquer momento. _ts é um carimbo de data/hora de modificação ou de criação. Você pode usar o _ts para comparação cronológica. _lsn é uma ID de lote que é adicionada somente para o feed de alterações; representa a ID da transação. Muitos itens podem ter a mesma _lsn. O ETag no FeedResponse é diferente do _etag que você vê no item. O _etag é um identificador interno e usado para controle de simultaneidade; ele informa sobre a versão do item, enquanto o ETag é usado para o sequenciamento do feed.

## <a name="working-with-change-feed"></a>Trabalhando com o feed de alterações

É possível trabalhar com o feed de alterações usando as seguintes opções:

* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Usando o feed de alterações com o processador do feed de alterações](change-feed-processor.md) 

O feed de alterações está disponível para cada chave de partição lógica dentro do contêiner e pode ser distribuído entre um ou mais clientes para processamento paralelo, conforme mostrado na imagem abaixo.

![Processamento distribuído do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Recursos do feed de alterações

* O feed de alterações é habilitado por padrão para todas as contas do Azure Cosmos.

* É possível usar a [taxa de transferência provisionada](request-units.md) para ler o feed de alterações, assim como qualquer outra operação do Azure Cosmos DB, em qualquer uma das regiões associadas ao banco de dados do Azure Cosmos.

* O feed de alterações inclui inserções e operações de atualização feitas em itens dentro do contêiner. É possível capturar exclusões definindo um sinalizador de "exclusão suave" dentro dos seus itens (por exemplo, documentos) no lugar de exclusões. Como alternativa, é possível definir um período de expiração finito para seus itens com a [funcionalidade TTL](time-to-live.md). Por exemplo, 24 horas e use o valor dessa propriedade para a captura ser excluída. Com essa solução, é necessário processar alterações em um intervalo de tempo menor do que o período de expiração da TTL.

* Cada alteração em um item aparece exatamente uma vez no feed de alterações, sendo que os clientes devem gerenciar a lógica do ponto de verificação. Se você quiser evitar a complexidade do gerenciamento de pontos de verificação, o processador do feed de alterações fornecerá o ponto de verificação automático e a semântica "pelo menos uma vez". Consulte [usar o feed de alterações com o processador do feed de alterações](change-feed-processor.md).

* Somente a alteração mais recente para um determinado item está incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.

* O feed de alterações é classificado por ordem de modificação em cada valor de chave de partição lógica. Não há nenhuma garantia de ordem entre os valores de chave de partição.

* As alterações podem ser sincronizadas de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual haja alterações disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógica de um contêiner do Azure Cosmos. Essa funcionalidade permite que alterações de grandes contêineres sejam processadas em paralelo por vários consumidores.

* Os aplicativos podem solicitar vários feeds de alteração no mesmo contêiner simultaneamente. ChangeFeedOptions.StartTime pode ser usado para fornecer um ponto de partida. Por exemplo, para localizar o token de continuação correspondente a uma determinada hora. O ContinuationToken, se especificado, tem precedência sobre os valores de StartTime e StartFromBeginning. A precisão de ChangeFeedOptions.StartTime é de aproximadamente 5 segundos.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade do feed de alterações é inserida como fluxo de alteração na API do MongoDB e consulta com predicado em API do Cassandra. Para saber mais sobre os detalhes de implementação para a API do MongoDB, consulte os [fluxos de alteração na API de Azure Cosmos DB para MongoDB](mongodb-change-streams.md).

O Apache Cassandra nativo fornece a captura de dados de alteração (CDC), um mecanismo para sinalizar tabelas específicas para arquivamento, bem como rejeitar gravações para essas tabelas assim que um tamanho configurável no disco para o log CDC for atingido. O recurso de feed de alterações na API Azure Cosmos DB para Cassandra aprimora a capacidade de consultar as alterações com o predicado por meio de CQL. Para saber mais sobre os detalhes da implementação, consulte [o feed de alterações na API de Azure Cosmos DB para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Próximas etapas

Agora, você pode prosseguir para saber mais sobre o feed de alterações nos seguintes artigos:

* [Opções de ler o feed de alterações](read-change-feed.md)
* [Usando feed de alterações com o Azure Functions](change-feed-functions.md)
* [Usando o processador de feed de alterações](change-feed-processor.md)
