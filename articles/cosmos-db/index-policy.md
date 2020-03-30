---
title: Políticas de indexação no Azure Cosmos DB
description: Saiba como configurar e alterar a política de indexação padrão para indexação automática e maior desempenho no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292054"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, cada contêiner tem uma política de indexação que dita como os itens do contêiner devem ser indexados. A política de indexação padrão para contêineres recém-criados indexa cada propriedade de cada item, aplicando índices de faixa para qualquer string ou número e índices espaciais para qualquer objeto GeoJSON do tipo Point. Isso permite que você obtenha um alto desempenho de consulta sem ter que pensar em indexação e gerenciamento de índices antecipadamente.

Em algumas situações, talvez você queira substituir esse comportamento automático para atender melhor às suas necessidades. Você pode personalizar a política de indexação de um contêiner definindo seu *modo de indexação*e incluir ou excluir *caminhos de propriedade*.

> [!NOTE]
> O método de atualização das políticas de indexação descritos neste artigo só se aplica à API SQL (Core) do Azure Cosmos DB.

## <a name="indexing-mode"></a>Modo de indexação

O Azure Cosmos DB suporta dois modos de indexação:

- **Consistente**: O índice é atualizado sincronizadamente à medida que você cria, atualiza ou exclui itens. Isso significa que a consistência das consultas de leitura será a [consistência configurada para a conta](consistency-levels.md).
- **Nenhuma**: A indexação está desativada no recipiente. Isso é comumente usado quando um contêiner é usado como uma loja de valor-chave puro sem a necessidade de índices secundários. Também pode ser usado para melhorar o desempenho das operações em massa. Depois que as operações em massa forem concluídas, o modo de índice pode ser definido como Consistente e, em seguida, monitorado usando o [IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) até concluir.

> [!NOTE]
> O Azure Cosmos DB também suporta um modo de indexação Lazy. A indexação preguiçosa realiza atualizações para o índice em um nível de prioridade muito menor quando o motor não está fazendo nenhum outro trabalho. Isso pode resultar em resultados de consulta **inconsistentes ou incompletos.** Se você planeja consultar um contêiner Cosmos, você não deve selecionar indexação preguiçosa.

Por padrão, a política `automatic`de indexação é definida como . É alcançado definindo `automatic` a propriedade na política `true`de indexação para . A configuração `true` desta propriedade permite que o Azure CosmosDB indexe automaticamente os documentos à medida que são escritos.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Incluindo e excluindo caminhos de propriedade

