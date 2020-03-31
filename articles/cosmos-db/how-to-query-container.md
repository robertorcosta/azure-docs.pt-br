---
title: Consultar contêineres no Azure Cosmos DB
description: Saiba como consultar contêineres no Azure Cosmos DB usando consultas de partição e partição cruzada
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131390"
---
# <a name="query-an-azure-cosmos-container"></a>Consultar um contêiner do Azure Cosmos

Este artigo explica como consultar um contêiner (coleção, grafo ou tabela) no Azure Cosmos DB. Em particular, ele cobre como as consultas de partição e partição cruzada funcionam no Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta na partição

Quando você consulta dados de contêineres, se a consulta tiver um filtro de chave de partição especificado, o Azure Cosmos DB otimizará automaticamente a consulta. Ele encaminha a consulta para as [partições físicas correspondentes](partition-data.md#physical-partitions) aos valores-chave de partição especificados no filtro.

Por exemplo, considere a consulta abaixo com `DeviceId`um filtro de igualdade ligado . Se executarmos esta consulta em um `DeviceId`contêiner particionado, esta consulta será filtrada para uma única partição física.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Como no exemplo anterior, esta consulta também será filtrada para uma única partição. Adicionar o filtro `Location` adicional não altera isso:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Aqui está uma consulta que tem um filtro de alcance na tecla de partição e não será escopo para uma única partição física. Para ser uma consulta na partição, a consulta deve ter um filtro de igualdade que inclua a chave de partição:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Consulta entre partições

A consulta a seguir não tem um filtro`DeviceId`na tecla de partição (). Portanto, ele deve se espalhar para todas as partições físicas onde é executado contra o índice de cada partição:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Cada partição física tem seu próprio índice. Portanto, quando você executa uma consulta de partição cruzada em um contêiner, você está efetivamente executando uma consulta *por* partição física. O Azure Cosmos DB agregará automaticamente os resultados em diferentes partições físicas.

Os índices em diferentes partições físicas são independentes um do outro. Não há índice global no Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Consulta entre partições em paralelo

Os SDKs do Azure Cosmos DB 1.9.0 e versões superiores dão suporte a opções de execução de consultas paralelas. Consultas entre partições em paralelo permitem que você execute consultas entre partições de baixa latência.

Você pode gerenciar a execução de consulta paralela ajustando os seguintes parâmetros:

- **MaxConcurrency**: Define o número máximo de conexões de rede simultâneas às partições do contêiner. Se você definir `-1`esta propriedade para , o SDK gerencia o grau de paralelismo. Se `MaxConcurrency` o `0`conjunto for , há uma única conexão de rede com as partições do contêiner.

- **MaxBufferedItemCount**: negocia a latência da consulta em comparação com a utilização de memória do lado do cliente. Se a opção for omitida ou definida como -1, o SDK gerenciará o número de itens no buffer durante a execução de consultas paralelas.

Devido à capacidade do Azure Cosmos DB de paraleleterizar consultas de partição cruzada, a latência de consulta geralmente será dimensionada bem como o sistema adiciona [partições físicas](partition-data.md#physical-partitions). No entanto, a carga de RU aumentará significativamente à medida que o número total de partições físicas aumentar.

Quando você executa uma consulta de partição cruzada, você está essencialmente fazendo uma consulta separada por partição física individual. Embora as consultas de partição cruzada usem o índice, se disponível, elas ainda não são tão eficientes quanto as consultas de partição.

## <a name="useful-example"></a>Exemplo útil

Aqui está uma analogia para entender melhor as consultas de partição cruzada:

Vamos imaginar que você é um motorista de entrega que tem que entregar pacotes para diferentes complexos de apartamentos. Cada conjunto de apartamentos tem uma lista no local que tem todos os números da unidade do morador. Podemos comparar cada complexo de apartamentos com uma partição física e cada lista com o índice da partição física.

Podemos comparar consultas de partição e partição cruzada usando este exemplo:

### <a name="in-partition-query"></a>Consulta na partição

Se o motorista de entrega conhece o complexo de apartamentos correto (partição física), então ele pode dirigir imediatamente para o prédio correto. O motorista pode verificar a lista do complexo de apartamentos dos números da unidade do residente (o índice) e entregar rapidamente os pacotes apropriados. Neste caso, o motorista não perde tempo ou esforço dirigindo até um complexo de apartamentos para verificar se algum destinatário do pacote mora lá.

### <a name="cross-partition-query-fan-out"></a>Consulta de partição cruzada (fan-out)

Se o motorista de entrega não souber o complexo de apartamentos correto (partição física), ele precisará dirigir até cada prédio de apartamentos e verificar a lista com todos os números da unidade do morador (o índice). Assim que chegarem a cada complexo de apartamentos, eles ainda poderão usar a lista dos endereços de cada morador. No entanto, eles precisarão verificar a lista de todos os complexos de apartamentos, se algum destinatário do pacote mora lá ou não. É assim que as consultas de partição cruzada funcionam. Embora eles possam usar o índice (não precisa bater em cada porta), eles devem verificar separadamente o índice para cada partição física.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Consulta de partição cruzada (escopo para apenas algumas partições físicas)

Se o motorista de entrega sabe que todos os destinatários do pacote vivem dentro de alguns poucos complexos de apartamentos, eles não precisarão dirigir até cada um deles. Enquanto dirigir para alguns complexos de apartamentos ainda exigirá mais trabalho do que visitar apenas um único edifício, o motorista de entrega ainda economiza tempo e esforço significativos. Se uma consulta tiver a chave de `IN` partição em seu filtro com a palavra-chave, ela só verificará os índices de partição física relevantes para dados.

## <a name="avoiding-cross-partition-queries"></a>Evitando consultas de partição cruzada

Para a maioria dos contêineres, é inevitável que você tenha algumas consultas de partição cruzada. Ter algumas consultas de partição cruzada é ok! Quase todas as operações de consulta são suportadas através de partições (tanto teclas de partição lógica quanto partições físicas). O Azure Cosmos DB também tem muitas otimizações no mecanismo de consulta e SDKs do cliente para paraleleterizar a execução de consultas através de partições físicas.

Para a maioria dos cenários de leitura pesada, recomendamos simplesmente selecionar a propriedade mais comum em seus filtros de consulta. Você também deve certificar-se de que sua chave de partição adere a outras [práticas recomendadas de seleção de chave de partição](partitioning-overview.md#choose-partitionkey).

Evitar consultas de partição cruzada normalmente só importa com recipientes grandes. Você é cobrado um mínimo de cerca de 2,5 RU's cada vez que você verifica o índice de uma partição física para obter resultados, mesmo se nenhum item na partição física corresponder ao filtro da consulta. Como tal, se você tiver apenas uma (ou apenas algumas) partições físicas, as consultas de partição cruzada não consumirão significativamente mais RU's do que consultas de partição.

O número de partições físicas está vinculado à quantidade de RU provisionados. Cada partição física permite até 10.000 RU's provisionados e pode armazenar até 50 GB de dados. O Azure Cosmos DB gerenciará automaticamente partições físicas para você. O número de partições físicas no recipiente depende do seu armazenamento provisionado e consumido.

Você deve tentar evitar consultas de partição cruzada se sua carga de trabalho atender aos critérios abaixo:
- Você planeja ter mais de 30.000 RU's provisionados
- Você planeja armazenar mais de 100 GB de dados

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre o particionamento no Azure Cosmos DB:

- [Particionamento no BD Cosmos do Azure](partitioning-overview.md)
- [Chaves de partição sintética no Azure Cosmos DB](synthetic-partition-keys.md)
