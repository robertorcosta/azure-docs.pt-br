---
title: Solucionar problemas de consulta ao usar o Azure Cosmos DB
description: Saiba como identificar, diagnosticar e solucionar problemas Azure Cosmos DB problemas de consulta SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: b3c6926f17e8378fd3b53bfd59a7c5ea8141adb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097227"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Solucionar problemas de consulta ao usar o Azure Cosmos DB

Este artigo percorre uma abordagem geral recomendada para a solução de problemas de consultas no Azure Cosmos DB. Embora você não deva considerar as etapas descritas neste artigo uma defesa completa contra possíveis problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Você deve usar este artigo como um local de início para solucionar problemas de consultas lentas ou dispendiosas na API do Azure Cosmos DB Core (SQL). Você também pode usar [logs de diagnóstico](cosmosdb-monitor-resource-logs.md) para identificar consultas que são lentas ou que consomem quantidades significativas de taxa de transferência.

Você pode categorizar amplamente as otimizações de consulta no Azure Cosmos DB:

- Otimizações que reduzem o encargo de RU (unidade de solicitação) da consulta
- Otimizações que apenas reduzem a latência

Se você reduzir a carga de RU de uma consulta, certamente diminuirá a latência também.

Este artigo fornece exemplos que você pode recriar usando o conjunto de [nutrição](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) .

## <a name="common-sdk-issues"></a>Problemas comuns do SDK

Antes de ler este guia, é útil considerar problemas comuns do SDK que não estão relacionados ao mecanismo de consulta.

- Para obter o melhor desempenho, siga estas [dicas de desempenho](performance-tips.md).
    > [!NOTE]
    > Para melhorar o desempenho, recomendamos o processamento de host do Windows de 64 bits. O SDK do SQL inclui um perinterop. dll nativo para analisar e otimizar as consultas localmente. O perinterop. dll tem suporte apenas na plataforma Windows x64. Para Linux e outras plataformas sem suporte em que o perinterop. dll não está disponível, uma chamada de rede adicional será feita ao gateway para obter a consulta otimizada.
- O SDK permite configurar um `MaxItemCount` para suas consultas, mas você não pode especificar uma contagem mínima de itens.
    - O `MaxItemCount`código deve lidar com qualquer tamanho de página, de zero a.
    - O número de itens em uma página sempre será menor ou igual ao especificado `MaxItemCount`. No entanto, `MaxItemCount` é estritamente um máximo e pode haver menos resultados do que esse valor.
- Às vezes, as consultas podem ter páginas vazias mesmo quando há resultados em uma página futura. Os motivos para isso podem ser:
    - O SDK pode estar fazendo várias chamadas de rede.
    - A consulta pode estar demorando um longo tempo para recuperar os documentos.
- Todas as consultas têm um token de continuação que permitirá que a consulta continue. Certifique-se de drenar a consulta completamente. Examine as amostras do SDK e use um `while` loop on `FeedIterator.HasMoreResults` para drenar toda a consulta.

## <a name="get-query-metrics"></a>Obter métricas de consulta

Quando você otimiza uma consulta no Azure Cosmos DB, a primeira etapa é sempre [obter as métricas de consulta](profile-sql-api-query.md) para sua consulta. Essas métricas também estão disponíveis por meio do portal do Azure. Depois de executar a consulta na Data Explorer, as métricas de consulta são visíveis ao lado da guia **resultados** :