Uma política de indexação personalizada pode especificar caminhos de propriedade que estão explicitamente incluídos ou excluídos da indexação. Ao otimizar o número de caminhos indexados, você pode diminuir a quantidade de armazenamento usada pelo seu contêiner e melhorar a latência das operações de gravação. Esses caminhos são definidos [seguindo o método descrito na seção visão geral de indexação](index-overview.md#from-trees-to-property-paths) com as seguintes adições:

- um caminho que leva a um valor escalar (string ou número) termina com`/?`
- elementos de uma matriz são `/[]` abordados juntos `/0` `/1` através da notação (em vez de, etc.)
- o `/*` curinga pode ser usado para combinar com quaisquer elementos abaixo do nó

Tomando o mesmo exemplo novamente:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- o `headquarters`caminho `employees` é`/headquarters/employees/?`

- o `locations` `country` caminho é '`/locations/[]/country/?`

- o caminho para `headquarters` qualquer coisa sob é`/headquarters/*`

Por exemplo, poderíamos `/headquarters/employees/?` incluir o caminho. Esse caminho garantiria que indexássemos a propriedade dos funcionários, mas não indexaríamos JSON aninhado adicional dentro desta propriedade.

## <a name="includeexclude-strategy"></a>Incluir/excluir estratégia

Qualquer política de indexação deve `/*` incluir o caminho raiz como um caminho incluído ou excluído.

- Inclua o caminho raiz para excluir seletivamente caminhos que não precisam ser indexados. Esta é a abordagem recomendada, pois permite que o Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.
- Exclua o caminho raiz para incluir seletivamente caminhos que precisam ser indexados.

- Para caminhos com caracteres regulares que incluem: caracteres alfanuméricos e _ (sublinhado), você não precisa escapar da seqüência de caminhos em torno de aspas duplas (por exemplo, "/caminho/?"). Para caminhos com outros caracteres especiais, você precisa escapar da seqüência de caminhos em torno de aspas duplas (por exemplo, "/\"path-abc\"/?"). Se você espera personagens especiais em seu caminho, você pode escapar de todos os caminhos por segurança. Funcionalmente não faz diferença se você escapar de cada caminho Vs apenas aqueles que têm personagens especiais.

- A propriedade `_etag` do sistema é excluída da indexação por padrão, a menos que a marca ção seja adicionada ao caminho incluído para indexação.

- Se o modo de indexação estiver `id` definido `_ts` como **consistente,** as propriedades do sistema serão automaticamente indexadas.

Ao incluir e excluir caminhos, você pode encontrar os seguintes atributos:

- `kind`pode ser `range` `hash`ou . A funcionalidade do índice de intervalo fornece toda a funcionalidade de um índice de hash, por isso recomendamos usar um índice de intervalo.

- `precision`é um número definido no nível de índice para caminhos incluídos. Um valor `-1` de indica máxima precisão. Recomendamos sempre definir `-1`esse valor para .

- `dataType`pode ser `String` `Number`ou . Isso indica os tipos de propriedades JSON que serão indexadas.

Quando não especificadas, essas propriedades terão os seguintes valores padrão:

| **Nome da propriedade**     | **Valor Padrão** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` e `Number` |

Consulte [esta seção](how-to-manage-indexing-policy.md#indexing-policy-examples) para indexar exemplos de políticas para incluir e excluir caminhos.

## <a name="spatial-indexes"></a>Índices espaciais

Quando você define um caminho espacial na política de ```type``` indexação, você deve definir qual índice deve ser aplicado a esse caminho. Os tipos possíveis de índices espaciais incluem:

* Point

* Polygon

* MultiPolygon

* LineString

O Azure Cosmos DB, por padrão, não criará nenhum índice espacial. Se você quiser usar funções espaciais sql incorporadas, você deve criar um índice espacial nas propriedades necessárias. Consulte [esta seção](geospatial.md) para obter exemplos de política de indexação para adicionar índices espaciais.

## <a name="composite-indexes"></a>Índices compostos

Consultas que possuem `ORDER BY` uma cláusula com duas ou mais propriedades requerem um índice composto. Você também pode definir um índice composto para melhorar o desempenho de muitas consultas de igualdade e alcance. Por padrão, nenhum índice composto é definido, então você deve [adicionar índices compostos](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) conforme necessário.

Ao definir um índice composto, você especifica:

- Dois ou mais caminhos de propriedade. A seqüência em que os caminhos de propriedade são definidos importa.

- A ordem (ascendente ou descendente).

> [!NOTE]
> Quando você adiciona um índice composto, a consulta utilizará os índices de intervalo existentes até que a nova adição de índice composto esteja concluída. Portanto, quando você adiciona um índice composto, você pode não observar imediatamente melhorias de desempenho. É possível acompanhar o progresso da transformação do índice [utilizando um dos SDKs](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>ORDEM POR consultas em várias propriedades:

As seguintes considerações são usadas ao usar `ORDER BY` índices compostos para consultas com uma cláusula com duas ou mais propriedades:

- Se os caminhos de índice composto não `ORDER BY` corresponderem à seqüência das propriedades na cláusula, então o índice composto não poderá suportar a consulta.

- A ordem dos caminhos de índice composto (ascendente `order` ou `ORDER BY` descendente) também deve corresponder à da cláusula.

- O índice composto também `ORDER BY` suporta uma cláusula com a ordem oposta em todos os caminhos.

Considere o seguinte exemplo em que um índice composto é definido no nome das propriedades, idade e _ts:

| **Índice Composto**     | **Consulta `ORDER BY` de amostra**      | **Suportado pelo Índice Composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

Você deve personalizar sua política de indexação `ORDER BY` para que possa servir todas as consultas necessárias.

### <a name="queries-with-filters-on-multiple-properties"></a>Consultas com filtros em várias propriedades

Se uma consulta tiver filtros em duas ou mais propriedades, pode ser útil criar um índice composto para essas propriedades.

Por exemplo, considere a seguinte consulta que tenha um filtro de igualdade em duas propriedades:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Esta consulta será mais eficiente, levando menos tempo e consumindo menos RU's, se for capaz de alavancar um índice composto em (nome ASC, idade ASC).

Consultas com filtros de faixa também podem ser otimizadas com um índice composto. No entanto, a consulta só pode ter um filtro de alcance único. Os filtros `>` `<`de `<=` `>=`faixa `!=`incluem , , , e . O filtro de intervalo deve ser definido por último no índice composto.

Considere a seguinte consulta com filtros de igualdade e alcance:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Esta consulta será mais eficiente com um índice composto em (nome ASC, idade ASC). No entanto, a consulta não utilizaria um índice composto em (idade ASC, nome ASC) porque os filtros de igualdade devem ser definidos primeiro no índice composto.

As seguintes considerações são usadas ao criar índices compostos para consultas com filtros em múltiplas propriedades

- As propriedades no filtro da consulta devem coincidir com as do índice composto. Se uma propriedade estiver no índice composto, mas não estiver incluída na consulta como filtro, a consulta não utilizará o índice composto.
- Se uma consulta tiver propriedades adicionais no filtro que não foram definidas em um índice composto, então uma combinação de índices compostos e de intervalo será usada para avaliar a consulta. Isso exigirá menos RU's do que exclusivamente usando índices de intervalo.
- Se uma propriedade tiver`>`um `<` `<=`filtro `>=`de `!=`faixa ( , , ou ), então esta propriedade deve ser definida por último no índice composto. Se uma consulta tiver mais de um filtro de alcance, ela não utilizará o índice composto.
- Ao criar um índice composto para otimizar consultas `ORDER` com vários filtros, o índice composto não terá impacto sobre os resultados. Essa propriedade é opcional.
- Se você não definir um índice composto para uma consulta com filtros em várias propriedades, a consulta ainda terá sucesso. No entanto, o custo RU da consulta pode ser reduzido com um índice composto.

Considere os seguintes exemplos em que um índice composto é definido no nome das propriedades, idade e carimbo de data e hora:

| **Índice Composto**     | **Consulta de amostra**      | **Suportado pelo Índice Composto?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Consultas com um filtro, bem como uma cláusula ORDER BY

Se uma consulta filtrar uma ou mais propriedades e tiver propriedades diferentes na cláusula ORDER BY, `ORDER BY` pode ser útil adicionar as propriedades no filtro à cláusula.

Por exemplo, adicionando as propriedades no filtro à cláusula ORDER BY, a seguinte consulta poderia ser reescrita para aproveitar um índice composto:

Consulta usando índice de intervalo:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Consulta usando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

O mesmo padrão e otimizações de consulta podem ser generalizados para consultas com vários filtros de igualdade:

Consulta usando índice de intervalo:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Consulta usando índice composto:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

As seguintes considerações são usadas ao criar índices compostos para otimizar uma consulta com um filtro e `ORDER BY` uma cláusula:

* Se a consulta filtrar as propriedades, estas `ORDER BY` devem ser incluídas primeiro na cláusula.
* Se você não definir um índice composto em uma consulta com `ORDER BY` um filtro em uma propriedade e uma cláusula separada usando uma propriedade diferente, a consulta ainda terá sucesso. No entanto, o custo RU da consulta pode ser reduzido com `ORDER BY` um índice composto, particularmente se a propriedade na cláusula tiver uma alta cardinalidade.
* Todas as considerações para `ORDER BY` criar índices compostos para consultas com múltiplas propriedades, bem como consultas com filtros em várias propriedades ainda se aplicam.


| **Índice Composto**                      | **Consulta `ORDER BY` de amostra**                                  | **Suportado pelo Índice Composto?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Modificando a política de indexação

A política de indexação de um contêiner pode ser atualizada a qualquer momento [usando o portal Azure ou um dos SDKs suportados](how-to-manage-indexing-policy.md). Uma atualização da política de indexação desencadeia uma transformação do antigo índice para o novo, que é realizado on-line e no local (para que nenhum espaço de armazenamento adicional seja consumido durante a operação). O índice da velha política é eficientemente transformado para a nova política sem afetar a disponibilidade de gravação ou o throughput provisionado no contêiner. A transformação do índice é uma operação assíncrona, e o tempo necessário para ser concluído depende do throughput provisionado, do número de itens e do seu tamanho.

> [!NOTE]
> Ao adicionar um intervalo ou índice espacial, as consultas podem não retornar todos os resultados correspondentes e o farão sem retornar quaisquer erros. Isso significa que os resultados da consulta podem não ser consistentes até que a transformação do índice seja concluída. É possível acompanhar o progresso da transformação do índice [utilizando um dos SDKs](how-to-manage-indexing-policy.md).

Se o modo da nova política de indexação for definido como Consistente, nenhuma outra mudança de política de indexação poderá ser aplicada enquanto a transformação do índice estiver em andamento. Uma transformação de índice em execução pode ser cancelada definindo o modo de política de indexação para Nenhum (que irá imediatamente diminuir o índice).

## <a name="indexing-policies-and-ttl"></a>Políticas de indexação e TTL

O [recurso Tempo-para-Viver (TTL)](time-to-live.md) requer que a indexação esteja ativa no contêiner em que está ativada. Isso significa que:

- não é possível ativar o TTL em um recipiente onde o modo de indexação está definido como Nenhum,
- não é possível definir o modo de indexação para Nenhum em um recipiente onde o TTL está ativado.

Para cenários em que nenhum caminho de propriedade precisa ser indexado, mas o TTL é necessário, você pode usar uma política de indexação com:

- um modo de indexação definido como Consistente, e
- nenhum caminho incluído, e
- `/*`como o único caminho excluído.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos artigos a seguir:

- [Visão geral de indexação](index-overview.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
