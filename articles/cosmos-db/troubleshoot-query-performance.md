---
title: Solucionar problemas de consulta ao usar o Azure Cosmos DB
description: Saiba como identificar, diagnosticar e solucionar problemas de consulta do Azure Cosmos DB SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 852ed8c49eda7f13542eb0bad63d84e1cf770e92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131372"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Solucionar problemas de consulta ao usar o Azure Cosmos DB

Este artigo percorre uma abordagem geral recomendada para solucionar problemas no Azure Cosmos DB. Embora você não deva considerar as etapas descritas neste artigo como uma defesa completa contra possíveis problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Você deve usar este artigo como um local de partida para solucionar consultas lentas ou caras na API do núcleo Azure Cosmos DB (SQL). Você também pode usar [registros de diagnóstico para](cosmosdb-monitor-resource-logs.md) identificar consultas que são lentas ou que consomem quantidades significativas de throughput.

Você pode categorizar amplamente otimizações de consulta no Azure Cosmos DB: 

- Otimizações que reduzem a carga da unidade de solicitação (RU) da consulta
- Otimizações que apenas reduzem a latência

Se você reduzir a carga RU de uma consulta, você quase certamente diminuirá a latência também.

Este artigo fornece exemplos que você pode recriar usando o conjunto de dados [nutricionais.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

## <a name="important"></a>Importante

- Para obter o melhor desempenho, siga as [dicas de Desempenho](performance-tips.md).
    > [!NOTE]
    > Para melhorar o desempenho, recomendamos o processamento do host do Windows de 64 bits. O SQL SDK inclui um ServiceInterop.dll nativo para analisar e otimizar consultas localmente. ServiceInterop.dll é suportado apenas na plataforma Windows x64. Para Linux e outras plataformas não suportadas onde o ServiceInterop.dll não está disponível, uma chamada de rede adicional será feita para o gateway para obter a consulta otimizada.
- As consultas do Azure Cosmos DB não suportam uma contagem mínima de itens.
    - O código deve lidar com qualquer tamanho de página, de zero à contagem máxima de itens.
    - O número de itens em uma página pode e mudará sem aviso prévio.
- Páginas vazias são esperadas para consultas e podem aparecer a qualquer momento.
    - Páginas vazias são expostas nos SDKs porque essa exposição permite mais oportunidades de cancelar uma consulta. Também deixa claro que o SDK está fazendo várias chamadas de rede.
    - Páginas vazias podem aparecer em cargas de trabalho existentes porque uma partição física é dividida no Azure Cosmos DB. A primeira partição terá zero resultados, o que causa a página vazia.
    - As páginas vazias são causadas pelo backend que antecipa uma consulta porque a consulta está demorando mais do que algum tempo fixo no backend para recuperar os documentos. Se o Azure Cosmos DB antecipar uma consulta, ele retornará um token de continuação que permitirá que a consulta continue.
- Certifique-se de drenar completamente a consulta. Olhe para as amostras de SDK, e use um `while` loop `FeedIterator.HasMoreResults` para drenar toda a consulta.

## <a name="get-query-metrics"></a>Obter métricas de consulta

Quando você otimiza uma consulta no Azure Cosmos DB, o primeiro passo é sempre [obter as métricas de consulta](profile-sql-api-query.md) para sua consulta. Essas métricas também estão disponíveis através do portal Azure:

[![Obtendo métricas](./media/troubleshoot-query-performance/obtain-query-metrics.png) de consulta](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Depois de obter as métricas de consulta, compare a contagem de documentos recuperados com a contagem de documentos de saída para sua consulta. Use esta comparação para identificar as seções relevantes a serem revisadas neste artigo.

A contagem de documentos recuperados é o número de documentos que a consulta precisava carregar. A Contagem de Documentos de Saída é o número de documentos necessários para os resultados da consulta. Se a contagem de documentos recuperados for significativamente maior do que a contagem de documentos de saída, houve pelo menos uma parte da sua consulta que não pôde usar o índice e precisava fazer uma varredura.

Consulte as seguintes seções para entender as otimizações de consulta relevantes para o seu cenário.

### <a name="querys-ru-charge-is-too-high"></a>A taxa de RU da Consulta é muito alta

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A contagem de documentos recuperados é significativamente maior do que a contagem de documentos de saída

- [Inclua os caminhos necessários na política de indexação.](#include-necessary-paths-in-the-indexing-policy)

- [Entenda quais funções do sistema usam o índice.](#understand-which-system-functions-use-the-index)

- [Modifique as consultas que tenham um filtro e uma cláusula ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Otimize as expressões JOIN usando uma subconsulta.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A contagem de documentos recuperados é aproximadamente igual à contagem de documentos de saída

- [Evite consultas de partição cruzada.](#avoid-cross-partition-queries)

- [Otimize consultas que tenham filtros em várias propriedades.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Modifique as consultas que tenham um filtro e uma cláusula ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A carga ru de Consulta é aceitável, mas a latência ainda é muito alta

- [Melhore a proximidade.](#improve-proximity)

- [Aumente a oferta de throughput provisionado.](#increase-provisioned-throughput)

- [Aumente a MaxConcurrency.](#increase-maxconcurrency)

- [Aumente MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas em que a contagem de documentos recuperados excede a contagem de documentos de saída

 A contagem de documentos recuperados é o número de documentos que a consulta precisava carregar. A Contagem de Documentos de Saída é o número de documentos necessários para os resultados da consulta. Se a contagem de documentos recuperados for significativamente maior do que a contagem de documentos de saída, houve pelo menos uma parte da sua consulta que não pôde usar o índice e precisava fazer uma varredura.

Aqui está um exemplo de consulta de varredura que não foi inteiramente atendida pelo índice:

Consulta:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Métricas de consulta:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

A contagem de documentos recuperados (60.951) é significativamente maior do que a contagem de documentos de saída (7), de modo que esta consulta precisava fazer uma varredura. Neste caso, a função do sistema [UPPER()](sql-query-upper.md) não usa o índice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Inclua os caminhos necessários na política de indexação

Sua política de indexação deve `WHERE` cobrir `ORDER BY` quaisquer propriedades `JOIN`incluídas em cláusulas, cláusulas e a maioria das funções do sistema. O caminho especificado na política de índice deve corresponder (sensível a maiúsculas e minúsculas) da propriedade nos documentos JSON.

Se você executar uma consulta simples no conjunto de dados [nutricionais,](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) você `WHERE` observará uma taxa de RU muito menor quando a propriedade na cláusula é indexada:

#### <a name="original"></a>Original

Consulta:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Política de indexação:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Ru taxa:** 409.51 RUs

#### <a name="optimized"></a>Otimizado

Política de indexação atualizada:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Ru taxa:** 2.98 RUs

Você pode adicionar propriedades à política de indexação a qualquer momento, sem efeito sobre a disponibilidade ou desempenho da gravação. Se você adicionar uma nova propriedade ao índice, as consultas que usam a propriedade usarão imediatamente o novo índice disponível. A consulta usará o novo índice enquanto ele está sendo construído. Assim, os resultados da consulta podem ser inconsistentes enquanto a reconstrução do índice está em andamento. Se uma nova propriedade for indexada, as consultas que usam apenas índices existentes não serão afetadas durante a reconstrução do índice. Você pode [acompanhar o progresso da transformação do índice](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Entenda quais funções do sistema usam o índice

Se uma expressão pode ser traduzida em uma variedade de valores de seqüência, ela pode usar o índice. Caso contrário, não pode.

Aqui está a lista de funções de string que podem usar o índice:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, mas somente se o primeiro num_expr for 0

A seguir estão algumas funções comuns do sistema que não usam o índice e devem carregar cada documento:

| **Função do sistema**                     | **Ideias para otimização**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Use o Azure Search para pesquisa de texto completo.                        |
| SUPERIOR/INFERIOR                             | Em vez de usar a função do sistema para normalizar dados para comparações, normalize o invólucro na inserção. Uma consulta ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` como ```SELECT * FROM c WHERE c.name = 'BOB'```se torna . |
| Funções matemáticas (não agregados) | Se você precisar calcular um valor com freqüência em sua consulta, considere armazenar o valor como uma propriedade em seu documento JSON. |

------

Outras partes da consulta ainda podem usar o índice, mesmo que as funções do sistema não.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Modificar consultas que tenham um filtro e uma cláusula ORDER BY

Embora as consultas que tenham `ORDER BY` um filtro e uma cláusula normalmente usem um índice de intervalo, elas serão mais eficientes se puderem ser servidas a partir de um índice composto. Além de modificar a política de indexação, você deve adicionar `ORDER BY` todas as propriedades no índice composto à cláusula. Essa alteração na consulta garantirá que ele use o índice composto.  Você pode observar o impacto executando uma consulta no conjunto de dados [nutricionais:](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

#### <a name="original"></a>Original

Consulta:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Política de indexação:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Ru taxa:** 44.28 RUs

#### <a name="optimized"></a>Otimizado

Consulta atualizada (inclui ambas as `ORDER BY` propriedades na cláusula):

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Política de indexação atualizada:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Ru taxa:** 8.86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Otimize expressões JOIN usando uma subconsulta
Subconsultas de vários valores podem otimizar `JOIN` expressões empurrando predicados após cada expressão selecionada, em vez de, depois de todas as junções cruzadas na `WHERE` cláusula.

Considere esta consulta:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Ru taxa:** 167.62 RUs

Para esta consulta, o índice corresponderá a qualquer documento que tenha uma tag com o nome "fórmula infantil", nutriçãoValor superior a 0 e quantidade de serviço maior que 1. A `JOIN` expressão aqui executará o produto cruzado de todos os itens de etiquetas, nutrientes e matrizes de porções para cada documento correspondente antes de qualquer filtro ser aplicado. Em `WHERE` seguida, a cláusula aplicará `<c, t, n, s>` o predicado do filtro em cada tupla.

Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, ele se expandirá para 1 x 10 x 10 x 10 (ou seja, 1.000) tuplas. O uso de subconsultas aqui pode ajudar a filtrar itens de matriz juntados antes de se juntar à próxima expressão.

Esta consulta é equivalente à anterior, mas usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru taxa:** 22.17 RUs

Suponha que apenas um item na matriz de tags corresponda ao filtro e que existem cinco itens para os nutrientes e matrizes de porções. As `JOIN` expressões se expandirão para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas em que a contagem de documentos recuperados é igual à contagem de documentos de saída

Se a contagem de documentos recuperados for aproximadamente igual à contagem de documentos de saída, a consulta não precisa digitalizar muitos documentos desnecessários. Para muitas consultas, como aquelas que usam a palavra-chave TOP, a contagem de documentos recuperados pode exceder a contagem de documentos de saída por 1. Não precisa se preocupar com isso.

### <a name="avoid-cross-partition-queries"></a>Evite consultas de partição cruzada

O Azure Cosmos DB usa [particionamento](partitioning-overview.md) para dimensionar contêineres individuais à medida que a Unidade de Solicitação e as necessidades de armazenamento de dados aumentam. Cada partição física tem um índice separado e independente. Se a sua consulta tiver um filtro de igualdade que corresponda à chave de partição do seu contêiner, você precisará verificar apenas o índice da partição relevante. Essa otimização reduz o número total de RUs que a consulta requer.

Se você tem um grande número de RUs provisionadas (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de aproximadamente 100 GB), você provavelmente tem um contêiner grande o suficiente para ver uma redução significativa nas taxas de RU consulta.

Por exemplo, se você criar um contêiner com a chave de partição foodGroup, as seguintes consultas precisarão verificar apenas uma única partição física:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Essas consultas também seriam otimizadas pela adição da chave de partição na consulta:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

As consultas que possuem filtros de intervalo na tecla de partição, ou que não tenham filtros na tecla de partição, precisarão verificar o índice de cada partição física para obter resultados:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Otimizar consultas que têm filtros em várias propriedades

Embora as consultas que tenham filtros em várias propriedades normalmente usem um índice de intervalo, elas serão mais eficientes se puderem ser servidas a partir de um índice composto. Para pequenas quantidades de dados, essa otimização não terá um impacto significativo. Pode ser útil, no entanto, para grandes quantidades de dados. Você só pode otimizar, no máximo, um filtro de não igualdade por índice composto. Se a sua consulta tiver vários filtros de não igualdade, escolha um deles que usará o índice composto. O resto continuará a usar índices de faixa. O filtro de não igualdade deve ser definido por último no índice composto. [Saiba mais sobre índices compostos](index-policy.md#composite-indexes).

Aqui estão alguns exemplos de consultas que poderiam ser otimizadas com um índice composto:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Aqui está o índice composto relevante:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência da consulta

Em muitos casos, a taxa ru pode ser aceitável quando a latência de consulta ainda é muito alta. As seções a seguir dão uma visão geral das dicas para reduzir a latência da consulta. Se você executar a mesma consulta várias vezes no mesmo conjunto de dados, ele terá a mesma carga RU todas as vezes. Mas a latência da consulta pode variar entre execuções de consulta.

### <a name="improve-proximity"></a>Melhorar a proximidade

As consultas que são executadas de uma região diferente da conta Azure Cosmos DB terão latência maior do que se fossem executadas dentro da mesma região. Por exemplo, se você estiver executando código no seu computador desktop, você deve esperar que a latência seja dezenas ou centenas de milissegundos mais alta (ou mais) do que se a consulta veio de uma máquina virtual dentro da mesma região do Azure como a Azure Cosmos DB. É simples [distribuir globalmente dados no Azure Cosmos DB](distribute-data-globally.md) para garantir que você possa aproximar seus dados do seu aplicativo.

### <a name="increase-provisioned-throughput"></a>Aumento do throughput provisionado

No Azure Cosmos DB, seu throughput provisionado é medido em Unidades de Solicitação (RUs). Imagine que você tem uma consulta que consome 5 RUs de throughput. Por exemplo, se você prover 1.000 RUs, você poderá executar essa consulta 200 vezes por segundo. Se você tentasse executar a consulta quando não havia throughput suficiente disponível, o Azure Cosmos DB retornaria um erro HTTP 429. Qualquer um dos SDKs atuais da API Core (SQL) irá automaticamente tentar essa consulta depois de esperar por um curto período de tempo. As solicitações estranguladas demoram mais, de modo que o aumento do throughput provisionado pode melhorar a latência da consulta. Você pode observar o [número total de solicitações estranguladas](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na lâmina **Métricas** do portal Azure.

### <a name="increase-maxconcurrency"></a>Aumentar a MaxConcurrency

Consultas paralelas funcionam consultando várias partições em paralelo. Mas os dados de uma coleção particionada individual são obtidos em série em relação à consulta. Assim, se você definir MaxConcurrency para o número de partições, você tem a melhor chance de alcançar a consulta mais performática, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não sabe o número de partições, você pode definir MaxConcurrency (ou MaxDegreesOfParallelism em versões SDK mais antigas) para um número alto. O sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumentar maxbufferedItemcount

As consultas são projetadas para pré-buscar resultados enquanto o lote atual de resultados está sendo processado pelo cliente. O pré-busca ajuda a melhorar a latência geral de uma consulta. A configuração maxbufferedItemCount limita o número de resultados pré-buscados. Se você definir esse valor para o número esperado de resultados retornados (ou um número maior), a consulta pode obter o maior benefício da pré-busca. Se você definir esse valor como -1, o sistema determinará automaticamente o número de itens a serem protegidos.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir para obter informações sobre como medir RUs por consulta, obter estatísticas de execução para ajustar suas consultas e muito mais:

* [Obtenha métricas de execução de consulta SQL usando o .NET SDK](profile-sql-api-query.md)
* [Ajustando o desempenho da consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Dicas de desempenho para o SDK do .NET](performance-tips.md)
