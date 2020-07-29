---
title: Otimizar o custo e RU/s para executar consultas no Azure Cosmos DB
description: Saiba como avaliar os encargos de unidade de solicitação para uma consulta e otimizar a consulta em termos de desempenho e custo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: e1c60542ec16ca19d26a77c1b9fb9676cf875e3d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318259"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Otimizar o custo de consulta no Azure Cosmos DB

O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados, incluindo consultas relacionais e hierárquicas que operam nos itens dentro de um contêiner. O custo associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários realizarem várias operações de bancos de dados a fim de atender a uma solicitação. Este artigo descreve como avaliar os encargos de unidade de solicitação para uma consulta e otimizar a consulta em termos de desempenho e custo.

As leituras em Azure Cosmos DB normalmente são ordenadas da mais rápida/mais eficiente para mais lenta/menos eficiente em termos de taxa de transferência da seguinte maneira:  

* Leituras de ponto (pesquisa de chave/valor em uma única ID de item e chave de partição).

* Consulta com uma cláusula de filtro em uma chave de partição única.

* Consulta sem uma cláusula de filtro de igualdade ou intervalo em qualquer propriedade.

* Consulta sem filtros.

Como as pesquisas de chave/valor na ID do item são o tipo mais eficiente de leitura, você deve verificar se a ID do item tem um valor significativo.

Consultas que leem dados de uma ou mais partições incorrem em latência mais altas e consomem mais unidades de solicitação. Como cada partição tem indexação automática para todas as propriedades, a consulta pode ser atendida com eficiência no índice. Você pode fazer consultas que usam várias partições mais rapidamente usando as opções de paralelismo. Para saber mais sobre particionamento e chaves de partição, consulte [Particionamento no Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Avaliar o encargo de unidades de solicitação para uma consulta

Depois que você tiver alguns dados armazenados em seus contêineres do Azure Cosmos, poderá usar o Data Explorer no portal do Azure para construir e executar suas consultas. Você também pode obter o custo das consultas usando o Data Explorer. Esse método lhe dará uma ideia dos encargos reais envolvidos nas consultas e as operações típicas às quais seu sistema dá suporte.

Você também pode obter o custo de consultas programaticamente usando os SDKs. Para medir a sobrecarga de operações como criar, atualizar ou excluir, inspecione o cabeçalho `x-ms-request-charge` ao usar a API REST. Se você estiver usando o .NET ou o SDK do Java, a `RequestCharge` propriedade será a propriedade equivalente para obter o encargo da solicitação e essa propriedade estará presente dentro de ResourceResponse ou FeedResponse.

```csharp
// Measure the performance (request units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Fatores que influenciam o encargo de unidades de solicitação para uma consulta

As unidades de solicitação para consultas dependem de uma série de fatores. Por exemplo, o número de itens do Azure Cosmos carregados/retornados, o número de pesquisas em relação ao índice, o tempo de compilação da consulta, etc. detalhes. O Azure Cosmos DB garante que a mesma consulta, quando executada nos mesmos dados, sempre consumirá o mesmo número de unidades de solicitação, mesmo com execuções repetidas. O perfil de consulta usando métricas de execução de consulta dá uma boa ideia de como as unidades de solicitação são gastas.  

Em alguns casos, você poderá ver uma sequência de 200 e 429 respostas, e unidades de solicitação de variável em uma execução paginada de consultas, porque as consultas serão executadas o mais rápido possível com base nas RUs disponíveis. Você poderá ver uma execução de consulta se desmembrar em várias páginas/viagens de ida e volta entre servidor e cliente. Por exemplo, 10.000 itens poderão ser retornados como várias páginas, cada uma cobrada com base no cálculo executado para a página. Quando você soma essas páginas, deve obter o mesmo número de RUs que receberia de toda a consulta.  

## <a name="metrics-for-troubleshooting"></a>Métricas para solução de problemas

O desempenho e a taxa de transferência consumidos pelas consultas e as UDFs (funções definidas pelo usuário) dependem principalmente do corpo da função. A maneira mais fácil de descobrir quanto tempo é gasto na execução da consulta nas UDFs e o número de RUs consumidas é habilitando as métricas de consulta. Se você usar o SDK do .NET, aqui estão as métricas de consulta de exemplo retornadas pelo SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Melhores práticas para otimizar consultas pelo custo 

Considere as seguintes melhores práticas ao otimizar consultas pelo custo:

* **Colocar vários tipos de entidade**

   Tente colocar vários tipos de entidade em um único contêiner ou na menor quantidade possível. Esse método gera benefícios não apenas em termos de preço, mas também em termos de transações e execução da consulta. As consultas têm como escopo um único contêiner, e as transações atômicas em vários registros por meio de procedimentos armazenados/gatilhos têm como escopo uma chave de partição em um único contêiner. A colocação de entidades no mesmo contêiner pode reduzir o número de viagens de ida e volta na rede para resolver relacionamentos entre registros. Assim, ela aumenta o desempenho de ponta a ponta, permite transações atômicas em vários registros em um conjunto de dados maior e, como resultado, reduz os custos. Se a colocação de vários tipos de entidade em um único contêiner (ou poucos) costuma ser difícil em seu cenário, talvez porque você esteja migrando um aplicativo existente e não deseje fazer alterações de código, considere provisionar a taxa de transferência no nível do banco de dados.  

* **Medir e ajustar para o uso mais baixo de unidades/segundo da solicitação**

   A complexidade de uma consulta afeta a quantidade de RUs (unidades de solicitação) consumida para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem. Todos esses fatores influenciam o custo das operações de consulta. 

   A carga de solicitação retornada no cabeçalho da solicitação indica o custo de uma determinada consulta. Por exemplo, se uma consulta retorna 1.000 itens de 1 KB, o custo da operação é 1.000. Assim, em um segundo, o servidor mantém apenas duas dessas solicitações antes de limitar as solicitações subsequentes. Para saber mais, consulte o artigo [Unidades de solicitação](request-units.md) e a calculadora de unidades de solicitação. 

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Como funcionam os preços do Azure Cosmos](how-pricing-works.md)
* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [Azure Cosmos DB capacidade reservada](cosmos-db-reserved-capacity.md)