[![Obtendo métricas](./media/troubleshoot-query-performance/obtain-query-metrics.png) de consulta](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Depois de obter as métricas de consulta, compare a **contagem de documentos recuperados** com a **contagem de documentos de saída** para a consulta. Use essa comparação para identificar as seções relevantes a serem examinadas neste artigo.

A **contagem de documentos recuperados** é o número de documentos que o mecanismo de consulta precisa carregar. A **contagem de documentos de saída** é o número de documentos que foram necessários para os resultados da consulta. Se a **contagem de documentos recuperados** for significativamente maior do que a **contagem de documentos de saída**, havia pelo menos uma parte da consulta que não pôde usar um índice e necessário para fazer uma verificação.

Consulte as seções a seguir para entender as otimizações de consulta relevantes para seu cenário.

### <a name="querys-ru-charge-is-too-high"></a>A cobrança de RU da consulta é muito alta

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A contagem de documentos recuperados é significativamente maior que a contagem de documentos de saída

- [Inclua os caminhos necessários na política de indexação.](#include-necessary-paths-in-the-indexing-policy)

- [Entenda quais funções do sistema usam o índice.](#understand-which-system-functions-use-the-index)

- [Entenda quais consultas de agregação usam o índice.](#understand-which-aggregate-queries-use-the-index)

- [Otimizar consultas que têm uma cláusula de filtro e de ORDENAção.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Otimizar expressões de junção usando uma subconsulta.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A contagem de documentos recuperados é aproximadamente igual à contagem de documentos de saída

- [Minimizar consultas entre partições.](#minimize-cross-partition-queries)

- [Otimizar consultas que têm filtros em várias propriedades.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Otimizar consultas que têm uma cláusula de filtro e de ORDENAção.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A cobrança de RU da consulta é aceitável, mas a latência ainda é muito alta

- [Melhorar a proximidade.](#improve-proximity)

- [Aumentar a taxa de transferência provisionada.](#increase-provisioned-throughput)

- [Aumentar MaxConcurrency.](#increase-maxconcurrency)

- [Aumentar MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas em que a contagem de documentos recuperados excede a contagem de documentos de saída

 A **contagem de documentos recuperados** é o número de documentos que o mecanismo de consulta precisa carregar. A **contagem de documentos de saída** é o número de documentos retornados pela consulta. Se a **contagem de documentos recuperados** for significativamente maior do que a **contagem de documentos de saída**, havia pelo menos uma parte da consulta que não pôde usar um índice e necessário para fazer uma verificação.

Veja um exemplo de consulta de verificação que não foi totalmente servida pelo índice:

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

A **contagem de documentos recuperados** (60.951) é significativamente maior que a **contagem de documentos de saída** (7), implicando que essa consulta resultou em uma verificação de documento. Nesse caso, a função de sistema [Upper ()](sql-query-upper.md) não usa um índice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Incluir caminhos necessários na política de indexação

Sua política de indexação deve abranger todas as `WHERE` Propriedades incluídas em `ORDER BY` cláusulas, `JOIN`cláusulas, e a maioria das funções do sistema. Os caminhos desejados especificados na política de índice devem corresponder às propriedades nos documentos JSON.

> [!NOTE]
> As propriedades em Azure Cosmos DB política de indexação diferenciam maiúsculas de minúsculas

Se você executar a consulta simples a seguir no conjunto de [nutrição](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) , verá uma cobrança de ru muito menor quando a propriedade na `WHERE` cláusula for indexada:

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
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

**Encargo de ru:** 409,51 RUs

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

**Encargo de ru:** 2,98 RUs

Você pode adicionar propriedades à política de indexação a qualquer momento, sem nenhum efeito na disponibilidade ou no desempenho da gravação. Se você adicionar uma nova propriedade ao índice, as consultas que usam a propriedade usarão imediatamente o novo índice disponível. A consulta usará o novo índice enquanto ele estiver sendo compilado. Portanto, os resultados da consulta podem ser inconsistentes enquanto a recompilação do índice está em andamento. Se uma nova propriedade for indexada, as consultas que usam apenas índices existentes não serão afetadas durante a recompilação do índice. Você pode [acompanhar o progresso da transformação índice](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Entender quais funções do sistema usam o índice

Se uma expressão puder ser convertida em um intervalo de valores de cadeia de caracteres, ela poderá usar o índice. Caso contrário, ele não pode.

Aqui está a lista de algumas funções de cadeia de caracteres comuns que podem usar o índice:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- Substring (str_expr, num_expr, num_expr) = str_expr, mas somente se a primeira num_expr for 0

Veja a seguir algumas funções comuns do sistema que não usam o índice e que devem carregar cada documento:

| **Função do sistema**                     | **Ideias para otimização**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Use Azure Search para pesquisa de texto completo.                        |
| SUPERIOR/INFERIOR                             | Em vez de usar a função do sistema para normalizar dados para comparações, Normalize o uso de maiúsculas e minúsculas após a inserção. Uma consulta como ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` se ```SELECT * FROM c WHERE c.name = 'BOB'```torna. |
| Funções matemáticas (não agregadas) | Se você precisar calcular um valor frequentemente em sua consulta, considere armazenar o valor como uma propriedade em seu documento JSON. |

------

Outras partes da consulta ainda podem usar o índice, mesmo que as funções do sistema não.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Entender quais consultas de agregação usam o índice

Na maioria dos casos, as funções de sistema agregadas no Azure Cosmos DB usarão o índice. No entanto, dependendo dos filtros ou cláusulas adicionais em uma consulta de agregação, o mecanismo de consulta pode ser necessário para carregar um grande número de documentos. Normalmente, o mecanismo de consulta aplicará primeiro os filtros de igualdade e de intervalo. Depois de aplicar esses filtros, o mecanismo de consulta pode avaliar filtros adicionais e recorrer ao carregamento de documentos restantes para calcular a agregação, se necessário.

Por exemplo, considerando essas duas consultas de exemplo, a consulta com um filtro de `CONTAINS` função de igualdade e de sistema geralmente será mais eficiente do que uma consulta `CONTAINS` com apenas um filtro de função do sistema. Isso ocorre porque o filtro de igualdade é aplicado primeiro e usa o índice antes que os documentos precisem ser carregados para `CONTAINS` o filtro mais caro.

Consulta com somente `CONTAINS` filtro-cobrança de ru maior:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Consulta com o filtro de igualdade `CONTAINS` e o custo de ru menor do filtro:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Aqui estão exemplos adicionais de consultas de agregação que não usarão totalmente o índice:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Consultas com funções do sistema que não usam o índice

Você deve consultar a [página da função do sistema](sql-query-system-functions.md) relevante para ver se ele usa o índice.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Agregar consultas com funções definidas pelo usuário (UDF)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Consultas com GROUP BY

A carga de consultas de RU `GROUP BY` com aumentará conforme a cardinalidade das propriedades na `GROUP BY` cláusula aumentar. Na consulta abaixo, por exemplo, a cobrança de RU da consulta aumentará conforme o número de descrições exclusivas aumentar.

A cobrança de RU de uma função de agregação com uma `GROUP BY` cláusula será maior do que a carga de ru de uma função de agregação sozinha. Neste exemplo, o mecanismo de consulta deve carregar todos os documentos que correspondem `c.foodGroup = "Sausages and Luncheon Meats"` ao filtro para que a carga de ru deva ser alta.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Se você planeja executar com frequência as mesmas consultas de agregação, pode ser mais eficiente criar uma exibição materializada em tempo real com o [Azure Cosmos DB o feed de alterações](change-feed.md) do que a execução de consultas individuais.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Otimizar consultas que têm um filtro e uma cláusula ORDER BY

Embora as consultas que têm um filtro e `ORDER BY` uma cláusula normalmente usem um índice de intervalo, elas serão mais eficientes se puderem ser servidas de um índice composto. Além de modificar a política de indexação, você deve adicionar todas as propriedades no índice composto à `ORDER BY` cláusula. Essa alteração na consulta garantirá que ela use o índice composto.  Você pode observar o impacto executando uma consulta no conjunto de [nutrição](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) :

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
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

**Encargo de ru:** 44,28 RUs

#### <a name="optimized"></a>Otimizado

Consulta atualizada (inclui ambas as propriedades na `ORDER BY` cláusula):

```sql
SELECT *
FROM c
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

**Encargo de ru:** 8,86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Otimizar expressões de junção usando uma subconsulta

Subconsultas de vários valores podem `JOIN` otimizar expressões enviando predicados após cada expressão Select-many em vez de todas as junções cruzadas `WHERE` na cláusula.

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

**Encargo de ru:** 167,62 RUs

Para essa consulta, o índice corresponderá a qualquer documento que tenha uma marca com `infant formula`o `nutritionValue` nome, maior que 0 `amount` e maior que 1. A `JOIN` expressão aqui executará o produto cruzado de todos os itens de marcas, nutrients e servirá matrizes para cada documento correspondente antes de qualquer filtro ser aplicado. Em `WHERE` seguida, a cláusula aplicará o predicado de filtro em cada `<c, t, n, s>` tupla.

Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, ele será expandido para tuplas de 1 x 10 x 10 x 10 (ou seja, 1.000). O uso de subconsultas aqui pode ajudar a filtrar itens de matriz Unidos antes de se juntar à próxima expressão.

Essa consulta é equivalente à anterior, mas usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Encargo de ru:** 22,17 RUs

Suponha que apenas um item na matriz de marcas corresponda ao filtro e que haja cinco itens para as matrizes nutrients e servindo. As `JOIN` expressões se expandirão para 1 x 1 x 5 x 5 = 25 itens, em vez de 1.000 itens na primeira consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas em que a contagem de documentos recuperados é igual à contagem de documentos de saída

Se a **contagem de documentos recuperados** for aproximadamente igual à **contagem de documentos de saída**, o mecanismo de consulta não terá que verificar muitos documentos desnecessários. Para muitas consultas, como as que usam a `TOP` palavra-chave, a **contagem de documentos recuperados** pode exceder a **contagem de documentos de saída** em 1. Você não precisa se preocupar com isso.

### <a name="minimize-cross-partition-queries"></a>Minimizar consultas entre partições

Azure Cosmos DB usa o [particionamento](partitioning-overview.md) para dimensionar contêineres individuais à medida que as necessidades de unidade de solicitação e armazenamento de dados aumentam. Cada partição física tem um índice separado e independente. Se sua consulta tiver um filtro de igualdade que corresponda à chave de partição do contêiner, você precisará verificar apenas o índice da partição relevante. Essa otimização reduz o número total de RUs que a consulta requer.

Se você tiver um grande número de RUs provisionadas (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de aproximadamente 100 GB), provavelmente terá um contêiner grande o suficiente para ver uma redução significativa nos encargos de consulta RU.

Por exemplo, se você criar um contêiner com a chave de partição de alimentos, as consultas a seguir precisarão verificar apenas uma única partição física:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

As consultas que têm `IN` um filtro com a chave de partição verificarão apenas as partições físicas relevantes e não serão "Fan-out":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

As consultas que têm filtros de intervalo na chave de partição ou que não têm filtros na chave de partição precisarão de "Fan-out" e verificar o índice de cada partição física para obter os resultados:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Otimizar consultas que têm filtros em várias propriedades

Embora as consultas que têm filtros em várias propriedades normalmente usem um índice de intervalo, elas serão mais eficientes se puderem ser servidas de um índice composto. Para pequenas quantidades de dados, essa otimização não terá um impacto significativo. No entanto, pode ser útil para grandes quantidades de dados. Você só pode otimizar, no máximo, um filtro de não igualdade por índice composto. Se sua consulta tiver vários filtros de não igualdade, escolha um deles que usará o índice composto. O restante continuará a usar índices de intervalo. O filtro de não igualdade deve ser definido por último no índice composto. [Saiba mais sobre índices compostos](index-policy.md#composite-indexes).

Aqui estão alguns exemplos de consultas que podem ser otimizadas com um índice composto:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Este é o índice composto relevante:

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

Em muitos casos, a carga de RU pode ser aceitável quando a latência de consulta ainda é muito alta. As seções a seguir fornecem uma visão geral das dicas para reduzir a latência da consulta. Se você executar a mesma consulta várias vezes no mesmo conjunto de mesmos, ela terá a mesma cobrança de RU a cada vez. Mas a latência de consulta pode variar entre as execuções de consulta.

### <a name="improve-proximity"></a>Melhorar a proximidade

As consultas executadas a partir de uma região diferente da conta de Azure Cosmos DB terão uma latência maior do que se fossem executadas dentro da mesma região. Por exemplo, se você estiver executando código em seu computador desktop, deverá esperar que a latência seja de dezenas ou centenas de milissegundos (ou mais) do que se a consulta vier de uma máquina virtual na mesma região do Azure que Azure Cosmos DB. É simples [distribuir dados de forma global em Azure Cosmos DB](distribute-data-globally.md) para garantir que você possa trazer seus dados mais perto do seu aplicativo.

### <a name="increase-provisioned-throughput"></a>Aumentar a taxa de transferência provisionada

Em Azure Cosmos DB, sua taxa de transferência provisionada é medida em unidades de solicitação (RUs). Imagine que você tenha uma consulta que consome 5 RUs de taxa de transferência. Por exemplo, se você provisionar 1.000 RUs, poderá executar essa consulta 200 vezes por segundo. Se você tentou executar a consulta quando não havia taxa de transferência suficiente disponível, Azure Cosmos DB retornaria um erro HTTP 429. Qualquer um dos SDKs da API do núcleo atual (SQL) repetirá automaticamente essa consulta depois de esperar por um curto período de tempo. As solicitações limitadas demoram mais, portanto, aumentar a taxa de transferência provisionada pode melhorar a latência da consulta. Você pode observar o [número total de solicitações limitadas](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na folha **métricas** do portal do Azure.

### <a name="increase-maxconcurrency"></a>Aumentar MaxConcurrency

As consultas paralelas funcionam consultando várias partições em paralelo. Mas os dados de uma coleção particionada individual são buscados em série em relação à consulta. Portanto, se você definir MaxConcurrency como o número de partições, terá a melhor chance de obter a consulta de melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá definir MaxConcurrency (ou MaxDegreesOfParallelism em versões mais antigas do SDK) para um número alto. O sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumentar MaxBufferedItemCount

As consultas são projetadas para buscar antecipadamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda a melhorar a latência geral de uma consulta. A definição de MaxBufferedItemCount limita o número de resultados previamente buscados. Se você definir esse valor como o número esperado de resultados retornados (ou um número mais alto), a consulta poderá obter o máximo benefício da busca prévia. Se você definir esse valor como-1, o sistema determinará automaticamente o número de itens para armazenar em buffer.

## <a name="next-steps"></a>Próximas etapas
Consulte os artigos a seguir para obter informações sobre como medir RUs por consulta, obter estatísticas de execução para ajustar suas consultas e muito mais:

* [Obter métricas de execução de consulta SQL usando o SDK do .NET](profile-sql-api-query.md)
* [Ajustando o desempenho da consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Dicas de desempenho para o SDK do .NET](performance-tips.md)
