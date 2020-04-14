---
title: Indexação no Azure Cosmos DB
description: Entenda como funciona a indexação no Azure Cosmos DB, diferentes tipos de índices como Range, Spatial, índices compostos suportados.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 684799ee12715c789910accf80aa5b4afec763d4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273232"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação no Azure Cosmos DB - Visão geral

O Azure Cosmos DB é um banco de dados agnóstico que permite iterar em sua aplicação sem ter que lidar com esquemas ou gerenciamento de índices. Por padrão, o Azure Cosmos DB indexa automaticamente todas as propriedades para todos os itens do [seu contêiner](databases-containers-items.md#azure-cosmos-containers) sem ter que definir nenhum esquema ou configurar índices secundários.

O objetivo deste artigo é explicar como o Azure Cosmos DB indexa os dados e como ele usa índices para melhorar o desempenho da consulta. Recomenda-se passar por esta seção antes de explorar como personalizar [políticas de indexação.](index-policy.md)

## <a name="from-items-to-trees"></a>De itens a árvores

Toda vez que um item é armazenado em um contêiner, seu conteúdo é projetado como um documento JSON e, em seguida, convertido em uma representação de árvore. O que isso significa é que cada propriedade desse item é representada como um nó em uma árvore. Um nó pseudo raiz é criado como um pai para todas as propriedades de primeiro nível do item. Os nódulos de folha contêm os valores escalares reais transportados por um item.

Como exemplo, considere este item:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Seria representado pela seguinte árvore:

![O item anterior representado como uma árvore](./media/index-overview/item-as-tree.png)

Observe como as matrizes são codificadas na árvore: cada entrada em uma matriz recebe um nó intermediário rotulado com o índice dessa entrada dentro da matriz (0, 1 etc.).

## <a name="from-trees-to-property-paths"></a>De árvores a caminhos de propriedades

A razão pela qual o Azure Cosmos DB transforma itens em árvores é porque permite que as propriedades sejam referenciadas por seus caminhos dentro dessas árvores. Para obter o caminho para uma propriedade, podemos atravessar a árvore do nó raiz até aquela propriedade, e concatenar os rótulos de cada nó atravessado.

Aqui estão os caminhos para cada propriedade a partir do item de exemplo descrito acima:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando um item é escrito, o Azure Cosmos DB indexa efetivamente o caminho de cada propriedade e seu valor correspondente.

## <a name="index-kinds"></a>Tipos de índice

Azure Cosmos DB atualmente suporta três tipos de índices.

### <a name="range-index"></a>Índice de faixa

**O** índice de intervalo é baseado em uma estrutura ordenada em forma de árvore. O tipo de índice de intervalo é usado para:

- Consultas de igualdade:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Jogo de igualdade em um elemento de matriz
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Consultas de intervalo:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (trabalha `>` `<`para, `>=` `<=`, `!=`, )

- Verificando a presença de um imóvel:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- As correspondências do prefixo de seqüência (CONTAINS keyword não aproveitarão o índice de intervalo):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`Consultas:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`Consultas:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Os índices de intervalo podem ser usados em valores escalares (string ou number).

### <a name="spatial-index"></a>Índice espacial

Os índices **espaciais** permitem consultas eficientes em objetos geoespaciais, tais como - pontos, linhas, polígonos e multipolígonos. Essas consultas usam palavras-chave ST_DISTANCE, ST_WITHIN ST_INTERSECTS. A seguir, alguns exemplos que usam o tipo de índice espacial:

- Consultas de distância geoespacial:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoespacial dentro de consultas:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Consultas de interseção geoespacial:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Os índices espaciais podem ser usados em objetos [GeoJSON](geospatial.md) formatados corretamente. Pontos, LineStrings, Polígonos e MultiPolifons são suportados atualmente.

### <a name="composite-indexes"></a>Índices compostos

**Índices compostos** aumentam a eficiência quando você está realizando operações em vários campos. O tipo de índice composto é usado para:

- `ORDER BY`consultas em várias propriedades:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Consultas com filtro `ORDER BY`e . Essas consultas podem utilizar um índice composto se `ORDER BY` a propriedade do filtro for adicionada à cláusula.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Consultas com um filtro em duas ou mais propriedades onde pelo menos uma propriedade é um filtro de igualdade

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Enquanto um predicado de filtro usar um do tipo índice, o mecanismo de consulta avaliará isso primeiro antes de digitalizar o resto. Por exemplo, se você tiver uma consulta SQL, como`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* A consulta acima primeiro filtrará para entradas onde primeiro Nome = "Andrew" usando o índice. Em seguida, passe todas as entradas firstName = "Andrew" através de um pipeline subseqüente para avaliar o predicado do filtro CONTAINS.

* Você pode acelerar as consultas e evitar varreduras completas de contêineres ao usar funções que não usam o índice (por exemplo, CONTÉM) adicionando predicados adicionais de filtro que usam o índice. A ordem das cláusulas do filtro não é importante. O mecanismo de consulta é vai descobrir quais predicados são mais seletivos e executar a consulta de acordo.


## <a name="querying-with-indexes"></a>Consultar com índices

Os caminhos extraídos ao indexar dados facilitam a visualização do índice ao processar uma consulta. Ao combinar `WHERE` a cláusula de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem ao predicado da consulta muito rapidamente.

Por exemplo, considere a `SELECT location FROM location IN company.locations WHERE location.country = 'France'`seguinte consulta: . O predicado de consulta (filtragem em itens, onde qualquer local tem "França" como seu país) corresponderia ao caminho destacado em vermelho abaixo:

![Combinando um caminho específico dentro de uma árvore](./media/index-overview/matching-path.png)

> [!NOTE]
> Uma `ORDER BY` cláusula que encomenda por um único imóvel *sempre* precisa de um índice de intervalo e falhará se o caminho que ele faz referência não tiver um. Da mesma `ORDER BY` forma, uma consulta que encomenda por várias propriedades *sempre* precisa de um índice composto.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
